# ZMK Modifiers & Behaviors Reference

## Core Modifiers

There are 8 modifier keys, 4 per side:

| Left | Right | What it is |
|------|-------|------------|
| `LSHFT` | `RSHFT` | Shift |
| `LCTRL` | `RCTRL` | Control |
| `LALT` | `RALT` | Alt (Option on Mac) |
| `LGUI` | `RGUI` | GUI (Cmd on Mac, Win on Windows) |

Left and right are interchangeable for most purposes, **except** `RALT` which on some OS layouts acts as **AltGr** (used for special characters like umlauts).

Shorthand prefixes for combining modifier + key: `LS()` `RS()` `LC()` `RC()` `LA()` `RA()` `LG()` `RG()`

You can nest them: `LS(LA(N4))` = Shift+Alt+4.

---

## Tier 1 — Used on every keymap

### `&kp` — Key press
The foundation. Sends a single key or modifier+key combo.
```
&kp A           // tap A
&kp LSHFT       // hold shift
&kp LS(N2)      // Shift+2 = @
&kp LC(C)       // Ctrl+C = copy
&kp LG(SPACE)   // Cmd+Space (Spotlight on Mac)
```

### `&mo` — Momentary layer
Hold to activate a layer, release to deactivate. No tap behavior.
```
&mo NAV         // hold = NAV layer active, release = back to base
```

### `&lt` — Layer-tap
Hold for layer, tap for key.
```
&lt NAV SPACE   // tap = Space, hold = activate NAV layer
```

### `&mt` — Mod-tap
Hold for modifier, tap for key. The basis of "home row mods".
```
&mt LSHFT A     // tap = A, hold = Shift
```

### `&trans` — Transparent
Pass through to the layer below. Used on overlay layers to let base keys work.

### `&none` — No action
Key does nothing.

---

## Tier 2 — Very useful once you know them

### `&sk` — Sticky key (one-shot modifier)
Tap and release, the next key you press gets the modifier applied. No holding required.
```
&sk LSHFT       // tap, release, then tap 'a' -> you get 'A'
```

Sticky keys **stack**: tap `&sk LGUI`, tap `&sk LALT`, then tap a key -> sends `GUI+ALT+key`. The sticky state waits for a non-modifier key before deactivating, so you can chain as many as you need.

Example flow for Ctrl+Shift+T (reopen browser tab):
1. Tap `&sk LCTRL` -> Ctrl is "queued"
2. Tap `&sk LSHFT` -> Ctrl+Shift are "queued"
3. Tap `T` -> sends Ctrl+Shift+T, all sticky mods deactivate

This is a popular alternative to home row mods. Instead of putting modifiers on letter keys (where timing causes misfires), put `&sk` modifiers on a separate layer:
```
Mods layer (accessed via thumb hold or &sl):
      sk GUI  sk ALT  sk CTL  sk SFT    _     |     _    sk SFT  sk CTL  sk ALT  sk GUI
```

### `&sl` — Sticky layer (one-shot layer)
Tap to visit a layer for exactly one keypress, then auto-returns to base.
```
&sl SYM         // tap, press one key from SYM layer, automatically back to base
```
Faster than holding `&mo` when you only need one symbol.

### `&caps_word` — Smart caps lock
Activates shift for consecutive letters. Auto-deactivates on space or punctuation. Underscores and numbers don't break it.
```
&caps_word      // activate, type 'my_variable' -> 'MY_VARIABLE', space turns it off
```
Perfect for SNAKE_CASE constants.

### `&tog` — Toggle layer
Tap once to lock a layer on, tap again to turn it off. Useful for semi-permanent modes like animation software layers.
```
&tog TVP        // tap = TVP layer on, tap again = off
```

### `&gresc` — Grave escape
One key, two symbols based on active modifiers:
- No modifiers -> `Esc`
- Shift held -> `` ` `` (backtick)
- GUI held -> `` ` `` (backtick)

Saves a key position on small boards. Less useful if you already have Esc on a combo or layer.

### `&key_repeat` — Repeat last key
Sends whatever key you last pressed. Reduces same-finger bigrams (e.g., typing "ll" without pressing `l` twice with the same finger).

---

## Tier 3 — Situational

### `&to` — Permanent layer switch
Like `&tog` but one-way. Switches to a layer and stays. You need another `&to BASE` somewhere on that layer to get back. Useful for completely separate modes like gaming where you want a totally different layout with no accidental base layer access.
```
&to GAME        // permanently switch to GAME layer
```

### `&kt` — Key toggle
Tap once to turn a modifier on (stays on), tap again to turn off. Like caps lock but for any modifier. Use case: gaming where you need Ctrl held for extended periods.
```
&kt LCTRL       // first press = Ctrl on, second press = Ctrl off
```

### `&macro` — Multi-key sequence
Sends a series of keypresses in order. Useful for email addresses, common strings, or complex shortcuts.
```
gif: gif {
    compatible = "zmk,behavior-macro";
    #binding-cells = <0>;
    bindings
        = <&macro_press &kp LSHFT>
        , <&macro_tap &kp N2>
        , <&macro_release &kp LSHFT>
        , <&macro_tap &kp G &kp I &kp F>
        ;
};
// Single keypress types "@gif"
```

### `&hold_tap` — Custom hold-tap behavior
The low-level version of `&mt`/`&lt`. Define your own instance with specific timing, flavor, and quirks. Available flavors:
- `hold-preferred` — bias toward hold
- `balanced` — waits for another keypress to decide
- `tap-preferred` — bias toward tap
- `tap-unless-interrupted` — taps unless another key is pressed during hold

Most people just tweak `&mt`/`&lt` globals and never touch this directly.

---

## Tier 4 — System/firmware

| Behavior | What it does |
|----------|-------------|
| `&sys_reset` | Reset the keyboard |
| `&bootloader` | Enter bootloader/flash mode |
| `&bt BT_CLR` | Clear bluetooth pairing |
| `&bt BT_NXT` / `&bt BT_PRV` | Cycle bluetooth profiles |
| `&out OUT_TOG` | Toggle USB/BT output |
| `&studio_unlock` | Unlock ZMK Studio |
