# Screencast tours

How Integral Ed makes the short case-study videos that play from a microsite's case cards: what a
tour contains, how it looks, how it is recorded and voiced, and what gets delivered. Written after
the first three tours for integraldataservices.com (October 2026) so the next microsite can start
from the same format.

The video code lives in the private Demo Portfolio repo (`IntegralEd/DemoPortfolio`, folder
`video/`). That repo's `video/README.md` and `video/STYLE.md` cover the rendering tools in depth.
This guide covers the format and the rules.

## What a tour is

A tour is a 60–90 second video about one case. It moves through five beats: the client's
situation, the manual work before, what we built, the product in use, and the result. Each scene
is an HTML page rendered frame by frame in headless Chrome and encoded with ffmpeg, so there is no
editing software involved. Text stays sharp, every frame can be reproduced, and a change is just
a re-render.

The live product is always a **synthetic copy** of the real app or sheet, loaded with an invented
world. Real client data never appears on screen.

## Who does what

| Who | Owns |
| --- | --- |
| **Page session** (the session that owns the microsite page) | `src/index.html`, site CSS and JS, the build. Reviews the video PR, merges it and wires the assets into the case card. |
| **Video session** | The synthetic world, the demo build, the recording, the scenes, the cuts, and the deliverables under `src/assets/cases/<case>/` plus `docs/video/manifest.json`. Works on its own branch (`video/tours`) and never edits the page, the site CSS or `vendor/`. |
| **David** | Facts about the case, review notes on each cut, the voiceover read, and the YouTube upload. Signs in to the recording profile and approves any OAuth consent himself. |

Questions for David go in the microsite's `docs/video/QUESTIONS.md` as a checklist. Each question
carries a recommendation, and work continues on everything the question doesn't block.

## The deliverable

| Item | Spec | Where |
| --- | --- | --- |
| Final video | 1920×1080, 60 fps, H.264, about −16 LUFS; uploaded unlisted until launch, with `tour.vtt` as its captions | YouTube; the manifest gets `youtubeId` |
| Masters | Two renders of the same cut (see "Final render") | The local video project's `out/`, never git |
| `poster.jpg` | 1600×900; a cursor-free frame of the live product inside its device frame | `src/assets/cases/<case>/` |
| Stills 1–3 | 1600×900, AVIF + WebP + PNG, made from lossless 2x captures (not video frames) | `src/assets/cases/<case>/still-N.*` |
| `tour.vtt` | WebVTT built from the caption windows, in American spelling | `src/assets/cases/<case>/` |
| Manifest entry | See the example at the end | `docs/video/manifest.json` |
| As-built script | A "Final cut" table in the tour's script doc: time, scene, what's on screen, caption, and the VO as recorded | `docs/video/tour-NN-<case>.md` |

Video files don't go in the microsite repo. The page plays YouTube through the theme's
`popout-frame` modal (`data-youtube` on the card's button).

## The five-beat format

Target about 80–90 seconds with the voiceover, 90 at most. David may keep a longer read when the
story needs it (one tour ran 2:07).

| # | Beat | Length | What's on screen |
| --- | --- | --- | --- |
| 1 | **Context** | 15–25 s | Who the client is (described by type, never named) and what we audited. A diagram that builds with the words: a map of sites, the sources, the vendor, the drop. |
| 2 | **Before** | 6–15 s | The manual task shown concretely and slowly: values copied one by one, files filtered and emailed, notes read and retyped. A **BY HAND** clock in hours (or "hrs/wk"). Stamp the stale output ("Snapshot", "Out of date by tomorrow"). |
| 3 | **After** | 10–25 s | The new pipeline. Each real tool appears as a labeled glyph (Drive, Apps Script, PostgreSQL, IMPORTRANGE…), and any manual step left over is drawn in gray. An **AUTOMATED** clock runs, or, when nothing is scheduled, a plain "Live · refreshes within minutes" badge. Don't invent a timer the system doesn't have. Ends by pushing into the device. |
| 4 | **Live product** | 25–45 s, 2–5 beats | The screencast inside the device frame. The AUTOMATED clock lands in green when the product updates, then leaves. One idea per beat: pick, filter, compare, export. |
| 5 | **Result** | 12–20 s | The cream end card: a headline, three stats, the closing question, and the Integral Ed mark. |

### Story rules

- **Show only what the product does.** Check the code or the sheet before you script a feature. If
  the case card claims something the build doesn't have, raise it as a question. It stays out of
  the footage and out of the VO.
- **Every VO line needs a picture that moves on its words.** If the picture has nothing to show,
  the line goes.
- **Show the data's real journey** (sources → vendor → drop → pipeline) before the tool.
- **No website screengrabs inside a case tour.** They read as an ad break.
- **Name what's different about this build.** Say it in the After beat when one tour uses a
  scheduled script and another uses spreadsheet formulas alone.
- **Get the facts from David.** Before you script, ask him for:
  - who did the manual work, how long it took, and how often;
  - what replaced it;
  - the KPI;
  - the result in numbers.
- **Write numbers out in the VO** ("forty-five thousand dollars") so the voice says what the screen
  shows.
- **The tour works with the sound off.** The captions and end card carry the story.

## Look

The tours follow the microsite's own theme: its tokens (`vendor/integralthemes/theme/tokens.css`
plus the site's overrides), its font, and its colors. These rules carry across sites:

- **Canvas:** 1920×1080, white. Keep text 110–160 px from the sides. Keep the top and bottom 60 px
  clear.
- **Glyphs:**
  - Single-color, always the primary plum (`#600b68`). Gray (`#a6a3ad`) is only for structure:
    idle pipes, the before's documents, rules.
  - Tools appear as single-color glyphs labeled with the product name, never as full-color logos.
  - Use Noun Project icons (Integral Ed has a Pro license, so no attribution is needed). Record
    their ids in the manifest. Draw a missing glyph in the same line style.
- **Captions:**
  - Lower third, white text on a plum pill, in the same position in every tour.
  - Keep them short (six words or fewer) unless David writes a longer one.
  - One caption per idea, timed to the VO line it belongs to.
- **Device frames:** the house CSS frames, `laptop3d` and `tablet3d`, not PNG frames.
  - Google Sheets and other desktop tools go on the **laptop**.
  - Responsive web apps go on the **tablet**, with at most one phone insert.
  - Record app screens at 1440×810 CSS pixels at 2x, so they map 1:1 onto the frame's screen.
- **Motion:**
  - Simple transforms only.
  - Ease in and out for anything that travels or zooms; linear for flow.
  - Nothing bounces.
  - Holds drift slightly so a frame never looks frozen.
  - Scenes fade through the background over 0.35 s.
  - Numbers count up over 1.2–1.6 s.
- **End card:**
  - Cream background (`#F3EEE8`) with plum text and the plum Integral Ed mark. On a plum
    background, the mark sits on a white disc, because its ∫ is cut out.
  - The result glyph, three stats, and the closing question as the last line ("What could your
    team do with…?").
- **Text:** American spelling everywhere (color, gray, analyze, program) and no em dashes. That
  covers captions, VO, on-screen UI in the synthetic build, `.vtt` files and the manifest.

## Synthetic data (non-negotiable)

- **Never record real data.**
  - Rebuild the app or sheet with an invented world from a seeded generator (`data/gen_world.*`
    in the video project), so it reruns identically.
  - Don't blur. Blur is reversible and looks worse. If something can't be rebuilt, cover it with
    a solid overlay and flag it for review.
- **No client identity anywhere.** No client names, logos, codenames, staff names, region or
  school names, URLs, base or table ids, or Drive ids. That applies to the UI, captions, VO,
  posters, stills, file names, the manifest and commit messages.
  - Recolor a real app to a neutral palette.
  - Relabel placeholders that leak meaning.
  - The client is described by type only ("a national charter network", "a statewide college
    advising program").
- **Check every invented name against the client's own lists** (alias lists, rosters, region
  names) before it goes on screen. Generic-sounding names collide: one tour's first set of
  regions matched a client's real regions.
- **Keep numbers plausible:**
  - realistic ranges;
  - small, mixed year-over-year changes (some down);
  - generic comments with no names or places;
  - broad demographic categories only.
- **Real apps:**
  - Run the app's own code locally against fixtures generated from the world.
  - Never use the production URL or production credentials, and never read a `.env` file.
  - Copy only source and config into the gitignored `work/`.
- **Google Sheets:**
  - Make copies of the templates in the recording account's Drive, outside any client folder.
  - Clear the data cells and refill them through the Sheets UI.
  - Replace real names in titles and labels with Find and replace.
  - Template bugs found along the way are fixed only in the demo copies and logged for David.
- **Client files are for structure only.** Read them for structure, delete any raw export once
  the structure is understood, and never commit them or put them on screen.
- **Before a cut leaves the machine,** search every capture's page text for the client's names.

## Recording the live product

- **Profile:** a dedicated Chrome profile outside any repo, launched with a remote-debugging port.
  - It is signed in to the shared Integral Ed recording account, not a personal one. David signs
    it in.
  - No extensions, no bookmarks bar, notifications off.
  - Claude never types credentials or approves consent screens.
- **Frame-per-pose capture** (`sheet/rec.mjs`):
  - Drive the real UI over CDP with Playwright and capture one 2x frame per pose.
  - Video time advances only on capture, so menus, loading and formula recalculation cost no
    screen time.
  - The recorder writes the cursor path, clicks and named marks to `track.json`. The cursor,
    halo, click ripple, highlight boxes and camera moves are drawn in the comp, so their style
    can change without re-recording.
- **Tall viewports for row-scrolled apps.** Google Sheets scrolls by whole rows, so record in a
  tall viewport (for example 1700 px) and pan the grid band in the comp, with the app chrome
  pinned above and the tab bar below. Measure pan offsets on the tall frame; don't compute them.
- **Build demos through the UI.** Open the template's `/copy` URL, navigate with the Name Box,
  paste from the clipboard, use Edit → Find and replace, and fill down.
  - Use no Apps Script and no extra OAuth unless the tour is about Apps Script.
  - When it is, narrow the scopes in `appsscript.json`. After recording, revoke the script's
    access and delete its triggers.
- **Google Sheets gotchas:**
  - Hiding the formula bar hides the Name Box too.
  - Find and replace's scope defaults to a specific range. Set it to "All sheets", tick "Also
    search within formulas", and confirm the extra OK.
  - IMPORTRANGE's "Allow access" button has a hidden duplicate; click the visible one.
  - Pasting trims trailing spaces, so lists must match exactly.
  - Navigating with the Name Box unhides tabs; re-hide them before recording.
- **`RECORDING.md` in every project:** setup, the hard rules, and a cleanup checklist. Cleanup
  means closing the recording Chrome, stopping local servers, revoking script access and
  triggers, and trashing any copy that touched real data.

## Voiceover

1. **Script.** All VO lines live in `plan.json`. Send them to David as an editable Word file
   (`node vo/vo-docx.mjs`). Each line gets its id, start time, and what's on screen, under
   recording notes:
   - read each line once, then stay silent for two seconds;
   - re-read a flubbed line after a pause (the last complete read wins);
   - read at a relaxed pace, because the edit tightens it.
2. **Scratch voice.** Until the read arrives, build and time the picture with macOS `say`
   (`vo/scratch.sh`).
3. **Cut.** Put the take in `vo/takes/` (gitignored).
   - Find the lines with ffmpeg `silencedetect` (−38 dB, 0.35 s) and transcribe each segment
     with whisper.cpp (`ggml-base.en`).
   - Mark the keeper spans in `vo/cuts.json` and run `vo/cut.py` at `TEMPO=1.08 PAUSE_MIN=0.3
     PAUSE_KEEP=0.2`. At 1.2× the read sounds rushed; at 1.05× with 0.45 s pauses it drags.
   - Transcribe every cut line again to confirm it's complete.
4. **Re-time the picture to the read** with `node timing.mjs`.
   - Clip frames anchor to VO lines, and motion-graphic beats cue as fractions of a line's
     length (`meta.<id>_d`), so they survive tempo changes.
   - Split a line into two ids when its sentences need different pictures.
   - The as-recorded lines replace the scripted ones in the docs.
5. **Check the mix.** The render normalizes the VO to −16 LUFS. Allow no silence longer than
   1.5 s except the end hold.

## Review loop

- **Review cuts** are numbered (v1, v2…), rendered at 30 fps with scene tags on, and sent with a
  contact sheet (`node render.mjs contact`).
- **Render a still before rendering motion.** Most problems are layout problems, and a still takes
  two seconds.
- **Before any cut goes out, check:**
  - the contact sheet, top to bottom;
  - the silence check;
  - the client-name search;
  - spelling;
  - nothing poking out from behind a device frame (labels placed under a laptop have shown
    through);
  - the sound-off test.
- **David's notes** become fixes and the next numbered cut.

## Final render and delivery

```bash
node render.mjs all --clean --fps60            # <slug>_clean_60fps_vo.mp4        captions burned in
node render.mjs all --clean --nocap --fps60    # <slug>_clean_nocap_60fps_vo.mp4  no captions: the YouTube upload
```

- **The two masters are the same cut.**
  - `--clean` hides the review tags.
  - `--nocap` leaves the captions out, because YouTube shows them from `tour.vtt`.
  - The captioned master is for places that can't load a captions track.
  - Both are H.264 at CRF 15, about 15–25 MB each, and stay off git.
- **Poster:** a cursor-free still (`--nocur`) of the live product in its frame, at 1600×900.
- **Stills:** capture the app as lossless 2x PNGs and compose them to 1600×900. Then encode AVIF
  (SVT-AV1, CRF 30) and WebP (quality 0.9), and keep the PNG as the fallback.
- **Captions file:** `node vtt.mjs` writes `tour.vtt` from the same caption windows the scenes
  use.
- **PR:**
  - Open it from `video/tours` in the microsite repo: deliverables, manifest entry and the
    as-built script table.
  - Merge `main` in first, and keep other sessions' sections of `QUESTIONS.md` and the manifest.
  - The page session reviews, merges and wires the card.
- **YouTube:** David uploads the `_nocap` master unlisted with `tour.vtt`. The `youtubeId` then
  goes into the manifest.
- **Code:** the video project's code (no media) is pushed to Demo Portfolio.

### Manifest entry

```json
{
  "id": "<case>",
  "title": "<case card title>",
  "device": "laptop",
  "tour": {
    "youtubeId": "<id>",
    "youtube": "https://youtu.be/<id>",
    "hosted": "youtube",
    "poster": "assets/cases/<case>/poster.jpg",
    "vtt": "assets/cases/<case>/tour.vtt",
    "seconds": 88.1,
    "master": {
      "clean": "<slug>_clean_nocap_60fps_vo.mp4",
      "captioned": "<slug>_clean_60fps_vo.mp4",
      "where": "Demo Portfolio video/projects/<slug>/out/ (gitignored); 1920x1080, 60 fps, H.264"
    }
  },
  "stills": ["assets/cases/<case>/still-1", "assets/cases/<case>/still-2", "assets/cases/<case>/still-3"],
  "synthetic": true,
  "source": "staging",
  "frame": "css-laptop3d",
  "vo": "<reader>, take of <date> (last complete read of each line), 1.08x, pauses cut to 0.2 s",
  "glyphCredits": { "nounProject": ["<id> <name>"], "license": "Noun Project Pro (no attribution needed)", "drawn": [] },
  "notes": "<the synthetic world in one paragraph; where the code lives>"
}
```

## Starting a tour for a new microsite

1. **Docs.** In the microsite repo, add `docs/video/` with:
   - a brief (deliverables, page wiring, the site's tokens);
   - one script per tour (`tour-NN-<case>.md`), using the five beats;
   - `QUESTIONS.md`;
   - `manifest.json`.
2. **Facts.** Interview David for the case facts (see the story rules) before scripting.
3. **Project.** In Demo Portfolio, copy the finished project closest to the new tour into
   `video/projects/<site>-<case>/`:
   - `ids-advising-dashboard`: Google Sheets with formulas only;
   - `ids-map-tracker`: Google Sheets with Apps Script;
   - `ids-survey-dashboard`: a web app run locally on fixtures.

   Copying a finished tour carries over the clocks, pipeline, end card, recorder and VO tools.
4. **Build.** Generate the synthetic world and check its names, then build the demo and write
   `RECORDING.md`.
5. **First cut.** Record the clips, build the scenes with scratch VO, and send v1 with a
   contact sheet.
6. **Voiceover.** Send the VO script as a Word file, cut David's read, re-time the picture, and
   run review cuts until he signs off.
7. **Deliver.** Render the final, deliver the assets, run the cleanup checklist, and record the
   final cut in the tour's script doc.
