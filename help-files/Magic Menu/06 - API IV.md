## Magic Menu API IV - Misc.

If you want to allow for explicitly creating a new menu item, you can evaluate the **[magic-menu.command.create]**
event yourself, at which poin the _"create new menu item"_ window is displayed, illustrated below.

https://phosphorusfive.files.wordpress.com/2018/03/magic-menu-create-new-command-screenshot.png

You can optionally pass in a default phrase/word/sentence as **[\_arg]**. In addition to a default phrase, you
can supply the following optional arguments.

* __[pos]__ - Parent command ID
* __[pos-is-phrase-id]__ - If true, then __[pos]__ argument is phrase ID and not command ID
* __[hyperlambda]__ - Default Hyperlambda to use
* __[force-parent]__ - If true, will force usage of specified __[pos]__ as parent, and not allow for creating a global command
* __[global]__ - Default global value for item
* __[private]__ - Default private value for item
* __[global-id]__ - Default Global ID to use for the item

**Notice** - Since most query events in the Magic Menu happens to return the _"phrase ID"_ and not the _"command ID"_, it is
highly likely that you'd most of the time want to provide `pos-is-phrase-id:bool:true` as you evaluate the
above Active Event, if you choose to supply a **[pos]** argument.

### Localization

In addition to that you can explicitly choose to supply a language as you speak and capture input, you also
have some _"standard phrases"_, such as the default greeting, etc. These phrases can be found inside of your
Magic Menu modules folder, in the _"/configuration/"_ folder. By default, only a handful of localization files
and phrases exists, such as for standard English, Australian English, and Norwegian - With only a handful of
greetings and similar phrases. However, feel free to expand to these, and localize these
phrases as you wish. These phrases can be used in your own logic by wrapping square brackets around whatever
you want for the Magic Menu to utter for you. Below is an example.

```hyperlambda-snippet
/*
 * Starting the Magic Menu and uttering a "core phrase".
 */
magic-menu.start:bool:false
magic-menu.listen:[Talk to me]
  voice:nb-NO
```

You can also combine multiple _"localized core phrases"_ with your own sentences, such as the following
illustrates.

```hyperlambda-snippet
/*
 * Starting the Magic Menu and uttering a "core phrase".
 */
magic-menu.start:bool:false
magic-menu.listen:Jeg heter Nora. [Talk to me]. Jeg vil gjerne høre hva du har å si. [Hello]
  voice:nb-NO
```

And of course, you can create such phrases as you see fit, and reference these in your own vocabulary,
as you see fit - By creating such phrases and storing them in your localization files in the _"/configuration/"_
folder.

#### Explicitly localizing a string

The event that actually does the heavy lifting in regards to localizing your text is called
**[magic-menu.language.get-localized-phrase]**. You can of course explicitly invoke this event, with a
square bracket reference from your language file, such as e.g. _"[Hello]"_, to get the localized version
of that sentence for whatever language your're looking to transform it into. Below is an example.

```hyperlambda-snippet
/*
 * Returning the localized phrase for [Hello] in Norwegian.
 */
magic-menu.language.get-localized-phrase:[Hello]
  voice:nb-NO
micro.windows.info:x:/-?value
```

Notice, the **[magic-menu.language.get-localized-phrase]** event can only handle a single localized reference.
If you wish, you can use the **[magic-menu.unwrap-phrase]** event, to unwrap entire sentences, containing
multiple localized references instead. Below is an example.

```hyperlambda-snippet
/*
 * Unwrapping entire sentence.
 */
magic-menu.unwrap-phrase:Jeg heter Nora. [Talk to me]. Jeg vil gjerne høre hva du har å si. [Hello]
  voice:nb-NO
micro.windows.info:x:/-?value
```

#### Localization files' structure

Your localization files' structure is probably important to understand, if you want to create your own
localized phrases or sentences. The language files are semantically named _"lang-MAJOR-MINOR.hl"_, and they can be found
in your _"/configuration/"_ folder. MAJOR being major language code, such as e.g. _"en"_ for English.
MINOR being minor language code, such as e.g. _"AU"_ for Australian. If you try to retrieve something for
the language code of _"en-AU"_ for instance, it will check to see if the language file _"lang-en-AU.hl"_ exists,
and if it does, it will see if your phrase exists within this file. If the file doesn't exist, or the file
does not contain whatever you're looking for, it will check the _"lang-en.hl"_ file. The default language
file the engine will use is _"lang-en.hl"_.

This implies that your localization efforts becomes a _"tree"_, where you can choose to only override strings
that are different for Australian language in your _"lang-en-AU.hl"_ file, while keeping all commonalities in
your _"lang-en.hl"_ file.

The default language file of _"lang-en.hl"_ will be used, if no localization file or value exists for your phrase -
And if not even this file exists or contains a localized version of your string, it will return the string as is,
without square brackets. This implies that it's probably smart to use the English sentence or phrase as your
reference, as you create your own localization entities - Since if your string cannot be localized in any ways,
the engine will resort to using whatever is in between your square brackets. Below is an example.

```hyperlambda-snippet
/*
 * This value does not exist in any language files.
 */
magic-menu.language.get-localized-phrase:[This doesn't exist]
micro.windows.info:x:/-?value
```

**Notice**, for reasons that'll become apparent further down in this documentation, you cannot use periods (.)
in your localized resource references.

### Inline event invocations

In addition to creating localized phrases, you can also invoke events, inline as a part of your text. This is done
by simply making sure your square brackets contains at least one dot (.). Below is an example.

```hyperlambda-snippet
/*
 * Creating some dummy Active Event.
 */
create-event:gaia.samples.foo
  return:My name is {0}
    :x:/../*/_arg?value

/*
 * Starting the Magic Menu and uttering an "event phrase".
 */
magic-menu.start:bool:false
magic-menu.listen:"Hello. [gaia.samples.foo:Phosphorus Five]"
```

Such inline event invocations are declared by making sure you add one (.) inside of your square brackets,
at which point the event is expected to be found on the left side of any (optional) colon, and
its **[\_arg]** argument on the right hand side of any colon. In the above example
the `[gaia.samples.foo:Phosphorus Five]` parts will be substituted with _"My name is Phosphorus Five_",
before uttering the text. This allows you to implement some fairly nifty tricks, by for instance creating
events that somehow returns something you want the Magic Menu to utter. Below is an example of creating
an event that translates everything it is given into the specified **[voice]** language, for then to
return that to caller, and consuming it in your speech.

```hyperlambda-snippet
/*
 * Creating an event that translates everything it's given
 * into specified [voice], and returns the results to caller.
 */
create-event:gaia.samples.translate
  eval-x:x:/+/*
  micro.google.translate:x:/../*/_arg?value
    dest-lang:x:/../*/voice?value
  return:x:/-?value

/*
 * Starting the Magic Menu and uttering an "event phrase".
 */
magic-menu.start:bool:false
magic-menu.listen:"[gaia.samples.translate:Hello, I am a computer]"
  voice:nb-NO
```

The reasons why the above works, is because all arguments passed into **[magic-menu.listen]**, or its
speak counterpart for that matter, will be added to all invocations of all Active Events you're referencing
inside of your square brackets. This implies that our **[gaia.samples.translate]** event, will be invoked
with the **[voice]** argument passed into **[magic-menu.listen]** in our above example. You could of course
use the full API of the Magic Menu inside such Active Events, and for instance check the current language of
the user, before translating, and for that matter cache such translations in for instance some database, etc.
Below is an example that illustrates how arguments are passed into your Active Event references, by adding
a dummy argument, which is not needed for the speak event, but still passed into your own event as your
square bracket event is invoked.

```hyperlambda-snippet
/*
 * Creating some dummy Active Event.
 */
create-event:gaia.samples.foo
  return:My name is {0}, and I am {1}
    :x:/../*/_arg?value
    :x:/../*/coolnes?value

/*
 * Starting the Magic Menu and uttering an "event phrase".
 */
magic-menu.start:bool:false
magic-menu.listen:"Hello. [gaia.samples.foo:Phosphorus Five]"

  /*
   * This argument will be ignored by the Magic Menu itself, but still
   * passed into your [gaia.samples.foo] event, as your text is being built
   * and parsed by the main engine.
   */
  coolnes:very cool
```

**Notice**, using the above to invoke Google Translate is simply an example, and you could use such Active Event
references any ways you see fit - As long as your events somehow are able to return something that the Magic
Menu is capable of actually speaking.

