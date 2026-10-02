# Audion Traffic Counter — user guide

**Contents**

- [What you need](#what-you-need)
- [Starting and installing](#starting-and-installing)
- [How the window is laid out](#how-the-window-is-laid-out)
- [RECORDINGS](#recordings)
- [CAMERA: lines, gates, zones](#camera-lines-gates-zones)
- [COUNT](#count)
- [RESULTS](#results)
- [SETTINGS](#settings)
- [Keys](#keys)
- [What accuracy to expect](#what-accuracy-to-expect)
- [Where things are](#where-things-are)
- [What the program does on the network and on the machine](#what-the-program-does-on-the-network-and-on-the-machine)
- [If something goes wrong](#if-something-goes-wrong)

The program counts vehicles in recordings of ordinary cameras: how many cars, buses, trucks
and motorcycles made each movement. Counting lines are drawn on the frame, the recording
goes into a queue, and the result is a table by movement and class plus a file of events,
one per vehicle.

The count runs on your machine. Recordings are not sent anywhere.

## What you need

- Windows 10 or 11, 64-bit.
- An NVIDIA video card of the RTX series, driver 570 or newer, is the main way to count:
  through TensorRT a half-hour recording of 640x480 takes about five minutes on an RTX 5070.
  An NVIDIA GTX card counts through PyTorch, several times slower. Without NVIDIA the program
  counts through DirectML on any DirectX 12 video card (Intel, AMD): on integrated Intel
  graphics a half-hour recording takes about an hour and a half.
- The internet during installation, and disk space: about 5 GB on a machine without NVIDIA;
  about 15 GB on a machine with an RTX card (PyTorch and TensorRT are 3 GB downloads each).

## Starting and installing

Unpack the program folder anywhere and start `Start.exe`. The program is portable: it keeps
everything of its own in this folder and installs nothing into the system.

At the first start the counting engine is not there yet. Open the **SETUP** section (bottom
left):

1. The **THIS MACHINE** card shows the video cards and what follows from them.
2. Press **INSTALL WHAT IS NEEDED**. Green marks exactly what this machine lacks: its own
   Python, PyTorch for your video card, TensorRT for an RTX card, the detection library,
   FFmpeg.
3. The installation is shown in the journal (the **JOURNAL** button, bottom right). It can
   be stopped with STOP and started again.
4. When everything is installed, the program removes the downloaded installation files by
   itself: on a machine with an RTX card the program folder then takes about 11.5 GB.

The installed folder can be carried whole to another machine with an NVIDIA card: nothing
has to be installed again. On another video card the first count starts half a minute
later - the program builds its fast engine for that card. On a machine without an NVIDIA
card open SETUP: it shows in green what that machine lacks.

The other buttons of the section repair one part. TENSORRT is the fast count on an RTX card:
on a machine with one it is installed together with the environment. DIRECTML and OPENVINO
are spare ways to count; install OPENVINO only as the last resort: it is the least reliable
for a queue of many recordings.

SETUP holds only what is installed for the counting engine. How the program itself works is
chosen in the **SETTINGS** section.

If the NVIDIA driver is too old, the section says so and asks you to update it.

### The project file

The program keeps the lines of every recording in its own folder and, as a second copy, in
the project file `audion-traffic-counter.json`. By default it lies in the folder of the
videos themselves. A recording is known there by the fingerprint of its file, not by its
name, so the placement comes back by itself: move the folder of recordings to another
machine, install the program anew, add a recording again - the lines are there.

The **PROJECT FILE** card of SETTINGS chooses the place: **BESIDE THE RECORDINGS** or
**IN THE USER'S FOLDER** - one file for all recordings in the chosen folder. The second is
for recordings that lie on a read-only disc or in somebody else's folder.

## How the window is laid out

- **At the top** - the four sections of the work, in order: RECORDINGS, CAMERA, COUNT,
  RESULTS. Under the name of each is its state: how many recordings, how many of them
  without lines, what is queued.
- **Bottom left** - SETUP, SETTINGS and ABOUT; **bottom right** - the **COUNT** button,
  which becomes STOP while a count runs. SETUP is what is installed for the counting engine
  (Python, PyTorch, the model, FFmpeg); SETTINGS is how the program itself works.
- Top right - the language (RU | EN), the dark and the light theme, the font size.
- Every button and field has a detailed tooltip: rest the mouse on it.

## RECORDINGS

**ADD FILES…** and **ADD A FOLDER…** put videos on the list. The files stay where they are:
the program remembers the path and copies nothing.

The list takes `.avi`, `.mp4`, `.mov`, `.mkv`, `.mts`, `.m2ts`, `.mpg`, `.wmv`, `.ts` and
`.webm` files; ADD A FOLDER passes by files with other extensions. The codecs are the usual
ones: H.264, H.265, AV1, MJPEG, MPEG-2, MPEG-4. If a recording of a rare format shows in CAMERA
and its count "failed", the counting engine could not read it: re-encode it to MP4 (H.264).
Make an interlaced recording (DV 720x576) progressive before it is counted.

A row shows the frame size and the length, whether the lines are placed, and how many times
the recording was counted. The mark on the left takes the recording into the queue. A click
on a row chooses the recording for CAMERA and RESULTS.

A new recording comes without lines.

## CAMERA: lines, gates, zones

On the left is the video of the chosen recording, on the right its placement. Every
recording has a placement of its own: an edit on one recording does not change the others.

### A line

Take the **LINE** tool, press on the frame and drag the line across the road.

- A line counts the vehicles that cross it **along its arrow**. The arrow stands in the
  middle of the line. The move of the mouse sets it: drawn bottom to top - those going right
  are counted, top to bottom - left, right to left - away from the camera, left to right -
  towards the camera.
- The two arrow buttons in the row of the line, on the right, turn the arrow round.
- Type into **Movement** the name these vehicles will bear in the results.
- **Oncoming** is usually empty. With a name in it the same line counts the oncoming flow
  as well, as a number of its own.
- The **CHECK LINE** button is for a line that counts a flow other lines count further on:
  the whole flow before a fork, for example, with a line on each ramp behind it. The numbers
  of such a line are shown in the results and left out of the TOTAL row - the same vehicles
  would be counted twice otherwise.

Put a line where vehicles are large and do not hide one another: not on a stop line, not
behind a pole and not at the very edge of the frame. A vehicle is counted once per pass.

### A gate

Gates are for an intersection and a roundabout, when it matters **from where and to where**
a vehicle went. With the **GATE** tool draw a line across every branch - over its entry and
exit lanes together - and name the branch. From the gates the program pairs the entry and
the exit of one vehicle into a manoeuvre "A → B": left, straight on, right, a U-turn.

A straight road needs no gates - lines are enough.

### A zone

Far vehicles take a few pixels, and the program misses them. With the **ZONE** tool draw a
rectangle around the far part of the frame and choose the enlargement, ×2 or ×3: the zone is
counted on its own, enlarged. A line that lies in the zone is given to it (the "Zone" switch
in the row of the line). Enlargement slows the count down.

### Editing and going through the recordings

- The **POINTER** tool: an end of a line is dragged, the line itself is moved by its middle,
  a zone by a corner or an edge. Delete removes what is chosen.
- Under the video: the previous recording, a frame back, play, a frame forward, the next
  recording. Going to a neighbouring recording saves the edits by itself; so do closing the
  program and starting a count.
- **AS THE PREVIOUS ONE** copies the lines, gates and zones of the recording above in the
  list. Handy for recordings of one shoot from one spot: copy and, if the frame has shifted,
  nudge the ends.
- **VIDEO IN FFPLAY** opens the recording in a large window of its own with the lines drawn.

This is how all recordings are gone through in a row: draw the lines, go to the next one.

## COUNT

At the top left is **THE COUNT AS IT GOES**: how many vehicles are counted so far, the speed
and the time left. When several recordings are counted at once, each has a row of numbers of
its own. On the right are **THIS MACHINE** and **HOW TO COUNT**, below them the **QUEUE**; a
long queue scrolls by itself, the rest stays where it is.

**THIS MACHINE** is what the count runs on: the processor, the memory, the video card, its
driver, Windows. The **ENGINE** tile shows what the program counts with: while nothing is
counted - the choice of "Count on" (AUTO); during a count - what the recording is really
counted on, TensorRT for example.

The **HOW TO COUNT** card:

- **Model** - from YOLO26N (the fastest) to YOLO26X (the most accurate and the slowest). A
  larger model sees far and partly hidden vehicles better. The weights are downloaded by
  themselves at the first count.
- **Count on** - AUTO chooses by itself: an NVIDIA card (TensorRT when it is installed, else
  CUDA), otherwise DirectML. What the machine has not got is dimmed.

How many recordings to count at once, the threads and the priority of the count are in the
SETTINGS section.

**TRIAL RUN** counts the first minute of the chosen recording and saves frames with boxes
and lines. Look at them before a count that takes hours: the boxes must stand on vehicles,
the lines across the flows.

**COUNT** counts all marked recordings in order - several at once on an NVIDIA card, and
then every recording has a row of numbers of its own in **THE COUNT AS IT GOES**. The queue
can be left for the night:

- a recording without lines is skipped;
- a recording the count failed on is skipped too - the rest are counted;
- a recording that breaks off (a damaged or cut file) is not taken for a counted one: its
  count "failed", and the journal says why. When no more than a second (or one per cent of
  the recording) is missing at the end, the run is kept and marked in RESULTS as not read to the end;
- the marks in RECORDINGS can be changed while a count runs: a recording the queue has not
  reached yet leaves it or joins it;
- while the count runs the computer will not fall asleep by itself (a closed laptop lid can
  still put it to sleep);
- at the end the bottom line says how many were counted, skipped and failed.

## RESULTS

Every count of a recording is kept as a **run** of its own and does not replace the earlier
ones.

- **RUNS** - the list: when, with which model, on what, how long, how many vehicles.
- **THE RUN** - the table of the chosen run: movements in rows, classes in columns; below
  it, the manoeuvres through gates. The last row, **TOTAL**, is the number of vehicles of the
  whole recording: the sum over all movements for each class and for all of them, like the
  total column of a survey sheet. Check lines are left out of it; of a gate only the entry
  is counted.
- **RUNS SIDE BY SIDE** - the runs as columns: what a change of the model or of the lines
  has moved. The last row is the TOTAL of every run.

While a count runs, the number of vehicles so far stands in the **THE COUNT AS IT GOES**
card of COUNT - the **TOTAL** tile before the tiles of the movements.

A run counted by an earlier version of the program has no TOTAL row: count the recording
again.
- **TABLE OF EVENTS** opens the file where every row is one crossing: frame, time, movement,
  class, line. **RUN FOLDER** opens the folder with the files.

### Checking against a manual count

If the recording was once counted by hand, the program shows the difference in percent
beside every number. Put a file `reference\<recording name>\<recording name>.json`, where
the recording name is the name of its file without the extension (for `DSCN0002.AVI` it is
`reference\DSCN0002\DSCN0002.json`):

```json
{
  "engine_classes": { "car": [0], "bus": [1], "truck": [2, 3] },
  "counts": {
    "N1": [120, 4, 9, 2],
    "N2": [87, 2, 5, 0]
  }
}
```

`counts` holds a row of numbers per movement, in the order of the columns of your sheet.
`engine_classes` says which columns add up into the classes of the program: `car`, `bus`,
`truck`.

## SETTINGS

How the program itself works (the button at the bottom left, between SETUP and ABOUT).

The **SPEED OF THE COUNT** card - these settings do not change the counts, only the time:

- **Recordings at once** - how many recordings of the queue are counted at the same time on
  an NVIDIA card, four by default. When one is through, the next takes its place. A machine
  without an NVIDIA card counts recordings one at a time.
- **Threads** - how many threads run the model inside one recording; 0 - the program chooses.
- **Priority** - BACKGROUND leaves the computer usable while the count runs.

The **PROJECT FILE** card - where the second copy of the lines of the recordings is kept
(see "The project file").

## Keys

| Keys | What they do |
|---|---|
| Ctrl+1 … Ctrl+7 | the sections in order |
| Ctrl+J | the journal |
| F1 | this guide |
| Esc | from SETUP, SETTINGS and ABOUT - back; in CAMERA - back to the pointer |
| Space | CAMERA: play or stop the video |
| ← → | CAMERA: a frame back and forward |
| Page Up, Page Down | CAMERA: the previous and the next recording |
| Delete | CAMERA: remove the chosen line or zone |

## What accuracy to expect

- Near the camera, where vehicles are large, the count agrees with a manual one within one
  or two percent (on the check recording: +0.8 % and -0.1 % on the two lines by the camera).
- Far away, where a vehicle is a few pixels, some vehicles are lost. A zone with enlargement
  and a larger model help; the smallest ramps of a low-resolution recording are beyond what
  the program sees.
- The classes are those the model knows: car, bus, truck, motorcycle. Cars are counted
  exactly; buses and trucks roughly: the model takes a minibus now for a bus, now for a
  truck. Trucks are not split by the number of axles.
- The program does not read number plates.
- Different ways of counting (TensorRT, CUDA, DirectML) differ by single vehicles. Compare
  runs made on the same one.

## Where things are

| Folder | What is in it |
|---|---|
| `App\`, `Start.exe` | the window of the program |
| `runtime\`, `models\`, `Tools\` | Python with its libraries, model weights, FFmpeg - installed by the SETUP section |
| `config\` | the settings, the list of recordings, the placements of lines (`config\cameras`) |
| `output\<recording>\<date-time>_<model>\` | a run: `events.csv`, `summary.json`, the lines it was counted with |
| `reference\` | manual counts to check against |
| `audion-traffic-counter.json` in the folder of the videos | the project file: a second copy of the lines, beside the recordings |
| `logs\` | the journal, a file per day |

## What the program does on the network and on the machine

It collects nothing and sends nothing. It goes to the network only during installation and
at the first count with a new model - to download Python (python.org), the libraries (PyPI,
pytorch.org), the model weights (GitHub), FFmpeg (gyan.dev, GitHub) and 7-Zip to unpack it
(7-zip.org).

It installs nothing into the system: no services, no scheduled tasks, no registry entries.
To remove the program, delete its folder.

## If something goes wrong

| What you see | What to do |
|---|---|
| "The counting engine is not installed" | the SETUP section, INSTALL WHAT IS NEEDED |
| "FFmpeg was not found", no video in CAMERA | the SETUP section, the FFMPEG button |
| SETUP asks to update the NVIDIA driver | update the driver and press CHECK AGAIN |
| a recording says "no lines", the queue says "will be skipped" | draw the lines in CAMERA |
| a line counts nothing | its arrow points the wrong way: turn it with the arrow button in the row of the line |
| a far line counts far fewer than there are | draw a zone with enlargement, take a larger model |
| "the edits of its lines are not saved … - the recording is not counted" | the recording has a line without a movement name, or not a single line is left: fix it in CAMERA and count it again |
| "the count failed" on one recording | the file cannot be read as a video, breaks off before its end, or its frame size differs from that of its lines; details are in the journal |
| "the engine was silent … - stopped" | the engine hung; the queue went on. Count this recording again; if it repeats, choose another way to count in COUNT |
| the count is slow | it runs on DirectML or on the processor: look at the "Count on" row in COUNT |
