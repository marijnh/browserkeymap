# Browser Keymap

A small library for doing sane key binding in the browser. It defines two
things:

### A string notation for key events

`browserkeymap` keys are represented by strings like `"Shift-Space"`,
`"Ctrl-Alt-Delete"`, or `"'x'"`. The rules are:

 * `keypress` events are represented as the character that was typed
   between single quotes. They do not support modifiers because
   browsers do not attach modifier information to `keypress` events.

 * `keydown` events are represented as zero or more modifiers
   (`Shift-`, `Cmd-`, `Alt-`, `Ctrl-`) followed by a key name.

 * A key name is a capital letter for a letter key, a digit for a
   number key, `F` plus a number for a function key, the symbol typed
   by the key when shift isn't held down (one of ``[\]`'*,-./;=``), or
   one of the names `Alt`, `Backspace`, `CapsLock`, `Ctrl`, `Delete`,
   `Down`, `End`, `Enter`, `Esc`, `Home`, `Insert`, `Left`, `Mod`,
   `PageDown`, `PageUp`, `Pause`, `PrintScrn`, `Right`, `Shift`,
   `Space`, `Tab`, or `Up`.

You can get a key name from an event by calling
`Keymap.keyName(event)`.

You can normalize a key name string (fixing the order of the
modifiers, and replacing alternative modifier names with their
standard name) by calling `Keymap.normalizeKeyName(string)`. This
function maps the modifier `Mod-` to `Cmd-` on Mac platforms and to
`Ctrl-` on non-Mac platforms. It also accepts `a-` for `Alt-`, `c-` or
`Control-` for `Ctrl-`, `m-` or `Meta-` for `Cmd-`, and `s-` for
`Shift-`.

You can use `Keymap.isModifierKey(string)` to find out whether the
given key name refers to a modifier key.

### An object type for keymaps

The `Keymap` constructor itself, which is the thing the library
exports, can be used to build keymaps.

    var myMap = new Keymap({
      "Ctrl-Q": handleQuit,
      "Shift-Space": autocomplete
    })

A keymap associates keys with values. You can call its `lookup` method
to look up a key:

    myMap.lookup("Ctrl-Q") // → handleQuit
    myMap.lookup("Alt-F4") // → undefined

You can create a new map from an existing map with its `update`
method:

    var newMap = myMap.update({
      "Alt-F4": handleQuit,
      "Ctrl-Q": null
    })

That will create a new map, starting with the bindings in `myMap`,
adding a binding for `Alt-F4`, and removing the binding for `Ctrl-Q`.

Multi-stroke keys are supported by providing space-separated names to
a keymap. When a prefix of a multi-stroke key is looked up, the
`lookup` method will return `Keymap.unfinished`. The handler should
then buffer the key name, and on the next key event (possibly with a
timeout to clear buffered keys), try again by prefixing the new key
event's name with the buffered key(s).

## License

This module is released under an MIT license.
