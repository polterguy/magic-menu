## Teaching your computer to speak

If you're logged in as _"root"_ in your system, and you speak something that your computer doesn't understand,
the response will be _"I do not understand"_ - At which point you can simply say _"I will teach you"_, and
a CodeMirror editor will popup, allowing you to type in a piece of Hyperlambda, and have that Hyperlambda
associated with your phrase/sentence/word. The same is true if you search for a phrase that doesn't exist
at your current position in the menu's hierarchi. Instead of speaking _"I will teach you"_, you can also
click the _"+"_ button to teach the Magic Menu a new phrase or word.

As you type in your Hyperlambda, you can also choose to give your item a Global ID, making it private, and
making it a global menu item. In addition you can type in synonyms, separated by a carriage return, and you
can even choose to create localized versions of your synonyms, by prepending your language code in front
of your phrase. Below is an example of how this might look like for the phrase _"Thank you"_.

**Notice**, to test this example, you can launch the Magic Menu by clicking the _"magic wand"_ toolbar
button at the top of your page, and speak out loud the phrase _"Thank you"_. This will result in that
the Magic Menu will utter _"I don't understand"_, at which point you can speak _"I will teach you"_.
When you have spoken _"I will teach you"_, a window will popup, allowing you to create a new command.

https://phosphorusfive.files.wordpress.com/2018/03/magic-menu-screenshot-teach.png

**Notice** the third phrase above is the Norwegian word for _"Thank you"_, and the language code of `nb-NO`
implies _"Norwegian Bokmal"_. If I changed my preferred language to Norwegian, I could speak _"Takk"_, and
the computer would evaluate the specified Hyperlambda as a response. This allows you to create localized
items, in any language you wish, by simply prepending the language code in front of your synonyms. The Magic
Menu will also only display menu items it has localized versions of, for your currently logged in user, according
to his currently selected language.

**Notice**, the Magic Menu assumes that whatever you're typing in as phrases is in your currently selected
language, so adding a language code for Norwegian, if that was my currently selected language, would not
be necessary - Since the Magic Menu would assume that everything I type into it as phrases, are localized
in my currently selected language, unless explicitly overridden as the screenshot above illustrates. You cannot
use colons (:) in your phrases for the above reasons.

### Changing your tree

The last line of Hyperlambda from the above screenshot is `return:bool:false`. This implies that my current
menu item, does not have any children items of itself, and is in fact a _"leaf item"_. If I had chosen to
return _"true"_ instead, the Magic Menu would create a new _"scope"_, or a _"sub menu"_, which I could further
add children menu items to. Since the phrase _"Thank you"_ doesn't need any children menu items, the above
screenshot example, simpy returns _"false"_ to the Magic Menu - Indicating it is a _"leaf item"_. However, a
phrase such as _"Open up an application"_ would probably need children menu items, since the user would
have to inform the Magic Menu which app he wants to open up, and this would be contextually related to
the parent menu item, being _"Open up an application"_. At which point your _"Open up an application"_ menu
item would have to return `true`.

If you look at the menu item for _"Translate something"_ in your administrative backend, you will see that
it doesn't return anything, which is equivalent of returning _"true"_. This implies that the phrase _"Translate
something"_ has its own children menu items, which you can clearly see from the screenshot below that it does
indeed have.

https://phosphorusfive.files.wordpress.com/2018/03/megic-menu-hierarchy-screenshot.png

In the above screenshot the phrases _"Arabic"_, _"Chinese"_, _"Danish"_, etc - Are children menu items
of the menu item _"Translate something"_, and are hence only accessible when you have first selected the
_"Translate something"_ menu item. In the administrative backend of the Magic Menu, you can see a _"Parent"_
column, which informs you of whether or not the item has a parent menu item, and what parent is has - Or
if it is a root menu item.

Yet again, global menu items are accessible from anywhere you are within your navbar structure. Global
menu items are also clearly marked in the GUI of the Magic Menu as you browse your available menu items.

### Millions of menu items

In theory, you could easily create a menu item structure with millions of items, or at least hundreds of thousands
of items, since the Magic Menu stores its items in your MySQL database - Which scales decently, for huge
amounts of data. This allows you to create a very rich interaction structure, of deeply cascading and
hierarchical commands, solving literally any problems, using exclusively the Magic Menu.

Since the Magic Menu also easily allows you to export your menu items, and import them into other systems -
The hope is that this will allow for a collaborative effort, where users collaborate, and exchange their
menu items with each other. Since each menu item is literally a Hyperlambda snippet, you could also in theory
create your entire apps, purely as Magic Menu items. Whether or not the latter makes sense or not, is up
to you to decide. This is arguably a lot of the purpose of the _"Global ID"_ field of the menu items, to
make sure you can globally and uniquely identify menu items - Which makes it important that you give your
menu items a **unique** global ID.

If you want to go down this road, and create entire applications using the Magic Menu, I would encourage you
to combine it with Hypereval though, to store larger snippets of code in - Such that you get at least some
sort of separation between _"implementation"_ and _"UI"_. I will periodically create menu items myself,
to automate some part of Phosphorus Five, which I might choose to release, either for gratis, or for a fee -
At which point I will **always** namespace my menu items, by prepending them with `gaia`.

**Warning**, hence **stay away** from namespacing your menu items with the string `gaia`, since such items
might crash with menu items I create and release into the public.

### Distributing your version - Advanced

If you have created some menu items, and you want to create your own distribution, containing these items -
You can easily do so by exporting your menu items from the administrative dashboard to a zip file, unzip this
file, and put the unzipped Hyperlambda files into your distribution's _"/initial-data/"_ folder. This ensures
that when the Magic Menu is installed, these items are automatically created during installation. This allows
you to create your own distributions, containing the menu items you want to distribute to your customers, which
is useful if you're creating your own distribution of Phosphorus Five, with for instance _"Hyperbuild"_ or
something similar.

