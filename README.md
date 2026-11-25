# tabletop

![tabletop: Android apps in tablet layout, in a window on your desktop](docs/social-preview.png)

Run your phone's apps in tablet layout, each in its own window on your Linux desktop.

tabletop asks which phone to use and which app to open. It then has
[scrcpy](https://github.com/Genymobile/scrcpy) start that app on a separate virtual display
sized like a tablet. Apps switch to their large-screen layouts (navigation rails, list and
detail side by side), and your phone's own screen stays free.

Tested on a Pixel 9 running Android 17, with scrcpy 5.0.1 on KDE Plasma (Wayland).

## How it works

```
adb devices ──► pick a phone ──► pick an app ──► scrcpy --new-display=1536x960/192
 (USB + wireless)   (zenity)     (launcher apps)          --start-app=<package>
```

- Every ready device is listed, over USB or Wireless debugging. A phone connected both
  ways shows up once, using USB. With only one phone connected, the picker is skipped.
- Phones are listed by codename and serial, e.g. `tokay (52220DLAQ001KK)`.
- The app picker lists packages with a launcher icon, read straight from the package
  manager, so it opens almost instantly. Type in it to search.
- scrcpy opens the app on a new virtual display that resizes along with the window
  (`--flex-display`). It keeps the phone awake while the window is open.

## Requirements

- Linux with `scrcpy` (3.0 or newer; tested with 5.0.1), `adb` (`android-tools`) and `zenity`
- Android 10 or newer, with **Developer options → USB debugging** on. For cordless use, also
  turn on **Wireless debugging** and pair once with `adb pair <ip>:<pairing-port>`.
- Some phone makers' Android versions handle virtual displays less well than Pixels do.

## Install

It's a single shell script. Put it anywhere on your `PATH`:

```sh
install -Dm755 tabletop ~/.local/bin/tabletop
```

## Display size

By default the virtual display is `1536x960` at 192 dpi: a 1280×800 dp tablet layout,
shown 1.2× larger than 1:1, which fits on a 1080p monitor. Set `TABLETOP_DISPLAY` to change it:

```sh
TABLETOP_DISPLAY=1280x800/160 tabletop   # same layout at 1:1, for smaller screens
```

The format is `WIDTHxHEIGHT/DPI`. Android lays apps out in dp, where
dp = px × 160 / dpi. To keep the 1280×800 dp layout at a scale `s`, use
`(1280·s)x(800·s)/(160·s)`. A higher dpi makes everything bigger. Some options:

| setting | scale | layout (dp) | feel |
|---|---|---|---|
| `1280x800/160` | 1.0× | 1280×800 | crisp but compact |
| `1536x960/192` | 1.2× | 1280×800 | the default |
| `1280x800/213` | 1.33× | 960×600 | big; just reaches tablet layouts |
| `1536x960/240` | 1.5× | 1024×640 | big |

Resizing the window gives you more room in dp; the dpi stays the same.

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).
