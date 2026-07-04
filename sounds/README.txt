Game Clock — sound cues
=======================

Drop your audio clips in THIS folder, next to index.html's expectations.
The app looks for these four filenames by default:

  start.mp3      3-2-1 start countdown   (plays when you press Start)
  5min.mp3       "5 minutes remaining"   (fires as the clock hits 5:00)
  1min.mp3       "1 minute remaining"    (fires as the clock hits 1:00)
  30s.mp3        "30 seconds remaining"  (fires as the clock hits 0:30)
  10s.mp3        "10 seconds remaining"  (fires as the clock hits 0:10)
  gameover.mp3   game over / horn        (fires at 0:00)
  test.mp3       sound check             (plays via the "Test sound" button)

Notes
-----
- A green dot next to each cue in the "Sound cues" panel means the file
  loaded OK. Grey = not found.
- Any audio format your browser supports works (mp3, ogg, wav, m4a...).
  If you use a different name or format, either rename to the above, or
  use the per-row "Choose file" button to load a clip on the fly.
- To change the default filenames permanently, edit the DEFAULT_SRC block
  near the top of the <script> in index.html.
- Browsers block audio until you interact with the page, so the first
  Start tap unlocks sound. After that, all cues fire on their own.

This README is just a placeholder so the folder exists — you can delete it.
