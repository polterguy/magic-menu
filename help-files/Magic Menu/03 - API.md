## Magic Menu API

The Magic Menu has a rich API which you can interact with. The most important Active Events are probably
the ones necessary to start and stop the Magic Menu, speak some phrase, and initiate speech recognition,
which you can find below.

* __[magic-menu.start]__ - Starts Magic Menu listening loop, optionally pass in "false" to only start menu, without greeting user and initiating listening loop
* __[magic-menu.speak]__ - Speaks something, optionally pass in **[voice]**
* __[magic-menu.listen]__ - Initiates the listening loop, and optionally speaks something before listening is initiated, optionally pass in **[\_arg]**, **[voice]** and **[lang]**
* __[magic-menu.quit]__ - Quits the Magic Menu

Below is an example of its most basic usage, which simply starts the menu, greets the user, and captures input
in a _"never ending loop"_, until the user somehow quits the menu. Hint, you can utter _"Goodbye"_ to quit the
menu.

```hyperlambda-snippet
/*
 * Simply starts the Magic Menu.
 */
magic-menu.start
```

If you start the Magic Menu using the above Hyperlambda, it will only recognize phrases, sentences, and words
that you have already taught it to recognize. And if you utter something it doesn't understand, you can teach
it new commands, assuming you're logged in as _"root"_.

### Customizing your input/output loop

The **[magic-menu.speak]** event, optionally takes a **[voice]** argument, being either a named voice according
to your browser's installed speech synthesis languages, or a language code such as `es-AR`, or a
combination of both. In addition, it requires you to (obviously) pass in an **[\_arg]** argument, being the
phrase or word(s) you want it to speak for you.

The **[magic-menu.listen]** event optionally takes a **[lang]** and a **[voice]** argument, being a language
code (e.g. `es-AR` for Spanish) used for capturing speech input, and a voice used for speaking. In addition,
the listen event also optionally takes an **[\_arg]** argument, which allows you to have it speak something,
before listening is initiated. Obviously, the **[voice]** argument only makes any sense if you also pass in
an **[\_arg]** argument.

Both events optionally takes an **[onfinish]** lambda callback, which is evaluated when speaking is done,
or speech has been captured. Below is an example of usage of the **[magic-menu.speak]**.

```hyperlambda-snippet
/*
 * Notice, we'll have to start the Magic Menu before we can interact with it.
 *
 * Making sure we start it without initiating listening loop.
 *
 * Without the :bool:false argument, the Magic Menu would greet the user,
 * and automatically capture speech input afterwards.
 * However, since we want to control what it says, and not actually capture
 * any input, we'll need to invoke it with a "false" argument, implying we only
 * want the menu to be launched.
 */
magic-menu.start:bool:false

/*
 * Speaks some English sentence
 */
magic-menu.speak:Hello, unless you've overridden your default language, this should sound like English.
  onfinish

    /*
     * Quits Magic Menu.
     */
    magic-menu.quit
```

Notice how after the Magic Menu has spoken what we supplied to **[magic-menu.speak]**, it will evaluate our
**[onfinish]** lambda callback, which for our purpose simply quits the menu, stopping its input loop, and
deleting its widget. If we hadn't supplied our above **[onfinish]**, the default implementation would be
evaluated, which tries to find a command matching what you uttered from its _"menu database"_, according to
your position in the menu hierarchy.

Below is an example of speaking a Norwegian sentence, which only works if you have a Norwegian voice installed
for your browser.

```hyperlambda-snippet
/*
 * Starting Magic Menu first, without initiating listening loop.
 */
magic-menu.start:bool:false

/*
 * Speaks some English sentence
 */
magic-menu.speak:Hei, jeg er Norsk
  voice:nb-NO
  onfinish
    magic-menu.quit
```

If you provide an **[\_arg]** to the **[magic-menu.listen]** event, it will internally invoke **[magic-menu.speak]**
with that argument, and initiate listening only after it has uttered your argument. At which point it will pass
in any (optional) **[voice]** argument to magic-menu.speak, and use any (optional) **[lang]** argument as its
listening language. Below is an _"echo loop"_ which first speaks something, assuming you're using English as your
default language, for then to capture speech input, and repeating whatever you said back to you.

```hyperlambda-snippet
/*
 * Starting Magic Menu first, without listening loop.
 */
magic-menu.start:bool:false

/*
 * Speaks some English sentence.
 */
magic-menu.listen:Speak to me brother
  onfinish
    magic-menu.speak:x:/../*/text?value
      onfinish
        magic-menu.quit
```

You can of course combine all constructs from above, to for instance speak something in Spanish, listen
for Spanish input, and repeating what was said in Spanish.

```hyperlambda-snippet
/*
 * Starting Magic Menu first, without initiating listening loop.
 */
magic-menu.start:bool:false

/*
 * Speaks something in Spanish, and waits for Spanish input.
 */
magic-menu.listen:Como estas
  voice:es-AR
  lang:es-AR
  onfinish

    /*
     * Repeats what it captured back to the user.
     */
    magic-menu.speak:x:/../*/text?value
      voice:es-AR
      onfinish
        magic-menu.quit
```

### Ninja tricks

By combining the Magic Menu and its API with Google Translate, you can use the Magic Menu to translate
from one language to another. Below is a piece of Hyperlambda that expects you to speak English, which
will then translate your input to Norwegian, and speack its Norwegian counterpart.

```hyperlambda-snippet
/*
 * Starting Magic Menu, without initiating listening loop.
 */
magic-menu.start:bool:false

/*
 * Asks user for input.
 */
magic-menu.listen:Give me something to work with
  lang:en-US
  voice:en-US
  onfinish

    /*
     * Translating input to Norwegian.
     */
    micro.google.translate:x:/../*/text?value
      dest-lang:nb-NO

    /*
     * Speaking our translated input with a Norwegian voice.
     */
    magic-menu.speak:x:/@micro.google.translate?value
      voice:nb-NO
      onfinish

        /*
         * Quits menu.
         */
        magic-menu.quit
```

Obviously, the ease of creating language tutoring apps, translation apps, etc, becomes quite stunning once
you see the creative potential of constructs such as the above.

