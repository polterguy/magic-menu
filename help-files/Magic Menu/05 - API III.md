## Magic Menu API III - Manipulating your tree

The Magic Menu is a hierarchical menu system. This implies that what you're actually doing as you select menu items,
is that you are moving around the different branches in a tree structure. Although these Active Events are for the
most parts intended for internal usage by the menu itself, you can explicitly choose to manipulate
which branch you're currently on explicitly, by using the API events of the Magic Menu. There are 4 basic Active Events
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

You can imagine the _"grammar-position"_ object as a stateful stack of pointers, pointing to branches in the tree
structure, that the Magic Menu's items are internally stored as. By intelligently using these four Active Events,
you can _"jump"_ back and forth in your tree, as you see fit. This allows you to for instance have a shallow
menu item, that once chosen, will evaluate a branch at another part of your tree, creating _"pointers"_, where
the implementation for your menu item, is in a completely different part of your tree. This avoids repeating
the same menu items multiple times, provides better enapsulation, and creates more _"DRY"_ code.

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

Below is another example, that first jumps into the Hyperlambda menu item, speaks something, for then to _"pop"_
the Hyperlambda item from its stack. Watch how the GUI of the menu _"jumps"_ back and forth, as the _"push"_
event, and the _"pop"_ event is evaluated.


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
magic-menu.speak:Jumping to Hyperlambda
  onfinish
    magic-menu.grammar-position.pop
    magic-menu.listen:Sorry, I changed my mind
```

The **[magic-menu.grammar-position.pop]** event can optionally pop multiple items off the stack, by providing
an integer value, indicating how many items you want to remove from the stack. While the **[magic-menu.grammar-position.clear]**
event, simply removes _all_ items from the stack, and jumps back to the root level.

The **[magic-menu.grammar-position.get]** event, simply returns the current position of the grammar object, or
the top item from your stack, without removing it from the stack of items.

Both the clear, pop and push events will post the **[magic-menu.position.changed]** event, which you can choose
to subscribe to yourself, in for instance your own widget lambda events, to be notified of whenever the
_"grammar object pointer"_ changes.
