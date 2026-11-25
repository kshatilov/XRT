# XRT / XRPPT

Working notes for **XRT** ("eXtended Reality Thesis") and **XRPPT** ("XR presentations"). XRT augments a physical book with projected content; XRPPT uses printed presenter notes to control a prepared presentation. This first document records the photographed notebook page and the accompanying explanation, not a settled technical design.

## 1. Notebook photo: transcription

This section transcribes the visible page, rather than silently correcting it to match the concept description below. Arrows represent drawn connections; `[unclear]` and `[reading?]` mark uncertain handwriting. Text hidden by the hand or page edge is not reconstructed.

### Names, settings, and assets

- Date at top: `25-Nov` (year not visible).
- `XRT (eXtended Reality Thesis)`; `XRPPT (XR presentations)`.
- A horizontal setting axis labels `remote classic`, `remote VR`, `on-site VR`, `on-site classic` (left to right; the middle two labels are cramped).
- `presenter / producer` -> a box with `?` -> `VR canvases`, `Mixed-mode on-site canvases`, `Remote classic canvases`.
- `smartglasses` -> another box with `?` -> `XR assets # digital twins of SOOI / tracking systems (CNN?)` [last abbreviation uncertain]; `SOOI assets # the book` [acronym uncertain]; `PPT / PDF`.

![Editable redraw of the setting and content map](diagrams/reality-and-assets.svg)

### Devices and audience

The sketched book is labelled `The book / presenters notes`, with regions `A1`, `A2`, `A3` and a finger pointing at `A3`. Circled numbers identify devices and destinations:

1. Book / presenter notes (regions `A1`, `A2`, `A3`).
2. Glasses on the presenter; `WiFi / BT`, `2x video feed & audio` alongside the connection.
3. `phone` (drawn with a screen).
4. `or laptop` (drawn with a screen); `hdmi` beside the output cable.
5. `on-site video-projector` -> screen labelled `A3`, with `on-site audience`.
6. `Projective overlay` beside a small projector/camera sketch; the line from the `presenter's 3rd POV camera` appears to feed this branch.

Other visible labels: `presenter's 3rd POV camera` -> `3rd video (USB)` -> laptop; `network` -> `remote/cloud conferencing` -> `zoom`; `Hdmi/Video` [partly cut off at right edge]. The photo connects the phone **or** laptop to the on-site projector, and sketches the overhead camera/projective-overlay path separately.

![Editable redraw of the devices and two output paths](diagrams/physical-setup.svg)

### Processing sketch

Four numbered inputs converge on `Input fusion`:

1. `hand tracking`
2. `anchor / aruco tracking` (marker drawn as `id48`)
3. `body tracking (optional)`
4. `gesture incl. sEMG tracking (opt.)`

`Sound (double tap)` also feeds input fusion. The main path reads `Input fusion` -> `OART` -> `content matching` -> `Output` -> `slide control` -> `Hdmi/Video` [last label is at the cropped right edge]. Side inputs: `tracker ID & pose matching DB` -> `OART`; `content (ppt + pdf)` -> `content matching`. `OART` is left as written; its expansion is not given on the page.

![Editable redraw of the input-to-output pipeline](diagrams/processing-pipeline.svg)

### Challenges and timeline

The challenges list at the bottom is partly cramped:

1. `Midas Touch`
2. `[unclear] DB assets` [looks like `TIP DB assets`]
3. `Meta glass interaction` [reading uncertain]
4. `fallback GD scenario` [reading uncertain]
5. `Govern[... ]` [cut off / unclear]

The timeline appears to read:

| Marker | Handwritten milestone |
| --- | --- |
| `04 Dec` | `quick & dirty prototype` |
| `20 Dec` | `projection working` |
| `15 Jan` | `Camera & OART` [reading uncertain] |
| `31 Jan` | `Show Case` |

No year is shown for these milestone dates.

![Editable redraw of the tentative timeline](diagrams/timeline.svg)

## 2. Concept, clarified from the accompanying notes

The following is a cleaned description **from the presenter's explanation**, not a verbatim reading of the photograph. The same physical interaction model can serve two distinct scenarios:

| | XRT: augmented book | XRPPT: presentation control |
| --- | --- | --- |
| Physical target | Open book and nearby tabletop | Printed slide thumbnails and presenter notes |
| Camera | Overhead camera in the first prototype; glasses later | Smart-glasses camera(s), relayed by a phone or laptop |
| Compute | Heavier, local processing is acceptable | Prefer a lightweight, low-latency pipeline |
| Output | Projected dots, outlines, strokes, diagrams, or 3D content on the book/table | Keypress-like commands to advance or select prepared PPT/PDF slides on a room screen/projector |

For **XRT**, start with the existing top-down camera/projector rig. Because camera and projector are fixed together, the previously developed camera-to-projector homography can place visual overlays in the correct position on the book or adjacent table. A first interaction might be a red dot following the pointing finger; richer overlays could connect real sketches to projected diagrams or models.

For **XRPPT**, glasses provide video (and sound for a possible double-tap signal) to a phone or laptop. The compute device determines which printed page/region is in view, detects the pointing finger, decides whether an intentional selection occurred, and maps the selected region to a prepared slide command. An app to retransmit a given glasses' feed may be needed; compatibility with any particular model is not assumed.

### Recognition and interaction options

- **Page and region identity:** start with ArUco markers attached to pages if useful, but aim to register scanned/photographed pages and their regions of interest (ROIs). A hierarchical database and lightweight visual detectors (for example, small YOLO-style models) are a proposed direction, not yet a chosen implementation.
- **Pointing and intent:** MediaPipe hand tracking is an initial finger/point detector. A double tap on the ROI, detected acoustically and/or from hand motion, can confirm selection rather than treating every pointed-at region as a command.
- **Midas-touch guard:** separate a cheap idle-versus-active discriminator from more specialized page, gesture, and intent detectors; only invoke the latter when appropriate. Normal gesticulation must not trigger slide changes. Dynamic gestures and an optional myoband/sEMG channel are future extensions, not first-prototype dependencies.
- **Control mapping:** bind recognized page/ROI IDs to slide actions, then emit presentation keypresses. Keep recognition, intent confirmation, and slide control separate so an uncertain detection cannot directly fire an action.

The two flows share concepts (page/ROI identification, hand intent, content mapping) but have different outputs: **XRT adds projective XR to the physical surface; XRPPT drives an ordinary presentation without requiring projective XR on the notes.**
