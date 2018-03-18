## Magic Menu API III - Manipulating your tree

The Magic Menu is a hierarchical menu system. This implies that what you're actually doing as you select menu items,
is that you are moving around the different branches in a tree structure. Although these Active Events are for the
most parts intended for internal usage by the menu itself, you can explicitly choose to manipulate
which branch you're currently on explicitly, by using the API of the Magic Menu. There are 4 basic Active Events
that allows you to query and manipulate the position in this tree, or what _"branch"_ you're currently at.
These are as follows.

* __[magic-menu.grammar-position.clear]__ - Clears your current position, and jumps to _"root branch"_
* __[magic-menu.grammar-position.get]__ - Returns your current position
* __[magic-menu.grammar-position.pop]__ - Removes the last position. Optionally pass in an integer implying _"n number of items"_ which it'll pop off its stack
* __[magic-menu.grammar-position.push]__ - Push a database command ID, or a global ID unto the stack of items

By using the above events, you can modify the state of your menu as you see fit, and explicitly move around in
your menu hierarchy, as you see fit. In fact, if you return boolean _"true"_ from your menu item's Hyperlambda,
what occurs is that the menu ID for your current command, is _"pushed"_ unto the stack of items, which is a
stack, preserved stateful within the Magic Menu itself, to track at which level it is currently at.

**Notice**, invoking the above events yourself, will post the **[magic-menu.position.changed]** event, which
again will re-databind your Magic Menu GUI. Below is an example of using push.

```hyperlambda-snippet
/*
 * Starting Magic Menu.
 */
magic-menu.start:bool:false

/*
 * "Pushing" the "gaia.help.hyperlambda" menu item unto
 * the stack, which will start at the "gaia.help.hyperlambda" level,
 * or "branch" if you wish.
 */
magic-menu.grammar-position.push:gaia.help.hyperlambda

/*
 * Providing context for the user.
 */
magic-menu.listen:I'm gonna jump right into Hyperlambda for you
```
