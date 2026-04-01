# Angler

A lightweight browser-based tool for annotating images and videos with angle measurements and text labels. Built to track progress in flexibility and mobility training — measure the angle at your hip in a forward fold, mark key landmarks, and export the annotated image or video clip.

![screenshot placeholder](docs/screenshot.png)

<video src="https://github.com/user-attachments/assets/e7cf0690-cdeb-4a1f-bc64-843cf44ca81e" controls width="400"></video>

---

## Requirements

- [Docker](https://docs.docker.com/get-docker/) with the Compose plugin (included in Docker Desktop)

That's it. No Node, Python, or other dependencies needed on your machine.

---

## Installation

```bash
git clone <your-repo-url>
cd angler
```

Or simply download and unzip the project folder.

---

## Running the app

```bash
docker compose up
```

Then open **http://localhost:3000** in your browser.

To stop it:

```bash
docker compose down
```

### Notes

- The app runs entirely in the browser. No data is sent anywhere.
- Because the source files are mounted as a volume, any edits to `app/index.html` are reflected immediately on browser reload — no container restart needed.
- To run in the background: `docker compose up -d`

---

## Usage

### 1. Load a file

Either click **Load Image** or **Load Video** in the toolbar, or **drag and drop** a file onto the canvas.

- **Images**: JPG, PNG, WebP
- **Videos**: MP4, WebM, MOV (any format your browser supports)

---

### 2. Tools

Select a tool from the toolbar or use the keyboard shortcuts below.

#### ↖ Select (`V`)

The default tool for interacting with existing annotations.

- **Click** an annotation to select it.
- **Drag** to move it anywhere on the image.
- **Drag the ↻ handle** (appears above the selected annotation) to rotate its label.
- For angle annotations, **drag any of the three dots** individually to reposition arm or vertex points.
- **Double-click** a text label to edit its content.
- **Click empty space** to deselect.

#### 📐 Angle (`A`)

Measures the angle formed by three points. Useful for joint angles (hip, knee, shoulder, etc.).

Click three times in this order:

| Click | What to place |
|-------|--------------|
| 1st   | First arm point (e.g. shoulder, or a point along the torso) |
| 2nd   | **Vertex** — the corner where the angle is measured (e.g. hip) |
| 3rd   | Second arm point (e.g. knee, or a point along the leg) |

The angle in degrees is displayed automatically near the vertex. Press `Esc` to cancel mid-way.

#### T Text (`T`)

Places a text label anywhere on the image.

- Click where you want the label.
- Type your text in the input that appears.
- Press **Enter** to confirm or **Esc** to cancel.
- After placing, switch to Select to move or rotate it.

---

### 3. Styling annotations

Select an annotation to reveal the **properties panel** on the right side of the toolbar:

- **Color swatch** — click to open a color picker and change the annotation color (works for both angle and text annotations).
- **Size − / +** — increase or decrease the font size of a text label (visible only when a text annotation is selected, in steps of 2px).

---

### 4. Undo / Redo

| Action | Shortcut |
|--------|----------|
| Undo   | `Ctrl+Z` (or `Cmd+Z` on Mac) |
| Redo   | `Ctrl+Shift+Z` (or `Cmd+Shift+Z`) |

Every action that modifies annotations — adding, moving, rotating, deleting, changing color or size — is undoable.

---

### 5. Deleting annotations

- Select an annotation and press `Delete` (or `Backspace`), or click the **🗑 Delete** button.
- To remove everything at once, click **✕ Clear All**.

---

### 6. Exporting images

Click **⬇ Export PNG** or press `Ctrl+E` (`Cmd+E`).

- **Chrome / Edge**: a native Save As dialog opens so you can choose the filename and folder.
- **Firefox / Safari**: the file is downloaded automatically as `angler-export.png` into your browser's default Downloads folder.

The exported PNG is at the **original image resolution** with all annotations rendered at full quality.

---

### 7. Video support

When a video is loaded, additional controls appear below the canvas:

#### Playback

- **Play/Pause** button or `Space` key to toggle playback.
- **Scrub bar** — drag to jump to any frame.
- **Current time / duration** displayed next to the scrub bar.

#### Audio

- **Mute/Unmute** button to toggle audio.
- **Volume slider** to set the volume level.

Audio is preserved in exported video clips.

#### Time-ranged annotations

Annotations added while a video is loaded are automatically attached to the current playback position:

- Each annotation has a **start time** and **end time** — it only appears within that range.
- By default the end time is set to the end of the video.
- Annotations placed at different timestamps are independent; scrubbing through the video shows or hides them automatically.

#### Keyframe animation (angle annotations)

Angle annotations in video mode support **keyframe animation** — the angle can change as the video plays:

1. Place an angle annotation at a given timestamp.
2. Scrub to a different time where the joint is in a different position.
3. Drag any of the three annotation points to their new positions.
4. A new keyframe is saved automatically on mouse release.

When you play the video, the annotation smoothly interpolates between keyframes, tracking the joint movement frame by frame. You can add as many keyframes as needed.

#### Exporting video

Click **⬇ Export Video**.

- A Save As dialog opens immediately so you can choose the filename and folder before recording begins. The suggested name is based on the original video filename (e.g. `myvideo-annotated.mp4`).
- The app detects which portion of the video contains annotations and **only records that slice** — e.g., if your annotations span 5:00–5:45, the export is ~46 seconds, not the full video length.
- A progress bar shows recording progress.
- The exported clip is at the **original video resolution and aspect ratio** (portrait videos stay portrait).
- The export format matches the original video when the browser supports it (MP4 on Safari and Chrome; WebM fallback on Firefox).
- The exported clip includes the original audio.

> **Note:** Recording happens at real-time speed (the browser plays through the annotated segment while capturing). For a 46-second annotated clip, the export takes about 46 seconds.

---

## Keyboard shortcuts

| Key | Action |
|-----|--------|
| `V` | Select tool |
| `A` | Angle tool |
| `T` | Text tool |
| `Space` | Play / Pause (video) |
| `Ctrl+Z` | Undo |
| `Ctrl+Shift+Z` | Redo |
| `Ctrl+E` | Export PNG |
| `Delete` / `Backspace` | Delete selected annotation |
| `Esc` | Cancel pending angle / deselect |

---

## Project structure

```
angler/
├── app/
│   └── index.html       # Entire app — HTML, CSS, and JS in one file
├── docker-compose.yml   # Starts nginx serving the app on port 3000
├── nginx.conf           # Minimal static file server config
└── README.md
```

The app is intentionally a single self-contained HTML file with no build step or external dependencies. This makes it easy to publish to any static hosting platform (Vercel, Netlify, GitHub Pages, S3, etc.) by simply uploading `app/index.html`.
