# Assets

Drop screenshots, board renders, and demo GIFs here, then reference them from the
project pages.

## Worth adding — high impact, low effort

You already have these files; adding them would meaningfully strengthen the
portfolio, since hardware projects are judged visually.

| Asset | Source on your machine | Goes in |
|---|---|---|
| ShadowHive demo clip | `~/ShadowHive.mp4` (62 MB) | `projects/shadowhive.md` |
| MP3 board render / photo | KiCad 3D view of `~/Documents/MP3_PROJECT` | `projects/custom-mp3.md` |
| MP3 schematic export | KiCad → File → Plot → SVG/PDF | `projects/custom-mp3.md` |
| Camera trap enclosure photo | Your 3D-printed enclosure + plywood mount | `research/wildlife-camera-trap.md` |
| EMG live plot screenshot | `host/emg_live.py` output | `projects/shadowhive.md` |
| Robotic hand photo | ProsthetiSense hardware | `projects/prosthetisense.md` |

**Video note:** GitHub renders `.mp4` inline in a README if you upload it via the
web editor (drag it into an edit box), but a 62 MB file in git history is not
ideal. Two better options: convert a 10-second highlight to GIF, or upload the
video to a GitHub Release and link it.

```bash
# 10-second GIF from the demo video, small enough to commit
ffmpeg -i ~/ShadowHive.mp4 -ss 0 -t 10 -vf "fps=12,scale=640:-1" ~/Portfolio/assets/shadowhive-demo.gif
```

Then in the project page:

```markdown
![ShadowHive live mapping](../assets/shadowhive-demo.gif)
```
