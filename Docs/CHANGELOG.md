# Changelog

**Contents**

- [1.0.0 — 2026-10-02](#100--2026-10-02)
- [0.11.2 — 2026-10-02](#0112--2026-10-02)
- [0.11.1 — 2026-10-02](#0111--2026-10-02)
- [0.11.0 — 2026-10-02](#0110--2026-10-02)
- [0.10.0 — 2026-10-02](#0100--2026-10-02)
- [0.9.0 — 2026-10-02](#090--2026-10-02)
- [0.8.0 — 2026-10-02](#080--2026-10-02)
- [0.7.0 — 2026-10-02](#070--2026-10-02)
- [0.6.0 — 2026-10-02](#060--2026-10-02)
- [0.5.0 — 2026-10-02](#050--2026-10-02)
- [0.4.0 — 2026-10-02](#040--2026-10-02)
- [0.3.0 — 2026-10-02](#030--2026-10-02)
- [0.2.0 — 2026-10-02](#020--2026-10-02)
- [0.1.0 — 2026-10-01](#010--2026-10-01)

## 1.0.0 — 2026-10-02

Major: the first version to be published. The number is the owner's word ("bump to
v1.0.0"); against 0.11.2 the program removes its downloads after an installation.

- What is published is the skeleton `cleanup_project.cmd` leaves: the built window (`App\`,
  `Start.exe`), the code, the configs and the documents, 108 MB. Everything else - Python,
  PyTorch, TensorRT on an RTX card, the detector weights, FFmpeg - is installed by INSTALL
  WHAT IS NEEDED in the SETUP section; the .NET SDK is needed only to build the window.
- After a successful installation the pip cache and the downloaded archives
  (`install\download`, 3.6 GB) are removed - by the buttons of SETUP and by
  `builder_main.cmd install_all` alike; after a failure they stay, so that the next try does
  not download everything again. Nothing is cut out of what is installed: the owner's rule
  ("nothing is cut out of what is installed, except the cache and the installation files").
- Checked on this version: 73 engine tests, the translation table (456 texts), `verify`,
  `check_queue` (counted 3, skipped 1, failed 2, every count on TensorRT, nine guard
  checks). On a clean copy of the skeleton the window pressed INSTALL WHAT IS NEEDED by
  itself: 6.1 minutes, "everything is in place", the downloads gone, 11.5 GB installed;
  `verify` and `check_queue` passed there, and `install_all` run over it removed the
  downloads the same way.

## 0.11.2 — 2026-10-02

Patch: a TensorRT engine file that cannot be loaded is built again.

- The installed folder is carried to another machine as it is: the libraries are the same
  for every card, and the engine file in `models\` names the card and the TensorRT it was
  built with - on another card it is built anew by the first count (checked with a note
  that named another card: 30 seconds, counted on `tensorrt`).
- The gap in that: an engine whose note names this card and which still cannot be loaded
  (another driver, a damaged file) sent every count to torch, for good - the file stayed.
  Now it is built again, once; torch is the step down only when that fails too. Checked
  with half an engine file: one count, and four at once - all on `tensorrt`.

## 0.11.1 — 2026-10-02

Patch: the first queue on a fresh installation was counted on torch instead of TensorRT.

- Found on the copy that was cleaned and built anew. The queue starts four counts at once;
  on a folder without a TensorRT engine all four exported the same weights into the same
  file at the same moment, the file came out broken, TensorRT refused it in every one of
  them and each stepped down to `cuda` (five times slower, and not the backend the numbers
  are checked on). Now one process at a time makes the files of a model - the downloaded
  weights, the ONNX export, the engine (`build_turn` in `engine\detector.py`, a lock on
  `models\<weights>.lock` that Windows lets go of when its process ends); the others wait
  and take what was made. The engine file appears whole or not at all.
- Checked by four counts started at once on a folder with no engine, and on one without
  the weights: all four on `tensorrt`. Three tests of the turn itself (73 engine tests).
- `check_queue` asks `doctor` what this machine counts on and fails when a recording of the
  queue was counted on something else: the defect above passed the check unnoticed.
- `--shots` has the `setup` scenario: INSTALL WHAT IS NEEDED pressed in the window, for a
  cleaned copy of the folder. Run on the skeleton left by `cleanup_project.cmd`: the window
  installed the environment, the model and FFmpeg in six minutes and said "everything is in
  place"; `verify` and `check_queue` passed on what it installed.
- SETUP: the number under INSTALL WHAT IS NEEDED counts the model that goes along with the
  environment (it said two parts and installed three).

## 0.11.0 — 2026-10-02

Minor: the four findings of the audit of 0.10.0 closed; a recording that breaks off is no
longer taken for a counted one.

- AUD-002. A recording that cannot be read to its end was reported as counted, with the
  length its header promised (a test clip cut at 55 %: exit code 0, 113 frames of 200, the
  full length in the summary). Now the count compares the frames it read with the frames
  the file promised: short by up to a second or one per cent - the run is kept and marked
  (`complete` false in `summary.json`, a mark in RESULTS, a warning in the journal); short
  by more - nothing is written, the exit code is 1 and the reason comes as an `error` line.
  `--partial` counts what can be read. `seconds` is the length really read;
  `frames_expected` and `frames_read` stand beside it. The window takes a recording for
  counted only when the `done` line came and `summary.json` is there, and says the engine's
  reason when it did not.
- AUD-003. The rules of a placement were asked three different ways: the window, `recordings
  list` and the count each had their own. They are one table now (`placement_problem` in
  `engine\camera.py`), the count refuses whatever the table refuses (a line outside its zone
  was counted before), and the window holds the same table in the same order
  (`Camera.Broken`). `tests\placement_cases.json` is put to both.
- AUD-001. CAMERA: when another recording was chosen with unsaved lines, "save" was answered
  and the save failed, the window went on to the other recording and the edits were lost.
  It stays on the recording now, with the edits and the reason.
- AUD-004. `recordings add` looked for the lines of a recording beside the video first even
  when the project file is kept in a folder of the user; the window looked there first. Both
  look in the order of `KeepsakeMode` now.
- The checks that make each finding happen on purpose: 70 engine tests (27 before), the
  guard checks `05` and `rules`, a cut recording in the queue of `check_queue` - it must end
  with counted 3, skipped 1, failed 2.
- The header: a section button no longer has a least width. Four of them were wider than
  the piece they stand in, and the chosen one lay over the separator at its right.
- CAMERA: the round handle of the playback slider is a fifth smaller (16 against 20).
- The lists of formats in the documents name AV1 (read whole on a short test clip).

## 0.10.0 — 2026-10-02

Minor: one vehicle, one box; the COUNT section laid out anew.

- One vehicle, one box is the rule of the count now: overlapping boxes of different classes
  are not both kept. On the reference recording the near lines came from +6.8 % and +3.3 %
  of the manual count to +0.8 % (N10) and -0.1 % (P107); trucks on N10 from 101 to 68
  against 64 by hand. The counts of every recording change with it: a run made before is
  marked in RESULTS ("the earlier rule of boxes"), and `--boxes-per-class` counts the old
  way. The owner's choice after the comparison.
- COUNT: THE COUNT AS IT GOES stands first, with THIS MACHINE and HOW TO COUNT beside it,
  and the queue below; the queue is the only part of the page that scrolls. THIS MACHINE
  is a badge: the processor, the memory, the video card with its memory, the driver,
  Windows, and the engine - the choice of "Count on" while nothing is counted, the backend
  a recording is really counted on during a count. The note "now this is..." beside the
  chips is gone.
- The numbers of a count are tiles of one line, the name and the value with a bar between
  them; "VEHICLES IN ALL" is "TOTAL". The blocks are filled in, not made anew at every line
  of the engine.
- CAMERA: the lines and the zones scroll inside one card, and the two columns end on one
  line.
- Fixed: a queue started before the engine had answered what it counts on took the
  recordings one at a time; the count now waits for that answer.
- Fixed: under load the picture in CAMERA could stay the same when the recording was
  changed (a late frame of the old recording took the frame of the new one). The `browse`
  scenario of `--shots` checks it.
- Fixed: `install\Check-Machine.ps1` is ASCII again (a Cyrillic word broke its parsing
  under Windows PowerShell); it reports the processor, the memory and Windows as well.
- The engine reports the first frame at once and measures the speed from it.

## 0.9.0 — 2026-10-02

Minor: the TensorRT backend, and the speed settings moved into SETTINGS.

- Backend `tensorrt` for NVIDIA RTX cards: the model built into one TensorRT engine per zone
  size (full precision), the frame turned into the blob and the head sieved on the card,
  the suppression of boxes in numpy. On an RTX 5070 with `yolo26s` the reference recording
  of 29 minutes is counted in 5.2 minutes against 24.4 on torch (140 frames a second against
  30); the counts differ by one vehicle on a movement at most.
- `--device auto` on an NVIDIA card takes `tensorrt` when it is installed, else `cuda`. When
  the engine cannot be built, `auto` steps down to `cuda`, says so in the journal, and the
  summary of the run names the backend it was really counted on.
- Install: `install\Install-TensorRT.cmd` (the wheels of NVIDIA's package index and onnx,
  about 3 GB), the builder step `tensorrt` (24). `Check-Machine.ps1` decides by the card:
  an RTX card takes TensorRT, a GTX card counts on PyTorch with CUDA alone; `build_env`
  installs it where the check says so. `Verify.ps1` and `doctor` report it. SETUP has the
  TENSORRT button and marks it for the machine like the others.
- COUNT: the TENSORRT chip in "Count on"; the heads of runs name TensorRT.
- SETTINGS: the SPEED OF THE COUNT card - "Recordings at once", "Threads" and "Priority"
  moved there from COUNT (they are the work of the program, not a choice of a count).
- On TensorRT detector threads help: four by default (one - 102 frames a second, three -
  146, four - 153, six - 149, eight - 136), the counts the same at any number.
- The engine option `--one-box`: boxes of different classes that overlap are not both kept
  (one vehicle, one box). Off by default until it is checked against the reference.
- The `start` line of a count now comes after the models are made, so it names the backend
  the count really runs on.

## 0.8.0 — 2026-10-02

Minor: the SETTINGS section; a control of the window moved.

- A new permanent button at the bottom, SETTINGS, between SETUP and ABOUT: how the program
  itself works. The PROJECT FILE card (where the project file with the lines is kept) moved
  there from SETUP.
- SETUP keeps only what belongs to the backend: the machine, the installers, what is
  installed. The owner's division: "in SETUP only what belongs to the backend; what belongs
  to the work of the program - SETTINGS".
- The sections are now seven: Ctrl+1 … Ctrl+7; Esc leaves SETTINGS as it leaves SETUP and
  ABOUT.

## 0.7.0 — 2026-10-02

Minor: the queue counts several recordings at once on an NVIDIA card.

- COUNT: the "Recordings at once" spinner (1 to 8, four by default - the owner's number).
  On `cuda` the queue runs that many engine processes side by side; when one recording is
  through, the next of the queue takes its place. Other backends count one at a time, and
  the window says so beside the spinner.
- The count as it goes shows a block per recording being counted; the status line shows how
  many are through and the share of the whole queue.
- Measured on an RTX 5070 with `yolo26s` on one-minute pieces: one process 27 frames a
  second, two 51 over all, four 71, eight 80; the event tables of every piece were the same
  byte for byte at every level.
- The rules of the queue are unchanged: read again before every recording, unsaved lines
  written first, a failed or silent engine reported and passed, only STOP ends it.

## 0.6.0 — 2026-10-02

Minor: the total number of vehicles of a recording.

- The total of a recording: `summary.json` carries `total` (class -> vehicles, the sum over
  the movements, as the total column of a survey sheet) and `check`; the `progress` and
  `done` lines of a count carry it too. A run counted before this version has no total.
- A check line: a plain line marked `"check": true` (the CHECK LINE button in its row in
  CAMERA) counts a flow other lines count further on; it is shown in the results and left
  out of the total. Of a gate only the entry is in the total.
- RESULTS: the TOTAL row under the table of a run and under the runs side by side (with the
  difference from the manual total when the recording has a reference); a check line is
  named as one in its row; the list of runs shows the vehicles of a run.
- COUNT: the VEHICLES IN ALL tile in the count as it goes.
- `main.py compare` prints the total against the manual count and gives it as `total` in
  its JSON line.
- The first verification on an NVIDIA card (RTX 5070, driver 591.86): installation, the
  queue and the guard checks on `cuda`, the reference recording against the manual count;
  the `cuda` baseline is saved in `reference\mira-zaozernaya-02\`.
- The reference lines file: the ends of three lines are swapped so that the arrow of each
  shows its main flow (`P107` away from the camera, `N13` and `N9` to the right), and the
  near line `P107` is marked as a check line. The counts did not move.

## 0.5.0 — 2026-10-02

Minor: the project file beside the recordings, and the four findings of the audit of 0.4.0.

- The project file: the lines, gates and zones of every recording are also kept in
  `audion-traffic-counter.json` in the folder of the videos, by the fingerprint of the
  file. A recording added again, from another folder or on another machine, gets its lines
  back by itself. SETUP has the choice of the place: beside the recordings (the default) or
  one file in a folder of the user.
- Audit ATC-01: lines that cannot be saved keep their recording out of the count; it was
  counted by its old file while the editor showed other lines.
- Audit ATC-02: the run's `camera.json` is made before the count and the engine counts by
  that copy; it was copied at the end and could hold lines saved during the count.
- Audit ATC-03: closing the window writes unsaved lines, and asks before closing without
  lines that cannot be saved; they were lost in silence.
- Audit ATC-04: the queue is read again before every recording, so a mark changed during a
  count takes a recording out of the queue or into it; the window showed one queue and
  counted another.
- `builder_main.cmd check_queue` makes each of those four happen on purpose and checks the
  project file (the "guard" scenario).

## 0.4.0 — 2026-10-02

Minor: a fallback backend, so the program counts on a machine without CUDA.

- Backend `directml`: the model exported to ONNX and run by ONNX Runtime with the DirectML
  provider, on any DirectX 12 video card (Intel, AMD, NVIDIA).
- `--device auto` now picks by reliability: `cuda`, `directml`, `openvino-gpu`,
  `openvino-cpu`, `cpu`. OpenVINO stays as the last resort: the least reliable for a queue
  of many recordings.
- COUNT: a row of buttons "Count on" (AUTO, CUDA, DIRECTML, OPENVINO, PROCESSOR); what the
  machine has not got is dimmed; beside it, what AUTO means on this machine.
- Install: `install\Install-DirectML.cmd`, the builder step `directml` (23), the DIRECTML
  button in SETUP. The environment of a machine without an NVIDIA card gets DirectML in
  place of OpenVINO; OpenVINO is installed only by its own step or button.
- `doctor` reports `onnxruntime`, `directml` and `backends`.
- RESULTS: the head of a run's column names what it was counted on.
- The engine can write where its threads stand (`AUDION_ENGINE_STACKS`).
- A new program icon: the camera frame, the counting line with its arrow, a car.
- Documents for people: `README.md`, `Docs\README_RU.md`, `Docs\README_EN.md` and the user
  guides `Docs\USER_GUIDE_RU.md`, `Docs\USER_GUIDE_EN.md`; F1 opens the guide.
- The builder: `check_queue` (15) - the window counts a queue of test recordings by itself;
  the menu headings in capitals. `main.py recordings add --lines NAME`.
- The check against a manual count takes movements of any name (it failed on a name that
  was not a letter followed by digits).

## 0.3.0 — 2026-10-02

Minor: the window is rebuilt around the owner's case - lines and gates placed on two hundred
recordings one after another, then the queue counts by itself overnight.

- Every recording has a placement of its own (`config\cameras\<recording>.json`). The list
  of cameras and NEW CAMERA are gone: a recording without lines is drawn on at once.
  AS THE PREVIOUS ONE copies the lines of the recording above into this one's own file.
- A new recording is no longer given the lines of another one because the frame size
  matched: it comes without lines and is marked so in RECORDINGS and on the START button.
- CAMERA: previous / next recording under the video (Page Up / Page Down); going on saves
  the edits; the name of the recording and its place in the list stand above the video.
- A line counts one way, along its arrow. The arrow follows the hand that drew the line and
  is turned by two arrow buttons in the row of the line (in place of the two name fields and
  the swap button); a name for the oncoming flow is optional. The arrow on the frame is
  bolder, and the name of the movement stands beyond its head.
- The queue never stops by itself: a recording without lines is skipped, a recording the
  engine failed on is reported and the next one starts, an engine silent for 15 minutes is
  stopped and the next one starts; unsaved lines are written before the count; the last
  line says counted / skipped / failed. Windows is kept from idle sleep while it runs.
- Engine: the OpenVINO infer requests are created before the first frame
  (`Detector.prepare`). Created lazily by the worker threads, they now and then left the
  engine hung at the start on Intel graphics.
- Lines can be placed without the window, by an agent, for the owner to review:
  `main.py recordings add | list` and `main.py frames` (the middle frame of every recording
  with a coordinate grid, and with its lines drawn). A file `config\cameras\<id>.json` is
  the lines of the recording of that id. The window opens a recording on its middle frame.
- COUNT: the card says the count is starting while the model loads (it said nothing was
  being counted).
- ABOUT: the Audion mark.

## 0.2.0 — 2026-10-02

Minor: a new capability of the window, and the place of the results changed with it.

- History of runs. Every count of a recording is kept as a run of its own in
  `output\<recording>\<yyyyMMdd-HHmmss>_<model>\` (`events.csv`, `summary.json`, the camera
  file the run was counted with) instead of one result per recording that the next count
  replaced. A cancelled count leaves no folder. `summary.json` carries `finished`.
- RESULTS: the list of runs of the recording (date, model, backend, time, crossings; a run
  can be deleted); on the left the chosen run by movement and class and its manoeuvres; on
  the right, as a card of its own, the runs side by side - a column per run, the manual
  count when the recording has a reference, the difference in percent in colour.
- `main.py compare` takes several summaries at once (`--summary a b c`).
- COUNT: the model chips carry the Ultralytics names (YOLO26N, S, M, L, X) in place of
  БЫСТРО / ОБЫЧНО / ТОЧНЕЕ; each has a detailed tooltip: parameters, size of the weights,
  mAP on COCO, when to take it.
- Every tooltip of the window comes after 1200 ms (was 1500).
- `--shots`: `AUDION_SHOT_TIP` takes a picture with a tooltip open; `tips.txt` lists the
  delay of every tooltip.

## 0.1.0 — 2026-10-01

The first version: nothing was released before it.

- The counting engine (`system_core\`): detector and tracker, zones with enlargement, lines with
  two named directions, gates and manoeuvres, backends `cuda` / `openvino` / `cpu`, the commands
  `doctor`, `count`, `trails`, `compare`.
- The window (`Source\`, Avalonia 12): sections RECORDINGS, CAMERA, COUNT, RESULTS, SETUP, ABOUT;
  Russian and English; dark and light; the video with lines, gates and zones drawn and stretched
  with the mouse; FFplay with the lines over the video.
- The builder (`builder_main.cmd`), the launcher (`Start.exe`), the installers of the engine
  runtime and of portable FFmpeg.
- Installation by machine: `check_machine` reads the video cards and the NVIDIA driver, the
  FFmpeg release follows the driver, `install_all` installs a clean folder; the SETUP section
  marks in green only what this machine needs and installs it with one button.
- `cleanup_project.cmd`: the folder down to the published skeleton.
