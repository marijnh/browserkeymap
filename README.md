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
   (`Shift-`, `Cmd-`, `Alt-`, `Ctrl-`, in that order) followed by a
   key name.

 * A key name is a capital letter for a letter key, `F` plus a number
   for a function key, the symbol typed by the key when shift isn't
   held down (one of ``[\]`'*,-./;=``), or one of the names `Alt`,
   `Backspace`, `CapsLock`, `Ctrl`, `Delete`, `Down`, `End`, `Enter`,
   `Esc`, `Home`, `Insert`, `Left`, `Mod`, `PageDown`, `PageUp`,
   `Pause`, `PrintScrn`, `Right`, `Shift`, `Space`, `Tab`, or `Up`.

You can get a key name from an event by calling `Keymap.keyName(event)`.

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

You can add and remove bindings on an existing keymap.

    myMap.addBinding("Alt-F4", handleQuit)
    myMap.removeBinding("Shift-Space")

So far, this doesn't do anything that a JavaScript object couldn't do.
The constructor accepts a second argument, which is an options object.
These options are supported:

  * **`multi`**: Boolean, defaults to true. Toggles support
    multi-stroke key bindings. Binding names may be space-separated
    strings containing multiple key names. The keymap will track which
    prefixes are part of a multi-stroke binding, and when such a
    prefix is looked up, it will return `"..."`, to indicate to client
    code that it should buffer the key name, and, on the next
    non-modifier key event, try looking up that key name suffixed by
    the next key name (separated by a space).

  * **`call`**: Function, defaults to null. When given, it makes this
    keymap a programmatic keymap, meaning the bindings are ignored and
    the function that is the value of the `call` option will be
    called, with the key name, when a key is looked up, and its return
    value returned.

## License

This module is released under an MIT license.
