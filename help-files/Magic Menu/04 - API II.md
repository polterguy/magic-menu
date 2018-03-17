## Magic Menu API II

In addition to the API events previously documented, the Magic Menu contains a whole range of additional events,
that allows you to somehow control its behavior, or query its state. Since the user can explicitly choose to
turn OFF speech recognition and utterance, querying the menu to figure out if this setting is turned on becomes
important. In addition, you can use the API events to explicitly turn on or off both utterance and recognition.

**Notice**, most of these events are simply _"convenience wrappers"_ around the **[p5.auth.my-settings.xxx]**
events. These settings again, are stored on a per user basis, implying all settings for the Magic Menu are
implemented on a _"per user level"_ in the _"auth.hl"_ file.

* __[magic-menu.speech.is-enabled]__ - Returns true if speech is enabled
* __[magic-menu.speech.turn-on]__ - Turns on speech recognition and utterance
* __[magic-menu.speech.turn-off]__ - Turns off speech recognition and utterance

Below is an example of how to turn OFF both speech recognition and utterance.

```hyperlambda-snippet
/*
 * Turning OFF speech, before starting the menu.
 */
magic-menu.speech.turn-off

/*
 * Starting Magic Menu.
 *
 * At this point, since speech is turned OFF, the Magic Menu
 * becomes a "simple navigation menu", and does not try to neither
 * speak, nor capture speech input.
 */
magic-menu.start
```

Since the Magic Menu will by default display whatever it is supposed to speak in
its _"information tooltip widget"_ if speech is turned off, it is still for most practical concerns
equally usable, since it provides feedback as text. The end user can still explicitly choose to turn
ON speech again, by clicking the microphone widget in his Magic Menu.

**Notice**, if what the menu is supposed to speak is a long sentence, the sentence will be clipped, and
you'll have to hover your mouse above the _"info tooltip widget"_ to see the whole sentence. This is chosen
to avoid having the menu fill more than its available space in your browser window.

### Accessing and changing your settings

The Magic Menu has several Active Events which allows you to retrieve and modify its settings for the
currently logged in user. Below is a list of these events.

* __[magic-menu.settings.get]__ - Returns the __[\_arg]__ setting back to caller for the currently logged in user (pass in e.g. _"voice"_)
* __[magic-menu.settings.set]__ - Sets the __[\_arg]__ setting to the specified __[value]__. Pass in e.g. _"voice"_ and `value:Alex`
* __[magic-menu.settings.get-language]__ - Returns the language settings, including the voice, semantically broken down into its distinct sections
* __[magic-menu.settings.set-language]__ - Sets the language, or to be more accurately the **[voice]** setting, which are actually overlapping settings

Some of the above events are convenience events, and they to some extent overlap in functionality. However,
to provide a more intuitive vocabulary when consuming the Magic Menu, I've chosen to keep them around as
_"convenience events"_. Below is a snippet illustrating usage of the getter events from our above list.

```hyperlambda-snippet
/*
 * Retreiving misc. settings from Magic Menu
 * for the currently logged in user.
 */
magic-menu.settings.get:voice
magic-menu.settings.get-language

/*
 * Displaying a modal window with the result.
 */
lambda2hyper:x:/../*(/~magic-menu)
eval-x:x:/+/**/innerValue
create-widgets
  micro.widgets.modal
    widgets
      pre
        innerValue:x:/@lambda2hyper?value
```

**Notice**, if you haven't changed your settings explicitly, the above **[magic-menu.settings.get]**
will actually **return nothing**. This event optionally takes a **[default]** argument, which will be returned
if there doesn't exist any value for the setting you're querying it for. This may sound like unnecessary, until
your realise that you actually can significantly simplify usage in your own code, since this event won't return
anything, unless there explicitly exists a value for the setting you're querying it for - Unless you provide a
**[default]** value.

The **[magic-menu.settings.get-language]** event will use a **[default]** argument being `Daniel,en-GB`, which
implies that this is the default voice and language code for the Magic Menu.

### Explicitly handling a menu item

In our previous documentation file, we had a lot of examples where we completely bypassed the internals
of the Magic Menu, and handled the speak utterance ourselves. Sometimes you want to handle the recognized text
yourself, to provide some sort of _"override"_ - And unless the user speaks something you recognize, you want to
evaluate the default menu matching event. This is easily achieved by explicitly invoking **[magic-menu.command.handle]**.
This event requires an **[\_arg]** argument, or an **[id]** argument, being either some phrase, or the
database ID to a command.

Below is an example of usage.

```hyperlambda-snippet
/*
 * Starting Magic Menu.
 */
magic-menu.start:bool:false

/*
 * Listens for "yes", and if user says anything else, the
 * default implementation will kick in.
 */
magic-menu.listen:Speak yes or some menu item
  onfinish
    if:x:/../*/text?value
      =:yes

      /*
       * User spoke "yes".
       */
      magic-menu.speak:Yes is the answer
        onfinish
          magic-menu.quit

    else

      /*
       * User spoke anything BUT "yes".
       */
      magic-menu.command.handle:x:/../*/text?value
```

If you want to, you can also pass in an **[id]** argument, instead of an **[\_arg]** argument - Which is expected
to be the database ID of the menu command object, which will directly evaluate that menu item's Hyperlambda.
This is in fact what occurs when you click a menu item in the Magic Menu, instead of choosing to use speech
input. This allows you to create a visual wrapper, displaying menu items, allowing the user to click
the menu item instead of using utterance. At which point you could if you wanted to, create your own visual
wrapper entirely around the Magic Menu, and simply use it as a database for commands. Below is an example.

```hyperlambda-snippet
/*
 * Notice, we'll have to start the Magic Menu before we can interact with it.
 *
 * Making sure we start it without initiating listening loop.
 */
magic-menu.start:bool:false
  show-options:bool:false

/*
 * Listing available commands.
 */
magic-menu.grammar-position.list-options

/*
 * Creating a list item for each command.
 */
for-each:x:/@magic-menu.grammar-position.list-options/*
  eval-x:x:/+/*/*/*/*/*(/innerValue|/onclick/*/.menu-item)
  add:x:/../*/create-widgets/*/micro.widgets.modal/*/widgets/*/ul/*/widgets
    src
      li
        widgets
          a
            href:#
            innerValue:x:/@_dp/#?value
            onclick
              .menu-item:x:/@_dp/#?name
              eval-x:x:/+/*
              magic-menu.command.handle
                id:x:/@.menu-item?value

/*
 * Creating a modal window, displaying available Menu items.
 */
create-widgets
  micro.widgets.modal
    widgets
      ul
        widgets
```

### Querying the Magic Menu

In our above Hyperlambda we used the **[magic-menu.grammar-position.list-options]** Active Event. This event
allows you to query the Magic Menu for items, and is actually quite powerful. It takes the following arguments.

* __[limit]__ - Maximum number of items to return
* __[offset]__ - Offset of where to begin returning items
* __[filter]__ - Search filter

This is in fact the event that is used to display menu items in the GUI of your Magic Menu, and it allows you
to implement paging, search, etc as you query the Magic Menu for its items. Below is an example, that will return
the first 50 items at the _"root level"_.

```hyperlambda-snippet
/*
 * Listing first 50 menu items.
 */
magic-menu.grammar-position.list-options
  limit:50

/*
 * Displaying results in a modal window.
 */
create-widgets
  micro.widgets.modal
    widgets
      pre
        innerValue:x:/@magic-menu.grammar-position.list-options
```

### Explicitly evaluating a menu item

You can also explicitly evaluate a menu item by its global ID. Combining this with your own GUI, you can
(almost) entirely create your own GUI. Consider the example from above, where we created a list of anchor
elements to evaluate our menu items, and combining it with the **[magic-menu.command.evaluate]** event, to
explicitly evaluate items instead. You still need to actually start the Magic Menu though, but you can start
it minimized, without displaying its options, or initiating the listening loop.

```hyperlambda-snippet
/*
 * Notice, we'll have to start the Magic Menu before we can interact with it.
 *
 * Making sure we start it without initiating listening loop.
 */
magic-menu.start:bool:false
  show-options:bool:false

/*
 * Listing available commands.
 */
magic-menu.grammar-position.list-options

/*
 * Creating a list item for each command.
 */
for-each:x:/@magic-menu.grammar-position.list-options/*
  eval-x:x:/+/*/*/*/*/*(/innerValue|/onclick/*/.global-id)
  add:x:/../*/create-widgets/*/micro.widgets.modal/*/widgets/*/ul/*/widgets
    src
      li
        widgets
          a
            href:#
            innerValue:x:/@_dp/#?value
            onclick
              .global-id:x:/@_dp/#/*/global-id?value
              magic-menu.command.evaluate:x:/@.global-id?value

/*
 * Creating a modal window, displaying available Menu items.
 */
create-widgets
  micro.widgets.modal
    widgets
      ul
        widgets
```

