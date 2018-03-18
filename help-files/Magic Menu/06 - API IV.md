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
as you see fit.

