## Introduction to the Magic Menu

The Magic Menu is a common navigation system for all your apps. In addition to being a common navigation
system, it also supports speech recognition and speech synthesis - But only on browsers that supports this,
which happens to be only Google Chrome at the time of this writing. This allows you to completely control
your system, almost exclusively using your voice if you wish. The Magic Menu allows you to create your own
menu commands, which are sentences, phrases and words, which are associated with some piece of Hyperlambda,
that will be evaluated when your menu item is chosen, or the phrase/sentence/word is spoken. Below is a
screenshot of how the Magic Menu looks like.

https://phosphorusfive.files.wordpress.com/2018/03/magic-menu-screenshot-gui.png

Below is an illustration of whatthe different parts of your menu are.

https://phosphorusfive.files.wordpress.com/2018/03/magic-menu-screenshot-gui-info.png

The Magic Menu supports searching for menu items, paging, in addition to having a hierarchical structure.
It also loads items on demands. This allows you to create humongously large menu structures,
with much more menu items than you would normally use in a normal menu.

Now you can use the Magic Menu simply as a _"navbar"_, and ignore its speech recognition capabilities - At
which point it becomes a highly flexible and extendible menu system. However, if you are using a browser that
supports speech recognition and speech synthesis (Google Chrome), you can also use it as a speech
interface to your web apps. Regardless of how you choose to use it, understanding its structure is imperative,
since you would highly likely want to create your own custom menu items, to make it possible to automate tasks
and such, through menu items in it. So let us have a look at its structure, which is more easily understood,
with a screenshot of its administrative backend.

https://phosphorusfive.files.wordpress.com/2018/03/magic-menu-screenshot.png

In the above screenshot, you can see that there are four synonym phrases, which are all associated with
the same piece of Hyperlambda, which will be evaluated when that menu item is _"chosen"_ or _"spoken"_.
Each phrase can also be localized for some specific language, by prepending the language code for your
chosen language in front of it - Which we will look at later.

The above example Hyperlambda simply checks to see if there are any modal windows opened in your app,
and if so, it will close these windows for you. This allows you to close a modal window, by for instance
speaking the phrase _"close modal windows"_. To create your own menu items, require you to have at least
some basic knowledge about Hyperlambda, and the API of whatever module you want to create items for.
If you want to learn Hyperlambda, then please check out the documentation for Hyperlambda at the root
of the documentation system.

### Menu structure

Each menu command can have multiples _"aliases"_ or _"synonyms"_, which are synonymous phrases or words,
which are all associated with the same actual piece of Hyperlambda. A menu item can be _"private"_. This
implies that it is private for your user only, and won't be accessible for other users in your system.

In addition, each menu item can also optionally be created as a _"global"_ menu item. To understand what
this implies, you must first realise that the menu structure is actually a graph object, or a tree structure,
allowing for you to create tree structures of menu items. A _"global"_ menu item will always be accessible,
regardless of where you are in this tree structure, and can hence be logically thought of as _"root menu items"_,
which are always accessible, regardless of how deeply you have traversed into your tree structure of menu items.

In addition, each menu item has a _"global ID"_. This is a unique identifier, which (preferably) should be
unique for your company/entity/user/etc. This allows you to _"merge"_ menu items from other companies or
users, without having these clash with each other, in addition to serving as a globally unique ID for
your menu item. I always start my menu items global ID with the word _"gaia."_ for instance. You should
find some other globally unique name to start yours with, such as your company name, etc.

#### Conversations with your computer

Due to that the Magic Menu's structure is hierarchical, and actually implemented as a tree structure -
This allows you to implement somewhat of a _"conversational type of user interface"_, which just so happens
to seem very intuitive for the human brain. For instance, by default there is a menu item called _"Translate something"_.
This menu item, if chosen, will ask the user _"What language do you wish to translate to"_, at which point
you can answer _"Arabic"_, _"Norwegian"_, etc.

When you have chosen a destination language, it will use its dictation capability to figure out what you
actually want to translate. This would result in what would feel like having a _"natural conversation"_ with
your computer, that could be broken up into the following entities.

* __You__ - _"Translate something"_
* __Computer__ - _"Which language do you wish to translate into"_
* __You__ - _"Spanish"_
* __Computer__ - _"What do you want to translate"_
* __You__ - _"Could I have a cup of coffee please"_
* __Computer__ - _"¿Podría tomarme una taza de café por favor?"_

**Yet again**, speech recognition is currently only available on Google Chrome.

**Notice**, the Magic Menu only listens for input when it is flashing yellow. If you speak to it when it
is not yellow, it won't capture your input.