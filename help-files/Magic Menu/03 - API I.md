## Magic Menu API

The Magic Menu has a rich API which you can interact with. The most important Active Events are probably
the ones necessary to start and stop the Magic Menu, speak some phrase, and initiate speech recognition,
which you can find below.

* __[magic-menu.start]__ - Starts Magic Menu listening loop, optionally pass in "false" to only start menu, without greeting user and initiating listening loop
* __[magic-menu.speak]__ - Speaks something, optionally pass in **[voice]**
* __[magic-menu.listen]__ - Initiates the listening loop, and optionally speaks something before listening is initiated, optionally pass in **[\_arg]**, **[voice]** and **[lang]**. Will return the utterance it recognizes as **[text]** to your **[onfinish]** lambda callback.
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

The **[magic-menu.start]** event optionally takes a boolean **[\_arg]**, which if is false, will not greet
the user or start the listening loop. In addition, you can also provide a **[show-options]** argument to it,
being boolean false, to make sure the options are not shown initially. And finally, if you want to provide
a custom _"greeting"_ to the menu, you can do so with a **[greeting]** argument. Notice, if you start the
Magic Menu with an explicit greeting, and without starting the input loop, or speaking the greeting - The
menu will only show this greeting in its _"help label"_.

```hyperlambda-snippet
/*
 * Notice, we'll have to start the Magic Menu before we can interact with it.
 *
 * Making sure we start it without initiating listening loop.
 */
magic-menu.start:bool:false
  show-options:bool:false
  greeting:Speaking English

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
  show-options:bool:false

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
  show-options:bool:false

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
  show-options:bool:false

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

Below is a list of some _"Ninja tricks"_ you can use the Magic Menu for.

#### Translation and Babel fishing

By combining the Magic Menu and its API with Google Translate, you can use the Magic Menu to translate
from one language to another. Below is a piece of Hyperlambda that expects you to speak English, which
will then translate your input to Norwegian, and speak its Norwegian counterpart.

```hyperlambda-snippet
/*
 * Starting Magic Menu, without initiating listening loop.
 */
magic-menu.start:bool:false
  show-options:bool:false

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

**Notice**, the speech parts of the Magic Menu is built on top of the speech Active Events from Micro. Feel
free to check out the documentation for Micro for more details about the internals of speech recognition and
speech synthesis in Phosphorus Five.

#### Speech utterance queue

Since the speech synthesis implementation of Phosphorus Five will always only speak one phrase at the time,
this implies that you can sequentially invoke **[magic-menu.speak]**, multiple times, which will result in
that each invocation is spoken linearly, as invoked by you. This can provide an easier way for you to have
the Magic Menu speak multiple sentences linearly, without having to mess with too many recursive levels of
handling the **[onfinish]** callback. Below is in example of this.

```hyperlambda-snippet
/*
 * Starting Magic Menu, without initiating listening loop.
 */
magic-menu.start:bool:false
  show-options:bool:false

/*
 * Uttering two sentences.
 */
magic-menu.speak:Hello there
magic-menu.speak:My name is Phosphorus Five

/*
 * Starting capture, making sure we utter a third sentence before
 * capturing is initiated.
 */
magic-menu.listen:What's yours?
  onfinish

    /*
     * Greeting user, by his name.
     */
    magic-menu.speak:Nice to meet you {0}
      :x:/../*/text?value
      onfinish

        /*
         * Quit menu.
         */
        magic-menu.quit
```

#### Querying available languages

To figure out what voices are available for speech synthesis (and hence speech recognition language codes),
you'll need to use an Active Event from Micro, which is called **[micro.speech.speak.query-voices]**. This event
is documented in the documentation for Micro.

