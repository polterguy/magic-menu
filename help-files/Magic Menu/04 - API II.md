## Magic Menu API II

In addition to the API events previously documented, the Magic Menu contains a whole range of additional events,
that allows you to somehow control its behavior, or query its state. Since the user can explicitly choose to
turn OFF speech recognition and utterance, querying the menu to figure out if this setting is turned on becomes
important. In addition, you can use the API events to explicitly turn on or off both utterance and recognition.

**Notice**, most of these events are simply _"convenience wrappers"_ around the **[p5.auth.my-settings.xxx]**
events. These settings again, are stored on a per user basis, implying all settings for the Magic Menu are
implemented on a _"per user level"_ in the _"auth.hl"_ file.

* __[magic-menu.speech.is-enabled]__ - Returns true if speech is enabled
* __[magic-menu.speech.turn-on]__ - Turns _ON_ speech recognition and utterance
* __[magic-menu.speech.turn-off]__ - Turns _OFF_ speech recognition and utterance

Below is an example of how to turn off both speech recognition and utterance before starting the Magic Menu.

```hyperlambda-snippet
/*
 * Turning OFF speech, before starting the menu.
 */
magic-menu.speech.turn-off

/*
 * Starting the Magic Menu.
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

### Displaying a custom text

The Magic Menu has an API event called **[magic-menu.set-text]** which allows you to change the text it
displays in its _"information widget"_. You can use this as you see fit, to give the user some hints about
what he or she can do. The default implementation for this is to display whatever it recognize, and speaks
though - So if you want to customize this text, you'll have to invoke the event _after_ you've started your
listening loop, or having it speak something. Below is an example.

```hyperlambda-snippet
/*
 * Starting Magic Menu.
 */
magic-menu.start:bool:false
magic-menu.listen:Hello there
magic-menu.set-text:Yo dude!
```

**Notice**, the text widget's content will be clipped, and you'll have to hover your mouse above the
_"info tooltip widget"_ to see the whole sentence, if you supply a very long piece of text. This is chosen
to avoid having the menu fill more than its available space in your browser window.

**Hint**, you can use **[magic-menu.get-text]** to retrieve the text currently displayed.

### Accessing and changing your settings

The Magic Menu has several Active Events which allows you to retrieve and modify its settings for the
currently logged in user. Below is a list of these events.

* __[magic-menu.settings.get]__ - Returns the (optionally supplied) __[\_arg]__ setting back to caller for the currently logged in user (pass in e.g. _"voice"_)
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

Also notice that if you want to return all Magic Menu settings, you can invoke the above **[magic-menu.settings.get]**
event without any **[\_arg]** argument, at which point all settings will be returned to caller. The latter does
not allow you to provide a **[default]** segment though.

The **[magic-menu.settings.get-language]** event will in fact internally use the **[magic-menu.settings.get]**
event, with a **[default]** argument being `Daniel,en-GB`, which implies that this is the default voice and
language code for the Magic Menu.

### Explicitly handling a menu item

In our previous documentation file, we had a lot of examples where we completely bypassed the internals
of the Magic Menu, and handled the speak utterance ourselves. Sometimes you want to handle the recognized text
yourself, to provide some sort of _"override"_ - And unless the user speaks something you recognize, you want to
evaluate the default menu matching event. This is easily achieved by explicitly invoking **[magic-menu.command.evaluate]**.
This event requires an **[\_arg]** argument, or an **[id]** argument, being either some phrase, or the
database ID to a phrase/synonym. The **[id]** argument can also optionally be supplied as the command's global
database ID.

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
      magic-menu.command.evaluate:x:/../*/text?value
```

Below is an example of evaluating the **[magic-menu.command.evaluate]** event with a global ID instead of a phrase.

```hyperlambda-snippet
/*
 * Starting Magic Menu.
 */
magic-menu.start:bool:false

/*
 * Starts listening, speaking something first.
 */
magic-menu.listen:Speak to me
  onfinish

    /*
     * Speaking something, and evaluating Hyperlambda command.
     */
    magic-menu.speak:All roads leads to Hyperlambda
      onfinish
        magic-menu.command.evaluate
          id:gaia.help.hyperlambda
```

The above arguably becomes the equivalent of polymorphistically overriding the core implementation, invoking
base implementation, unless it's something you explicitly know how to handle in your application.

If you want to, you can also pass in an **[id]** argument, instead of an **[\_arg]** argument - Which is expected
to be the database ID of the menu phrase object, or the global id of the command object, which will directly
evaluate that menu item's Hyperlambda. This is in fact what occurs when you click a menu item in the Magic Menu,
instead of choosing to use speech input. This allows you to create a visual wrapper, displaying menu items,
allowing the user to click the menu item instead of using utterance. At which point you could if you wanted to,
create your own visual wrapper entirely around the Magic Menu, and simply use it as a database for commands.
Below is an example.

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
              magic-menu.command.evaluate
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

