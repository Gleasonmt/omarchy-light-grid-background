# Light Grid

An animated background for [Omarchy](https://omarchy.org). Light-cycles racing over a glossy neon grid, laying walls of light under a stormy sky.

> **Animated with [omarchy-live-wallpaper](https://github.com/Gleasonmt/omarchy-live-wallpaper).**
> A background, not a theme: it goes with whatever theme you use.
![Light Grid, moving](media/light-grid.webp)

## Install

You need the [live-wallpaper plugin](https://github.com/Gleasonmt/omarchy-live-wallpaper), 1.2.0 or later.
Install the plugin and this background together:

```bash
omarchy plugin add https://github.com/Gleasonmt/omarchy-live-wallpaper --enable --yes
git clone --depth 1 https://github.com/Gleasonmt/omarchy-light-grid-background ~/.config/omarchy/live-backgrounds/light-grid && omarchy restart shell
```

If you've installed the plugin's optional `live-wallpaper` command, `live-wallpaper install https://github.com/Gleasonmt/omarchy-light-grid-background` does the second line for you.

Then pick **Light Grid** in Omarchy menu → Style → Background. It's offered under every theme,
including ones you install later. Update with `live-wallpaper update`, or `git pull` in that folder.

## Details mode

In details mode the picture stays still and only small details move. It uses the least power,
which makes it good on battery or if you want a calmer desktop. Turn it on with `live-wallpaper mode details`
(`battery-mode details` for battery only) or in Omarchy menu → Style → Live Wallpaper:

![Light Grid, details mode](media/light-grid-lite.webp)

## How it moves

The wallpaper is a GPU shader (`live/1-the-grid.frag.qsb`, source in `src/`) drawn by the
[live-wallpaper plugin](https://github.com/Gleasonmt/omarchy-live-wallpaper);
`backgrounds/1-the-grid.png` is a frame of it, used by the lock screen and when the plugin
isn't installed. It pauses while a window covers the screen. See the plugin's README
for frame rates and battery settings.

Original artwork. The look is inspired by films and games; no logos or characters
are used.

## Performance

Measured on a Dell Latitude 9520 (Intel Iris Xe graphics, 8 threads) driving a 3440×1440
monitor, plugged in, with the plugin's default 30 fps. The wallpaper pauses whenever a window
covers the screen, so these are the costs while it's actually visible:

| Mode | GPU used | Wallpaper CPU | Compositor CPU |
|---|---|---|---|
| Still (plugin off) | ~0% | 2% | 4% |
| **Details mode** | ~56% | 6% | 5% |
| **Light Grid** | ~59% | 5% | 5% |

*GPU used* is how busy the GPU was, weighted by its clock speed against its maximum
(1350 MHz here). *CPU* is a share of one core.

This is the heaviest of the scenes. Each pixel traces a ray through a 3D scene: the
glossy floor reflects the cycles, their light walls and the stormy sky, and the grid
lines are smoothed against shimmering. The scene is the same everywhere on screen, so
there is little hidden work to skip, and because the cycles ride across the whole grid,
details mode still redraws most of the screen. That's why details mode saves so little here.

If that's more than you want, these are optional, set in
`~/.config/omarchy/live-wallpaper.json` (see the
[plugin's README](https://github.com/Gleasonmt/omarchy-live-wallpaper)):

- **Half resolution**, `"renderScale": 0.5`: about **8.5%** GPU. The grid lines get a
  little softer up close. This is the biggest saving.
- **15 fps**, `"fps": 15`: about **32%** GPU. Everything stays sharp, but motion is less smooth.
- **Battery:** on battery the plugin already draws at half resolution and 10 fps by
  default (plugin 1.1.2 or later: run `omarchy plugin update`).

## Making changes

The scene is `src/<name>.frag` (with `src/common.glsl`): edit it, then run

```bash
tools/build.sh           # rebuilds live/, the still in backgrounds/
tools/build.sh --media   # also the animated previews in media/ (a few minutes)
```

and commit the results. `tools/check.sh` runs the same checks as the pull request CI
(the shader compiles for every GPU Omarchy runs on, and the files are in place; needs `glslang` and `jq` too). Needs `qt6-shadertools`, `qt6-declarative`, ImageMagick and
(for `--media`) ffmpeg. To try a change on your own desktop, run
`live-wallpaper update` after pushing, or copy the built files into
`~/.config/omarchy/live-backgrounds/light-grid/`.

## License

[MIT](LICENSE)
