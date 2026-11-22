# aiVoiceTools

Turns text into natural speech on your Mac with the Breeze-TTS-2 voice model. Everything runs on
this computer: no account, no upload. Type or paste text, open a subtitle file, or let another app
ask for speech, then play it at once, save it as a WAV file, or both.

Requires a Mac with Apple Silicon (M1 or later) and macOS 14 or later.

<!-- toc -->
**Contents**

- [Install](#install)
- [First run: the voice model](#first-run-the-voice-model)
- [Speaking text](#speaking-text)
  - [Fixed length (MOSS-TTS)](#fixed-length-moss-tts)
- [Voices](#voices)
  - [Exporting and importing voices](#exporting-and-importing-voices)
  - [Asking for a pace in the description](#asking-for-a-pace-in-the-description)
- [Several voices in one text](#several-voices-in-one-text)
- [Speaking subtitles](#speaking-subtitles)
- [Transcribing speech](#transcribing-speech)
  - [LiveChat: the microphone, live](#livechat-the-microphone-live)
- [Settings](#settings)
- [Speaking a text file](#speaking-a-text-file)
- [Saved files](#saved-files)
- [The web page](#the-web-page)
- [Where things are kept](#where-things-are-kept)
- [Advanced topics](#advanced-topics)
  - [Voice file formats](#voice-file-formats)
  - [Text restrictions](#text-restrictions)
  - [Local API for other apps](#local-api-for-other-apps)
  - [GPU details and the log](#gpu-details-and-the-log)
  - [Running a second copy](#running-a-second-copy)
- [License](#license)
- [Credits](#credits)
<!-- /toc -->

## Install

1. Open `aiVoiceTools-<version>.dmg` and drag **aiVoiceTools** into **Applications**.
2. The app is signed ad hoc, not by an identified developer, so the first time macOS may refuse to
   open it. Right-click (or Control-click) the app in Applications, choose **Open**, then **Open**
   again. Later launches open normally.

On an NVIDIA DGX Spark (or another ARM Linux computer with an NVIDIA GPU, NVIDIA's driver 580 or
newer and Ubuntu 24.04 or newer), install the CUDA package, which runs the voice model on the GPU through
CUDA:

```
cd ~/Downloads; sudo apt install ./aivoicetools-cuda_<version>_arm64.deb
```

It takes the place of the plain `aivoicetools` package if that one is installed.

Only one aiVoiceTools runs at a time. Opening it again (from Applications, from a mounted DMG, or
the program itself) brings the open window to the front instead of starting a second copy.

## First run: the voice model

A new install speaks with MOSS-TTS 1.5, a separate download. On the first launch its download
starts with no click when the disk has room for it, at the size picked for this computer (see
**Model** under Settings below); otherwise Settings says how much room it needs.

The other family, [Breeze-TTS-2 by BreezeBlue](https://huggingface.co/BreezeBlue/Breeze-TTS-2),
is also a separate download. Its weights come under their own license, the
[BreezeBlue Research and Non-Commercial License Agreement](https://huggingface.co/BreezeBlue/Breeze-TTS-2/blob/main/LICENSE).
When Breeze-TTS-2 is the family in use, the app shows that license; **Accept and download** fetches
the first model. A Mac with Apple silicon starts with the MLX HQ16-bit model
([mlx-community/Breeze-TTS-2-mlx](https://huggingface.co/mlx-community/Breeze-TTS-2-mlx)), which
runs as HQ8-bit (see below), or with its 8-bit copy with less than 16 GB of memory. Other computers
start with a GGUF model: Full precision (HQ16-bit, 6.3 GB), or Standard (3.6 GB) with less than
16 GB, and on the CPU Standard, or Compact with less than 16 GB. The bar shows how far it has got, and if you quit, the download carries
on where it stopped next time. Until the model is present, Generate, Try it and the
local API make no sound and say "Model not yet downloaded" while the download runs, or "Model
missing" when none is running. If an MLX model cannot start on your computer and a GGUF model is already
downloaded, the app says "Unable to use MLX, switching to Standard engine" and speaks with that one for that run. If there is
none, the app and the local API say why and make no speech.

In **Settings > Model**, a model from a publisher other than HoppouAI, mlx-community or BreezeBlue
asks before it downloads. The app's license and the notices of everything built into it are in
`aiVoiceTools.app/Contents/Resources`.

Models, in the **Settings** tab under Model (each can be downloaded, used or deleted there). To
switch models, first click **Download**, then click **Use** when it has finished: downloading
alone does not change the model in use.

| Model | Size | Notes |
| --- | --- | --- |
| Full precision (HQ16-bit) | 6.3 GB | The default; the original quality, run as HQ8-bit. |
| Standard (8-bit) | 3.6 GB | Close to the original quality at about half the size. |
| Compact (4-bit) | 2.5 GB | The smallest; for Macs with less memory. |

**HQ8-bit**: Enhanced 8-bit created in app from 16-bit models (Settings > Model, on by default). The
model's depth decoder is loaded in 8-bit while the rest stays 16-bit. It sounds about like the
original 16-bit, runs a little faster and needs about 0.7 GB less memory; turn it off for the 16-bit
model as published. The switch adds an **HQ8-bit** row at the top of the model list, made here
from the 16-bit file (source "aiVoiceTools Gen"); its **Use** loads that, and the 16-bit row's
**Use** loads the 16-bit file as published. Settings from an earlier version keep their
model and load it as before.

The list is read live from Hugging Face the first time Settings > Model is opened while the app
runs, and with **Refresh**: every Breeze-TTS-2 GGUF file of [HoppouAI/Breeze-TTS-2.cpp](https://huggingface.co/HoppouAI/Breeze-TTS-2.cpp)
whose header says it is a Breeze-TTS-2 model, such as 6-bit (3.1 GB), each pinned to the
repository's current revision, and on a Mac with Apple silicon every copy converted for MLX
(repositories tagged `breeze_tts` and `mlx`). Experimental files (`-dd2`, `-dd4`) are not offered.
When Hugging Face cannot be reached the app shows the list from last time, or the three models
above and the MLX default.
Each download is checked against its published SHA-256, where the hub gives one, before it is used.

## Speaking text

1. Choose **Text**, then type or paste.
2. Pick a **Voice**, a **Speed** (0.5x to 2x; the pitch stays the same) and an **Output**:
   **Play**, **Save**, or **Both**.
3. Click **Generate**. **Stop** ends it at once.

While it plays, the bar works as a level meter. Speech starts playing before the whole text is
done: the app first gathers enough audio that playback does not run dry. That is a few seconds on
a fast computer. On one slower than real time it can be up to two minutes of audio, and the
status line says "GPU not fast enough for real time - please wait for buffer" and when sound
will start; a text too long for that pauses to gather more.

A long text is spoken in pieces, and the model makes each piece on its own, so one piece can come
out louder or softer than the one before. The app evens them out: each piece is turned up or down
to the median speech level of the pieces before it (pauses do not count), by at most 8 dB and
never past -1 dBFS, so pieces end up within about 2 dB of each other. The first piece sets the
level. A saved file is leveled piece by piece as a whole. While it plays, each piece after the
first holds back its first second of speech to set its level; the piece before is still playing
then.

MOSS-TTS sometimes ends a piece with a short stray sound after the last word, a fraction of a
second after a pause (heard as a stray "but"). When a piece ends with at most 0.2 s of sound
after at least 0.3 s of quiet, and that sound is still loud when the piece stops (a real last
word fades out), the piece ends in the pause instead. Every MOSS piece ends with a 10 ms fade, so
none stops mid-sound. Breeze-TTS-2 does not do this, so its pieces are left as they are.

**Strictness** is how closely the voice follows the text and the voice description. 1.0 (the
default) is the fastest; higher values are stricter but take about twice as long.

Before text goes to a voice model, the app leaves out what reads badly, such as emoji and odd
symbols, and the status line says how many characters were not read. The full rules are under
[Text restrictions](#text-restrictions).

### Fixed length (MOSS-TTS)

**Fixed length** reads typed text to fill a set number of seconds, for a spot of an exact length
such as a 30 second promo. Turn it on and enter the seconds (1 to 600) on the same row. MOSS-TTS
is told how long the speech should be and paces its reading to fit, so the words are not sped up
or slowed down afterwards. A long text is spoken in pieces, and each piece gets its share of the
time.

- **Text that fits:** the speech fills the time, ending on a natural last word. The model follows
  the length closely but not to the frame, so a saved take that comes out up to 5% long is sped
  up evenly (the pitch stays) to end exactly on the length. Played as it is made, it can end a
  fraction of a second past the length, since what is heard cannot be sped up afterwards.
- **Text too short for the time:** the reading slows down only so far (about a third slower than
  usual), then ends naturally, and silence fills the rest of the time. The model is never asked to
  stretch a short text into babble or to add words. When most of the time would be silence, the
  note says so.
- **Text too long for the time:** the reading speeds up only so far (about a sixth faster than
  usual). Before you click Generate, the row shows the text's natural length and an orange note
  says the text is too long; the speech then runs over the time, and nothing is cut.

The status line says how long the speech was and how much silence followed it, or by how much it
ran over. **Speed** is grayed while Fixed length is on, since the length sets the pace. Each piece
also gets room past its share of the time, above **Max length** if needed, so the last words are
never cut.

Fixed length works with MOSS-TTS only. With Breeze-TTS-2, or for subtitles (which keep their own
timing), the row is grayed out, and hovering over it says why. The local API takes it per request
as `target_seconds` (see [Local API for other apps](#local-api-for-other-apps)).

## Voices

On the **Speak** tab, **Voice source** is either:

- **Voice design**: pick a preset or describe the voice in your own words, and choose a **Seed**.
  With MOSS-TTS 1.5, a woman's voice needs "Female" as the first word of the description, for
  example "Female, warm, calm narrator."; without it MOSS often speaks with a man's voice.
  The same seed, text and description give the same voice. **Random** picks a random seed;
  **Keep** saves the voice **▶ Try it** plays as a saved voice: the voice reads a short script
  that holds every sound of English (about 11 s), and that reading is saved the way an imported
  clip is, as a clone. From then on the voice no longer depends on the seed: it sounds the same
  with any text, on any engine or size, and the green light on the bottom bar is on while it is
  saved. While you design, every text in the description and seed follows the take Try it plays,
  so the voice Try it plays is the voice of any words.
  Designed voices kept before 0.2.78 are saved the same way, once, by themselves: each the first
  time the model it was kept with loads (the status line says how many). They then leave the
  list of designed voices and appear as saved voices of the same name; nothing is deleted.
- **Saved voice**: speak with a voice from the Voices tab, exactly as it was saved. A voice made
  from a seed always uses that seed, a cloned voice always the same one, and the description, seed
  and strictness in Voice design are not applied. Designed voices are in the same list, marked (designed): choosing one speaks
  with its own description, seed and strictness and leaves Voice design as it is. **▶ Try it** beside the menu,
  and on every voice in the Voices tab, plays a short line in that voice (click again to stop).

**Seed audition**, in the Voices tab below the saved voices, speaks one sentence with several
seeds (up to 10) and its own description, so you can hear the voices side by side. **Keep** saves a take you like as a voice, named after the
preset (or the description's first words) and its seed, such as `Warm-woman-seed-1`; it is then
under Saved voice and in the saved voices just above, and its row leaves the list. Keep on a long take saves the voice of its first
part (about 450 characters), and the status line says so.

The **Voices** tab is where saved voices are made: choose a reference clip (WAV, MP3, FLAC or Ogg, of
any length), type exactly what is said in it, and give it a name. Its dead air goes first: the silence before the
first sound and after the last, and any pause over a second is shortened to one. Then only the first
30 s of what is left are used, ending at a pause where it finds one, and the app says so ("Using
28 s of speech from a 4:12 clip (dead air removed)"): type only what is said in that part, which
**Play clip** plays. With MOSS-TTS only the first 20 s are used. A much longer reference can make
speech stop early.

The transcript must say exactly what is heard in the part used: words beyond it teach the voice
words it never hears. When a Transcribe model is downloaded (see Transcribe), choosing a clip
writes the words down for you: the app transcribes the part used, puts its words in the
Transcript box and ends the clip right after the last whole word, so the words and the audio stop
at the same place. Check the words, fix any mistakes, then save.
Without a Transcribe model, type the words yourself; a transcript that looks too long for the
part used is pointed out before the voice is saved. **Try it** speaks a short line
in that voice first; **Save voice** keeps it. In the Saved voices list, each voice has a radio button
left of its name and its own **Try it**; check a voice, then **Use**, **Export…**, **Rename** or
**Delete** beside the Saved voices title act on it (Delete asks once more). Voice tags and
subtitle speakers can use saved voices by name. A voice made from a seed (a designed
voice, or a take kept from the Seed audition) shows its seed beside its name; a voice cloned from
a clip has none.

### Exporting and importing voices

A voice can be taken to another computer with an `.aivoice` file. The file carries what the
voice sounds like, as clips of it speaking, and the other computer clones the voice from them,
the way Save voice clones from a clip. The clone can sound a little different from the voice it
was made from; it plays on either model family and any engine.

The file holds the voice's name, clips of it speaking with their exact words, and what made it
(the app version, model family, engine, weights and the computer it was exported on, which the
status line names when it is imported). The layout is under
[Voice file formats](#voice-file-formats).

**To export a voice**

1. Open the **Voices** tab and check the voice's radio button under Saved voices (saved and
   designed voices both export).
2. Click **Export…** beside the Saved voices title and choose where to save the file. If the model is still loading,
   the export waits for it.
3. A saved voice that keeps the clip it was saved from (every voice saved since 0.2.78) is
   written at once, with that clip. Any other voice reads the two scripts, which takes about as
   long as speaking them (longer on the CPU). **Stop export**, in Export's place, stops it, and
   nothing is written. Rename and Delete wait until it is done.
4. When the status line says "Exported…", copy the file to the other computer.

**To import a voice**

1. Open the **Voices** tab on the other computer.
2. Once the voice model has loaded, click **Import…** below Saved voices and choose one or more
   `.aivoice` files. The app clones each voice from its short clip (its 11 s reading), the one
   closest to the voice as it was made, or from the long clip when a file has no short one.
   Each takes a few seconds,
   and the status line says which clip it used. The green light on the bottom bar stays on until
   all are done.
3. Each voice is cloned with the model family that made it, when that family is on this
   computer: for a voice made with the other family, the app loads that family (MOSS-TTS at the
   voice's own size when it is downloaded), imports those voices, and then loads the model you
   had again. Your model choice in Settings does not change. When that family is not here, the
   voice is cloned with the model in use and may sound different, which the status line says;
   download that family and import it again for its own sound. A voice cloned while MOSS-TTS runs
   a smaller size than chosen (too little memory free) gets "-4bit" at the end of its name.
4. Use **Try it** on a new voice to hear it. A voice is named after its file (without
   `.aivoice`), so renaming the file renames the voice; a name already taken gets a number added.

An imported voice is always a saved voice, whatever it was exported as. A clip in the file is
used whole, since its words are exactly what it says.

### Asking for a pace in the description

Words like "speaks slowly" and "speaks quickly" change the pace. A specific number, such as a
words-per-minute figure or a length in seconds, does not reliably get you that pace. To hit an
exact length, change the speed after generating.

These tables are based on a small test, and results may vary based on the seed selected. Each is
one text at seed 1, and the two used different texts and descriptions, so compare within a table
rather than between them.

**MLX**, build 246 on a Mac, HQ8-bit, a 61-word text, with the description "A calm adult speaker."
plus the words below. Each result is the spoken length and words per minute.

| Added to the description | Strictness 1 | Strictness 4 |
|---|---|---|
| Nothing | 19.6 s, 187 | 23.1 s, 158 |
| Speaks slowly. | 24.5 s, 149 | 38.3 s, 95 |
| Speaks quickly. | 14.5 s, 253 | 13.9 s, 264 |
| Speaks at 120 words per minute. (would be 30.5 s) | 21.1 s, 174 | 20.6 s, 177 |
| Speaks at 180 words per minute. (would be 20.3 s) | 19.3 s, 190 | 21.5 s, 170 |
| Reads this text in 30 seconds. | 18.3 s, 200 | 19.5 s, 188 |

**C++ engine**, version 0.2.7 on Windows, HQ8-bit, a 71-word text, with the description "A clear,
calm adult narrator". Rewording the description with no mention of pace moved the length by up to
20%, so small differences here may not come from the pace you asked for.

| Description | Length | Words per minute |
|---|---|---|
| Baseline | 23.7 s | 181 |
| Neutral rewordings: clearly, friendly tone, warmly | 23.0, 24.7, 28.5 s | 187, 177, 153 |
| Speaks slowly | 31.8 s | 134 |
| Speaks quickly | 19.4 s | 221 |
| 120 words per minute | 23.1 s | 185 |
| 180 words per minute | 25.8 s | 170 |
| Read this in 30 seconds | 29.0 s | 149 |

## Several voices in one text

One text keeps one voice from start to finish. To use several voices, define them at the very
start and switch with tags:

```
[voice1=1234]
[voice2=Calm narrator]
[voice3= "an elderly woman with a soft voice seed 52"]
[voice1] Good morning. [voice2] And good morning to you. [voice3] Hello, both of you.
```

- `[voiceN=1234]` is a seed, spoken with the voice description from the window.
- `[voiceN=Name]` is a saved voice (see Voices) or a voice preset, by name.
- `[voiceN= "description seed N"]` is a voice description with its own seed.
- `[voiceN]` switches to that voice until the next tag. Text before the first tag uses the
  window's voice. Each voice sounds the same every time it comes back.

Definitions are not spoken. Other brackets, such as `[sigh]` or `(laugh)`, stay in the text. A
tag that is not defined, or a name with no voice, stops before any audio with a message naming the
tag and line. Tags also work in subtitle cues and in the local API's `input`.

## Speaking subtitles

1. Choose **Use File** and click **Open** (an .srt, a .voices.txt or any .txt).
2. Click **Generate**. Subtitles are always saved to a WAV file.

When the subtitles name their speakers, each speaker gets their own voice. These forms are
recognized at the start of a cue line:

- `[John] Good morning.` (1 to 3 words starting with a capital letter)
- `JOHN: Good morning.`
- `<v John>Good morning.</v>`

The app writes a copy next to the .srt, `film.voices.txt`, with one voice definition per speaker
at the top and each speaker's name replaced by their tag (the .srt itself is never changed):

```
[voice1= "male voice named John seed 123456"]
[voice2= "female voice named Sarah seed 789012"]
1
00:02:41,000 --> 00:02:43,295
[voice1] Good morning, everyone.
2
00:02:44,000 --> 00:02:47,100
[voice2] Morning, John. Are we ready to start?
```

The **Speakers** table shows each speaker's tag and lets you change the gender, the description,
the seed, or pick a saved voice; changes are written back into that file. Opening the .srt again
opens the `.voices.txt`; **Re-create from .srt** starts it afresh. Cues with dialogue dashes but
no names keep the voice before them. **Sample** at the end of a row plays "This is a sample of
this voice." in exactly that speaker's voice, at the current speed, and for a designed voice the current strictness (click again to
stop); a second click on an unchanged row plays it at once. It is available while no job runs.

**Timing** chooses how the cues are spoken:

- **Fit to timing** (the default, to match a video): the fitting below. The file is saved as
  `<name>-fit.wav`, with its summary `<name>-fit.fit.txt`.
- **Natural**: the timecodes are ignored and the cues are read in order at the base speed, with a
  0.3 s pause between them, nothing fitted or stretched. Voice tags and speakers still apply. A file
  with no timecodes is always read this way.

With Fit to timing, each cue with a timecode starts exactly at that time and has until the next
cue starts (the last cue: until its own end). Speech is never slowed down:

- A cue that is shorter than its time is played at the base speed, and silence fills the rest
  until the next cue starts: `fits (padded 1.2 s)`.
- One that is a little long is sped up to fit, up to 1.35x: `sped 1.12x`.
- One that is far too long is spoken again more quickly (up to two more tries, same voice; a cue
  in saved voices only is not, since that would change nothing, and is just sped up), then
  sped up to fit, up to 2x: `redone 2, 1.6x`.
- One that still runs over pushes the next cues later, so cues never overlap: `pushed 0.8 s`.

The cue list marks how each cue was fitted, and a summary with the same name as the WAV
(`.fit.txt`) is saved next to it.

**Max length** on auto is only a guard against a take that never ends: it allows the text at a
slow pace, twice over, plus a few seconds. A subtitle take that still reaches it is made again
with twice the room instead of being kept cut, and the status line and the `.fit.txt` count how
often that happened. Typed text that is only saved is handled the same way; typed text that is
played as it is made gets more room, and if a piece still reaches the limit the status line says
so.

**Speed** is the base pace: each cue is first played at that speed, and any speeding up above works
on top of it (the total stays between 0.5x and 2x of the natural speech). A cue without a readable
timecode (missing, malformed, or ending before it starts) is spoken right after the cue before it,
after a 0.3 s pause; a file with no timecodes at all is read line by line, in order.

Subtitles are rendered whole and saved. Tick **Play after generation** to hear the saved file from
the start once it is completely written.

## Transcribing speech

The **Transcribe** tab writes speech down, with our own speech-to-text engine. Pick a model and
download it once; **Default** is Large v3 Turbo on a GPU and Small on the CPU:

| Model | Download | Good for |
| --- | --- | --- |
| Large v3 Turbo | 1.6 GB | A GPU: near Large v3, several times faster |
| Large v3 | 3.1 GB | The most accurate, the slowest |
| Small | 970 MB | The CPU |
| Base | 290 MB | The fastest, the least accurate |

Open an audio file (WAV, MP3, FLAC or Ogg Vorbis, up to three hours), choose the language or leave
it on **Detect**, and click **Transcribe**; **Stop** ends it. The model loads the first time (a few
seconds) and stays loaded; it runs on the device Settings chooses for speech, next to the voice
model, which stays loaded too. When the GPU has no room left for it, it runs on the CPU. While speech to
text writes words down (here, a clip's words on the Voices tab, the web page or the API), the
green light on the bottom bar is on and the status line says "Sampling Audio" with the GPU
or CPU it runs on.

- **Translate to English** writes the speech in English, whatever language it is in. Large v3
  Turbo was not trained to translate; choose Large v3, Small or Base for it.
- **Word timings** find when each word is said, so subtitles follow the speech closely; off, each
  subtitle shares out its segment's time by length.
- **Speakers** tells voices apart by how they sound (no extra model) and tags each subtitle with
  its voice: `[voice1]` in .srt and .txt, `<v voice1>` in .vtt, and in the transcript shown. **Auto**
  finds how many voices there are (up to 8) from where joining voices costs the most; when you
  know, choose the count (2 to 8), which is more reliable, above all for similar voices or a
  voice that says little in a long recording. It needs each
  word's time, so it turns on Word timings for that transcript. A new voice starts a new subtitle;
  in .txt, a line is tagged with the voice that says most of it. When only one voice is found,
  nothing is tagged. Off by default.

Save the transcript as **.srt** or **.vtt** subtitles (lines of up to 42 characters, two lines and
seven seconds at most per subtitle, breaking at sentence ends and pauses), **.txt** (one segment
per line) or **.json** (segments and words with their times, and the text as shown). **Speak as subtitles** opens it on
the Speak tab as subtitles, to speak it again in another voice. The web page has the same tab.

### LiveChat: the microphone, live

The **LiveChat** switch on the Transcribe tab listens to a microphone and writes down what it
hears, one phrase at a time, so another program on this computer (for example a chat bot) can
hold a spoken conversation with you. Choose what it listens to in **Listen to** on the same row:
any microphone, a virtual device that carries another app's sound, or **System sound: ...**, the
sound this computer plays (for example a call or a video in another app; on a Mac this needs
macOS 14.6 or later and access under System Settings, Privacy & Security, Screen & System Audio
Recording). **System default** follows the system's choice of microphone; the list is read again
each time it opens, so a microphone plugged in since shows up. The bar beside it shows how loud
the input is. A phrase ends
when you pause: **Pause** on the same row is how long a quiet stretch ends it (0.65 seconds by
default, 0.5 to 3), so a breath mid-sentence does not. A phrase that goes on without a pause is
cut at its quietest moment once it reaches 25 seconds. While the Transcribe tab is open, the
phrase being said shows as it grows (about once a second), and each finished phrase stays in the
box.

While the app is speaking (from the window, the web page or the API), LiveChat does not listen,
and for half a second after, so a program talking to you through the app does not hear itself.

LiveChat uses the tab's language, and the tab's model unless **Model** on its row names another.
A smaller model (Small or Base) writes each phrase down sooner, with a few more mistakes; Large v3
Turbo is the most accurate of the quick ones. While LiveChat is on, the
speech-to-text model stays loaded on every tab. LiveChat is off whenever the app starts, so the
microphone is used only after you turn it on.

Other programs on this computer listen to LiveChat through the local API (see
[Local API for other apps](#local-api-for-other-apps)), one at a time: the program that asks
first is on the call, and the row says which; others are told the line is busy until it hangs
up, stops asking for 30 seconds, or you click **End call**. During a call, only that program may
play speech out loud through the API. Its replies start playing as soon as the first sound is
made, and a reply sent one sentence at a time plays as one, each sentence right after the last.

The first time, the system asks whether aiVoiceTools may use the microphone. On a Mac, if you
said no, turn it back on in System Settings, Privacy & Security, Microphone; a new build of the
app may ask again. On Windows, desktop apps need Settings, Privacy & security, Microphone, "Let
desktop apps access your microphone".

## Settings

The **Settings** tab, always the rightmost tab (after Speak, Voices, Transcribe and API), has one card per section; click a
card's title to open or close it:

- **General**: theme (Auto, Dark, Light; Auto follows the system), the save folder and the engine
  threads, the CPU threads for the vocoder (**All**, the default, uses every performance core; or
  choose 1 to that many; choosing reloads the model). **GPU**: the models always run on the best
  GPU (a dedicated card first, then the one with the most memory). They run on the CPU only when
  there is no usable GPU; MOSS-TTS also runs on the CPU when a dedicated GPU has too little memory
  for even its 4-bit model (under 6 GB), and the GPU row says so. A Mac's GPU shares the
  computer's memory, so a Mac always uses it. There is no switch for the CPU. On the CPU, playback
  first gathers a buffer and the Speak tab says "CPU not fast enough for real time - please wait
  for buffer". With several GPUs a list offers **Best GPU** and each one by name; changing it
  reloads the model. The line under it says where the models run. The engine line under the
  title names the GPU and, while it runs on one, "GPU Free:" and its free memory. If the GPU
  fails during a job, the app carries on on the CPU for the rest of the session. More detail is
  under [GPU details and the log](#gpu-details-and-the-log).
- **Noise Gate** (in General, on by default): how the quiet between words sounds in saved
  speech (subtitles, text saved with Output: Save, and the local API's WAV answers; with Both, or
  the API's `play`, the file keeps what you heard). The model sometimes drops a moment of perfect silence into a pause that otherwise holds
  a soft room tone, which can sound like a noise gate switching. The setting smooths that out
  either way:
  - **On**: the quiet between words fades smoothly to silence. Sound at -40 dBFS and above
    passes untouched (for a soft voice the gate opens lower, 20 dB under the voice's own level,
    so soft syllables pass too); 12 dB below where it opens it has faded out completely, with a
    gradual slope in between. The fade opens 20 ms before a sound and waits 60 ms after one, so the starts and ends
    of words are kept; it opens in about 5 ms and fades out over about 60 ms.
  - **Off**: the soft room tone stays, and any sudden silence inside it (below -80 dBFS, from
    20 ms to 2 s long, with room tone on both sides) is filled with room tone taken from just
    before it (short overlapping grains from random places, so it never repeats on a beat), at the
    level around it, faded in and out over 5 ms.

  The levels are fixed, apart from the opening point following a soft voice down. Speech played
  live as it is made is left as it is. Saved WAV files are dithered, and each trimmed subtitle
  take fades in and out over 10 ms, so quiet ends never stop abruptly.
- **Compression** (in General, below Noise Gate, off by default): a gentle compressor on all
  speech, played and saved alike (typed text, text files, subtitles, the web page and the local
  API).
  - **What it does:** above -24 dBFS, every 2 dB louder comes out only 1 dB louder (2:1). Then
    3 dB of makeup gain brings the whole back up, so speech at about -18 dBFS comes out where
    it went in. A limiter keeps every sample at or below -3 dBFS. It follows the level over
    about 20 ms, turns down within about 10 ms and comes back up over about 150 ms, so it evens
    out loud and soft words without pumping on each syllable. Quiet sound below -24 dBFS is
    only lifted by the 3 dB makeup.
  - **When to use it:** for a steadier, denser sound, as in broadcast or audiobook narration;
    for listening in a noisy place or at low volume; or when a voice swings between loud and
    soft words. Leave it off to keep the voice's natural range.
  - **How it differs from the automatic leveling:** the app always evens out whole pieces of a
    long text, matching each piece to the ones before it (see
    [Speaking text](#speaking-text)). That changes each piece by one fixed amount and leaves the
    speech inside it as it is. Compression works word by word inside every piece as well. It
    runs after the leveling, so with both on, pieces match first and the compressor then
    narrows what is left.
- **Advanced Settings (Danger)** (in General, last, off by default): a switch that shows extra
  controls for MOSS-TTS. While it is off they are hidden and speech uses the model's own values;
  the values set are kept for the next time it is turned on. Odd values can make speech garble,
  loop, or stop early, so **Default** beside a control, or **Reset all to the model's values**,
  puts it back. The web page has the same switch and controls.
  - **Language hint**: tells MOSS-TTS the language of the text (English, Chinese, Japanese,
    Korean, German, French, Spanish, Italian, Portuguese or Russian). **Auto**, the default, lets
    it tell from the text. Useful when a text is not English or mixes languages.
  - **Sampling**: how MOSS-TTS picks each next sound. **Audio temperature** (model value 1.7)
    makes the voice livelier and more varied when higher, steadier when lower; **Audio top-p**
    (0.8) and **Audio top-k** (25) limit the choice to the likeliest sounds. **Audio repetition
    penalty** (1, off) makes a sound used in the last 4 seconds less likely again, which can stop a voice that
    loops or stutters; try 1.05 to 1.2. **Text temperature**, **Text top-p** and **Text top-k**
    (1.5, 1 and 50) do the same for the channel that decides when to keep speaking or stop.
    An audio temperature below 0.5 is allowed, but the control shows a warning: a take can
    stall, with seconds of silence and lost words.
  - Breeze-TTS-2 does not use these values.
- **Model**: **Model family** picks Breeze-TTS-2 or MOSS-TTS 1.5 (grayed out in builds without
  the MOSS engine). MOSS-TTS 1.5 is the family of a new install, and its download starts on the
  first launch with no click when the disk has room for it (otherwise Settings says how much room
  it needs); a download cut short carries on at the next launch. With MOSS-TTS 1.5 the list shows
  its three sizes instead, each with its room on disk and Download, Use and Delete: **Small
  (plain 4-bit)**, made on this computer from the 16-bit file (or from an earlier 4-bit MOSS
  file, with no download) with progress and Cancel. A 16-bit file already here is used with no
  download, and one downloaded for Small is kept afterward, so Full precision is ready too
  (Delete removes it); **Standard (8-bit)**; and **Full precision
  (16-bit)**. MOSS picks its size by itself: Standard on a GPU with 14 GB of memory or more, Small
  otherwise, and downloads (or, for Small, makes) that size with no click; the help line above
  the sizes names the GPU's memory and the size picked. Until the picked size is here, a size
  already downloaded goes on speaking. **Download** on another size switches to it once it is
  here, with no Use click; **Use** switches between sizes already downloaded. A saved size is
  always used: a size picked with Use or Download, or one written in the settings file. It stays
  until another is picked, and Use on the size picked for the GPU goes back to picking by itself
  (a size picked by itself is not written to the settings file, so it follows the GPU). When a
  saved size is smaller than the one MOSS would pick for the GPU, it is still used, and the
  Engine line says "use of a larger model may be possible". Full precision is only picked by
  hand. Each time MOSS loads, it checks the
  memory free on the device at that moment: free GPU memory on any GPU (on a Mac, the smaller of
  that and the memory macOS has available), or free memory on the CPU. When the chosen size
  needs more than is free (about 18.3 GB for Full precision, 11.6 GB for Standard, 7.6 GB for
  Small), it loads the largest smaller size that is downloaded and fits (the smallest one
  downloaded when none fits), and the Engine line says so (it always names the size running, as in "MOSS-TTS 1.5 (4-bit)"), for example "using 4-bit due to
  small available VRAM (9.0 GB free)", or RAM on the CPU. On a Mac it says "small available
  memory (13.0 GB available in macOS)": the figure is what macOS had available before the
  model loaded, so it differs from GPU Free, which is Metal's own room for the app. The setting
  stays as it was, so the next load, with more memory free, uses the chosen size again. When
  MOSS picks 4-bit by itself (on the CPU, or on a GPU under 14 GB), the Engine line says that
  too: "using 4-bit: picked for the CPU". A download fetches the backbone and the shared codec file into the models folder's `moss`
  folder, and carries on where it stopped. Both families play while they speak: MOSS sometimes opens a piece with room noise before
  the first word; the app drops it (up to 3.5 s) before playing or saving. MOSS hands
  out its sound from the first few frames on, and playback starts once enough has gathered that
  it does not run dry. A MOSS voice that has to hold (a saved voice, a
  cloned clip, or the later pieces of a long text) speaks from a reference clip's codes, which
  keep the voice across texts and lengths; every saved voice plays on
  either model (see below). **Engine threads** and the **GPU** row apply to MOSS as well. Every saved and designed voice records the family, engine and weights it was
  made with: hover over its name in the Voices tab to see them. An orange ⚠ beside it means the
  model in use differs. It only tells you the voice may sound different; the voice still plays,
  and its tooltip says how it speaks:
  - A **designed** voice kept before 0.2.78 and not saved as a clone yet does not play with the
    other family, because its description and seed there would be another voice: Try it and
    Generate say which model to choose. When that model loads, the voice is saved as a clone of
    itself and then plays with every model.
  - A **saved** voice (a clone, or a voice saved from a take) made with the other family speaks
    as a clone of a short clip of it that the app keeps beside the voice (made when the voice is
    saved, or for a voice saved before 0.2.72, the first time it plays with its own family).
  - Either kind made with the same family but another engine or other weights speaks, and may
    sound a little different.
  - While speech is being made, the model choices in Settings are grayed, so a change cannot
    stop it; they come back when it is done.
  - A voice saved before this was recorded is taken to be of the family its codes belong to
    (Breeze-TTS-2 or MOSS-TTS 1.5), and warns the same way when that family is not in use. Then **HQ8-bit**, then one list of the models Hugging Face has: the model in use, this
  computer's default, then the rest most downloaded first: every Breeze-TTS-2 model on Hugging
  Face that the engines here can play (MLX folders tagged Breeze-TTS and MLX with the right files
  and a config built like Breeze-TTS-2's: the same layer counts, sizes, vocabularies and
  codebooks, apart from its quantization; and GGUF files from any repository named "breeze-tts"
  whose header says Breeze-TTS-2). A downloaded model that still fails to load says "Cannot play
  on this engine" with the reason, until it loads or is deleted. Each row names the precision,
  the size and the publisher, such as "16-bit · 6.3 GB · HoppouAI" (a 16-bit file always says
  16-bit), with Download, Use and Delete; click Download, then Use when it has finished. While
  the **HQ8-bit** switch is on, an "HQ8-bit · 6.3 GB · aiVoiceTools Gen" row comes first and the
  others move down one; each row's Use picks exactly that model. A file has one row: when
  Hugging Face has a newer revision of the file in use, its row says so and offers **Update**
  (then **Use newer** once it is downloaded). Models the app makes itself (HQ8-bit, MOSS Small)
  show "aiVoiceTools Gen" as their source.
  **Use** stops whatever is running and loads the model, so only one engine is ever in memory; while
  it loads, Generate is grayed and the local API's speech answers 503. **HQ8-bit** applies to any
  HQ16-bit model on either engine. Full precision (16-bit) is the original quality; 8-bit is about half its size; 6-bit sits between 8-bit and 4-bit; 4-bit suits
  Macs with less memory. **Engine threads** and the **GPU** row show only while a GGUF model is in use.
- **Local API**: on or off, the port (1024 to 65535), and a link to the web page while it listens.
  It listens on 127.0.0.1 only; see [Local API for other apps](#local-api-for-other-apps). The
  **API** tab shows each request as it happens; see [Seeing API use](#seeing-api-use).
- **License**: when the model license was accepted, and a link to it.
- **About**: version, build and commit.

## Speaking a text file

Any other `.txt` opened with **Use File** is read straight through and saved as one WAV under its
own name. It has no cues and no fitting, so Timing stays on Natural; nothing is written beside it.

- Without voice tags, the whole file is one voice: the one chosen now.
- With tags, it works like typed text: definitions such as `[voice1=7]` or `[voice1=Calm narrator]`
  go at the very start, and `[voice1]` changes the voice until the next tag. Text before the first
  tag uses the voice chosen now, and the voice never changes without a tag. Names in other brackets,
  such as `[John]`, are spoken; speakers are found only in an .srt.
- A `.txt` with readable timecodes (`00:00:01,000 --> 00:00:02,000`) is read as a `.voices.txt`:
  cues with Timing, its tags honored, the voice chosen now where there are none, and nothing written
  beside it.
- An empty file, a file with only definitions, or a tag mistake (with its line) is refused when it
  opens. A definition naming no known voice stops at Generate, with its tag and line.

The web page takes the same files through `/v1/page/subtitles/open` and `/v1/page/subtitles/speak`.

## Saved files

WAV files (24 kHz, mono, 16-bit) go to `~/Music/aiVoiceTools` unless you choose another folder in
Settings. **Open folder** shows them in Finder (File Explorer on Windows, the file manager on Linux). After a run that saved a file, **Reveal in Finder** (**Show in File Explorer** on Windows, **Show in folder** on Linux) shows it.

Text and the local API save as `speech-<date>-<time>.wav`; subtitles take the .srt's name
(`meeting.wav`, or `meeting-fit.wav` with Fit to timing). A saved file never replaces another: if
the name is taken, `-2`, `-3`, ... is added (`meeting-fit-2.wav`, with `meeting-fit-2.fit.txt`).
Re-creating a `.voices.txt` keeps the old one and writes `<name>-2.voices.txt`.

## The web page

While the Local API is on, the whole app is also a web page at <http://127.0.0.1:8765/> (or your
port), in any browser on this computer. It has the same tabs and controls as the window, and they share
one set of settings: a change on either side shows on the other.

- **Speak**: Text or Subtitles, the voice (Voice design or Saved voice, preset, description, seed
  with Random and Keep), strictness, max length, speed and output. Play plays in the
  browser; Save writes the WAV to the Mac's save folder and offers it as a download. Subtitles and text files are
  uploaded from the browser (up to 2 MB); the speakers table, cue list, Timing and Play after
  generation work as in the window, and the `.voices.txt`, the WAV and its `.fit.txt` can be
  downloaded.
- **Voices** with Seed audition (only the start of a long clip is uploaded) and **Settings**
  (the save folder and port are shown; they are chosen in the window). Turning the local API off
  from the page asks first, since the page goes away with it.

The page talks to the app only through JSON requests to the same address. It sends file
contents, never paths on the Mac, and can fetch only files in the app's own save and upload
folders. Requests from other web pages are refused.

## Where things are kept

- Settings: `~/Library/Application Support/aiVoiceTools/settings.toml`
- Models: `~/Library/Application Support/aiVoiceTools/models`

To remove the app completely, delete it from Applications and delete the
`~/Library/Application Support/aiVoiceTools` folder.

## Advanced topics

### Voice file formats

This is for anyone writing another app that reads or writes aiVoiceTools voices.

**The `.aivoice` file (an exported voice)**

An `.aivoice` file holds, in this order:

1. 8 bytes: `AIVOICE` followed by a zero byte.
2. 4 bytes: the length of the header in bytes, as an unsigned little-endian 32-bit number (at most
   1 MiB).
3. The header: UTF-8 JSON, described below.
4. The take codes: `codes_byte_count` bytes. Files exported since 0.2.72 have none (0 bytes).
   Older files carry the model's own encoded take here; another app should skip it.
5. Each clip in turn, in the header's `clips` order, each exactly its `byte_count` bytes long: a
   complete WAV file, mono, 24,000 Hz, 16-bit signed PCM.

The whole file is at most 64 MiB, and a clip at most 45 seconds.

The header's fields:

| Field | Type | Meaning |
| --- | --- | --- |
| `format` | number | The file format version, now `1`. A reader should refuse a higher number than it knows. |
| `name` | text | The voice's name. |
| `app_version` | text | The aiVoiceTools version that wrote it, for example `0.2.78`. |
| `origin` | object | What made the clips: `family` (`"breeze"` or `"moss"`), `engine` (`"mlx"`, `"gguf"` or `"moss"`) and `weights` (the model file, or the MLX repository). For MOSS-TTS the weights name the size: `moss-tts-1.5.gguf` (16-bit), `moss-tts-1.5-q8_0.gguf` (8-bit) or `moss-tts-1.5-q4_k_m.gguf` (4-bit). |
| `seed` | number or null | The seed the voice was designed from, when it was. Shown only; an import does not use it. |
| `designed` | object or null | For a voice exported while it was a designed voice: `instruction` (its description), `seed` and `strictness`. Shown only. |
| `codes_byte_count` | number | The length of the take codes (part 4). |
| `clips` | list | One entry per clip: `script` (`"short"` or `"long"`), `transcript` (exactly the words the clip says) and `byte_count` (the WAV file's length). |
| `made_on` | object or null | The computer it was exported on: `os`, `arch`, `backend` (`"Metal"`, `"CUDA"`, `"Vulkan"`, `"MLX"` or `"CPU"`, or null), `engine_shown` (the engine as the app named it), `gpu_chosen` and `gpu_used` (or null). Missing in files from before 0.2.54. |

The `short` script is the sentence used everywhere a voice is saved: it holds every sound of
English, and reads in about 11 seconds. The `long` script starts with the same words and goes
on, about 33 seconds. A voice saved since 0.2.78 exports only its `short` clip, as it was saved;
other exports carry both.

**To read one:** check the 8 bytes, read the length and the header, refuse a `format` above 1,
skip `codes_byte_count` bytes, then read each clip by its `byte_count`. Clone the voice from a
clip and its `transcript`; aiVoiceTools uses the short clip, or the long one when a file has no
short clip (MOSS-TTS clones from at most 20 seconds).

**To write one:** write the parts in the order above with `format` 1, `codes_byte_count` 0, and
at least one clip (`"short"`, with its exact words). Fields an app does not know (`seed`,
`designed`, `made_on`) can be null. aiVoiceTools imports any such file.

**Saved voices in the voices folder**

Each saved voice is `<name>.breeze` in the voices folder (on a Mac
`~/Library/Application Support/aiVoiceTools/voices`). That file holds the model's own encoded
codes and is read only by aiVoiceTools. Beside it, two files keep the clip the voice was saved
from, which is what another app can use:

- `<name>.breeze.clip.wav`: the clip, mono, 24,000 Hz.
- `<name>.breeze.clip.txt`: exactly the words it says, in UTF-8.

A name is 1 to 64 letters, digits, `-` and `_`, with single spaces between words.

### Text restrictions

Before any text goes to a voice model (typed text, text files, subtitles and the Local API, with
either model family), the app keeps only what reads well. Odd symbols can make a model lose its
place, repeat a phrase or keep talking after the text has ended. The text box keeps what you
typed; the status line says how many characters were not read.

**Kept**

- Letters in any language, digits, spaces and line breaks.
- Normal punctuation: `. , ! ? ; : ' " - ( )`, curly quotes and dashes, `« » „ ¿ ¡`, and a `+`
  or `−` sign before a number.
- Joining marks inside words (Persian, Indic scripts) and a middle dot inside a word (`l·l`).
- The symbols `$ # % & @ /`, currency signs (`€ £ ¥` and others) and `°`.
- Tags in square brackets such as `[sigh]` (one to eight letters).

Quotation marks shape how a sentence is phrased; they are never spoken.

**Not read**

- Emoji, and symbols such as `= < > ^ * ~ | \ { } _ ` + ± × ÷` outside short math (below).
- Emoticons such as `:)`, `:-(`, `;)`, `:D`, `:P`, `XD` and `<3`, also right after a word
  (`great:)`).
- Words in square brackets too long to be a tag (more than eight letters, such as
  `[laughing loudly]`).
- Invisible and control characters.

**Punctuation next to other punctuation** is read only in these combinations; any other run
of marks (such as `),;`, `--->` or `!?.`) is not read at all:

| Combination | Example | Read as |
|---|---|---|
| Ellipsis: two or more dots, or `…` | `wait... no` · `checking..` | a pause (`...`) |
| `!` or `?` repeated | `no!!!` · `why??` | one mark |
| `?!` or `!?` | `what?!?!` | `?!` |
| An ellipsis with `?` or `!`, either way round | `Really...?` · `What?!...` | as written |
| Closing quote or bracket beside `. , ! ? ; :` | `."` · `),` · `"no";` · `"Why?", he` | as written |
| Nested quotes | `'yes.'"` | as written |
| An abbreviation's dot before `,` or `.` | `U.S.,` · `e.g.,` · `etc..` | as written (`etc..` as `etc.`) |
| A dash (`--`, `–`, `—` or `-`) beside quotes or brackets | `now--later` · `"no"—twice` · `— (not long) —` | a dash |
| Opening bracket or quote after punctuation | `, (see` · `: "Hi` | as written |

**Math.** A run of numbers and math symbols (`+ - * × / ÷ = ^ ! ( ) < >`) with a symbol between
two numbers is read as words when it has 4 symbols or fewer (brackets count): `3+9/9*24` is
read "3 plus 9 divided by 9 times 24". With more than 4 symbols, as in `(24/95^8)/(9!*3)`, it
is not read at all.

| Symbol | Read as |
|---|---|
| `+` | plus |
| `-` | minus |
| `*` `×` | times |
| `/` `÷` | divided by |
| `=` | equals |
| `^` | to the power of |
| `!` right after a number, inside math | factorial |
| `<` · `>` | is less than · is greater than |
| `( )` | not read (they still count toward the 4) |

**Everyday numbers are read as written**: a run whose only symbols are `-` and `/` (dates
such as `11/09/2026`, ranges such as `9-5`, phone numbers such as `555-1234`, fractions such
as `1/2`); times such as `10:30`, prices such as `$4.99`, `50%` and `#1`; and numbers with a
comma every three digits, such as `$100,000,000,000,000.0`. A signed number in brackets after another
number, such as `500 (+12%)`, is read as written, not as math.

A text with nothing left to read (only emoji or symbols, say) is not spoken; the status line
says why.

### Local API for other apps

Turn on **Settings > Local API** to let other apps on this computer ask for speech. It listens on
`127.0.0.1` only, so only apps on this computer can reach it; web pages from elsewhere cannot use it.
The port is 8765 by default; type another from 1024 to 65535 (it applies when you finish typing).
The API starts once the voice model has loaded. While a model reloads it keeps listening and the
web page keeps working; speech answers 503 until the new model is ready. A port another app holds
shows the reason under the setting; turn the switch off and on, or type another port, to try again.

The full reference, with every call, `curl` and Python examples, setup on a headless Linux
machine and the voice file formats, is the [aiVoiceTools API Manual](aiVoiceTools-API-Manual.md).

The app also describes its own API. `GET /openapi.json` answers an OpenAPI 3.1 description of every
call, with its fields, limits, examples and answers, so another app can read it and know how to use
the API. <http://127.0.0.1:8765/docs> shows the same description as a page in your browser, with a
`curl` example for each call. Both come from the app itself and need no internet connection.

Speech is made one request at a time, so two renders never share the GPU's memory. A request that
comes while another is speaking (from the API, the web page or this window) waits in line and
starts when the one before it ends; its answer begins then. Up to 4 requests are answered at once;
a fifth gets 429 (busy) until one is done.

Speech, as a WAV file:

```
curl -s -X POST http://127.0.0.1:8765/v1/audio/speech -H 'Content-Type: application/json' -d '{"input":"Hello from another app.","response_format":"wav"}' -o hello.wav
```

The JSON body:

| Field | Meaning |
| --- | --- |
| `input` | The text to speak (required); voice tags work here too. |
| `instruction` | The voice description; the app's when left out. Not used with a saved voice or a clip. |
| `seed` | 0 to 4294967295; the app's when left out. Not used with a saved or designed voice, or a clip. |
| `voice` | A preset or saved voice by name, or `designed:<name>` for a designed voice, from `GET /v1/voices`. A preset brings its description, a designed voice its description, seed and strictness, whatever the request sends. |
| `ref_audio_path` | A clip on this computer to clone (WAV, MP3, FLAC or Ogg Vorbis, any length; its dead air is removed and the first 30 s of the rest are used, ending at a pause); with `ref_text`, a clip with more than 30 s of speech (20 s with MOSS-TTS) is refused, since its words would not match the part used. |
| `ref_text` | The exact words spoken in the part used. Leave it out, with a Transcribe model downloaded, and the app writes the words down itself and ends the clip after the last whole word within 30 s (20 s with MOSS-TTS). |
| `max_frames` | The most frames (80 ms each) per piece, 1 to 1500; the app's Max length when left out (Auto estimates from the text). |
| `target_seconds` | Fixed length (MOSS-TTS only), 1 to 600: the speech fills this many seconds (see [Fixed length](#fixed-length-moss-tts)). With Breeze-TTS-2, or with a `speed` other than 1, the request is refused with 400. The answer's `X-Natural-Seconds` header gives the text's natural length, and `X-Length-Warning` says when the text is too long or too short for the time. `POST /v1/audio/length` with `input` and `target_seconds` answers the same without speaking. |
| `speed` | 0.5 to 2.0; 1.0 when left out. |
| `cfg_scale` | Strictness, 0 to 100; 1 turns it off; the app's when left out. Not used with a saved or designed voice or a clip. |
| `response_format` | `pcm` (the default): raw 24 kHz mono 16-bit little-endian samples, streamed as they are made. `wav`: one WAV file. |
| `play` | `true` also plays the speech on this computer. |
| `save` | `true` also saves a WAV in the save folder. |
| `whole` | `true` makes a `pcm` answer like a saved file, for a caller that does not play it as it comes: whole pieces, and the Noise Gate setting applies. |
| `program` | The one-word name of your program (1 to 32 letters, digits, `-` and `_`, such as `Reader` or `my_tool`), shown in the **API** tab instead of "app". Send it on every request. |
| `language` | MOSS-TTS only: a language hint, one of `en`, `zh`, `ja`, `ko`, `de`, `fr`, `es`, `it`, `pt`, `ru`, or its English name (`German`); `auto` or empty means no hint. |
| `audio_temperature`, `audio_top_p`, `audio_top_k`, `audio_repetition_penalty`, `text_temperature`, `text_top_p`, `text_top_k` | MOSS-TTS only: sampling, as under **Advanced Settings (Danger)** in [Settings](#settings). Temperatures 0 to 3, top-p 0.01 to 1, top-k 0 to 1024, repetition penalty 1 to 2. They may also be sent inside a `sampling` object; a field at the top level wins. |

A sampling or language field left out takes the window's value while Advanced Settings (Danger)
is on, and the model's own value otherwise.

A request without `voice` uses the Voice design fields, whatever Voice source says. Speech comes
from the model in use (HQ8-bit included). A `wav` answer that does not `play` follows the Noise
Gate setting; a `pcm` stream or `play` is made as it is heard, like Output: Both, and does not.
`GET /v1/voices` lists the voices. Requests are answered one at a time, after any speech the
window is making.

A request with no body (a `GET`) names its program in the `X-Program` header instead, with the
same one-word name; any request may. A name that is not one word is refused with 400.

`save` answers with the saved file's name in the `X-Saved-As` header. Files are never replaced: a
taken name gets `-2`, `-3` and so on.

A `wav` answer also says how long it took: `X-Audio-Seconds` and `X-Audio-Frames` (80 ms each)
give the speech's length, `X-First-Audio-Seconds` the seconds until its first sound was made, and
`X-Generate-Seconds` the seconds until it was all made, both counted from when the request
reached the speech engine (a wait for speech ahead of it included). A `pcm` answer sends its
headers before the speech is made, so it has none of these. Either answer carries
`X-Tuning-Warning` when an allowed value can spoil the take (an `audio_temperature` below 0.5).

`keep_take` (the page's Seed audition uses it) with `"response_format": "wav"` keeps the take for
Keep and answers with its id in the `X-Take-Id` header.

Errors answer with JSON `{"error": "..."}`: 400 for a bad body or an unknown voice, 403 for a
caller from elsewhere, 415 when the body is not `application/json`, 429 when too many requests
are waiting, 500 when speech cannot be saved or played, 503 while the model loads. A `pcm` answer
has begun before its speech is made, so a failure after that only ends the stream early, and a
file named in `X-Saved-As` may not be there.

#### Speech to text

The API also writes speech down, in the form other apps already send: a multipart form with the
audio as `file` (WAV, MP3, FLAC or Ogg Vorbis, up to 512 MB and three hours).

```
curl -s http://127.0.0.1:8765/v1/audio/transcriptions -F file=@talk.mp3 -F response_format=srt -o talk.srt
```

`POST /v1/audio/translations` takes the same form and writes the speech in English. Large v3 Turbo
was not trained to translate, so a translation needs Large v3, Small or Base (409 otherwise).

| Field | Meaning |
| --- | --- |
| `file` | The audio (required), with its file name, whose extension says how to read it. |
| `model` | `openai/whisper-large-v3-turbo`, `openai/whisper-large-v3`, `openai/whisper-small` or `openai/whisper-base`, or a short name such as `large-v3-turbo` or `small`. Anything else, `whisper-1` included, uses the model in Settings: Large v3 Turbo on a GPU and Small on the CPU unless another was chosen. |
| `language` | A language code such as `en`; detected when left out (the Transcribe tab's choice does not apply). Not used for a translation. |
| `prompt` | Words likely to come up, such as names, given to the model as earlier text. |
| `response_format` | `json` (the default): `{"text": "..."}`. `verbose_json`: the task, the language code, the length, each segment with its times and scores, and, with word timings, every word with its times under `words`. `text`: one segment per line. `srt` or `vtt`: subtitles. |
| `temperature` | 0 to 1. 0 (the default) tries warmer guesses only when a window fails its checks; another value is the one used. |
| `timestamp_granularities[]` | `word` turns on word timings. |
| `word_timings` | `true` or `false`; when left out, on, as the Transcribe tab has it. Word timings make subtitles follow the speech more closely. |
| `speakers` | `off` (the default, or `false`), `auto` (or `true`), or a count from 2 to 8, which turns on word timings: tells voices apart and tags subtitles `[voice1]`, `[voice2]` (`<v voice1>` in VTT); `verbose_json` gives each segment and word a `speaker` number, and on `auto`, `speaker_join_costs` (the last joins of voices as [whole cost, cost per frame], the one that left one voice first) for tuning. |

`GET /v1/stt/models` lists the models, whether each is downloaded, the one used when a request
names none, the one loaded now and the language codes. A model must be downloaded first (on the
Transcribe tab). Errors answer like speech: 400 for a bad form or audio that cannot be read, 409
when the model is not downloaded or cannot translate, or when the web page's Stop ended it, 413
when the audio is too large, 415 when the body is not a multipart form, 429 when two transcripts
are already waiting, 500 when the engine fails, 503 when speech to text is not available.
Transcripts are made one at a time, the window's and the API's in the order asked. The model loads on the first request (a few seconds) and stays
loaded.

#### Listening to LiveChat

While LiveChat is on (on the Transcribe tab), a program asks for the next thing you say with
`GET /v1/listen`, naming itself in the `X-Program` header. The call waits until you finish a
phrase and answers it; with no speech before `timeout` seconds (60 when left out, 3600 at
most) it answers 204 with no body, and the program asks again:

```
curl -s -H "X-Program: MyBot" "http://127.0.0.1:8765/v1/listen?timeout=600"
```

```
{"text":"Hello, can you hear me?","final":true,"started_at":"2026-11-11T18:40:02.120-06:00","ended_at":"2026-11-11T18:40:04.900-06:00"}
```

Each call answers one phrase. Phrases you say between calls wait in order, and the next call
answers the oldest. The first program to call is on the call; another program gets 409 with
`{"busy": true, "on_call": "MyBot", "since": "..."}` until the call ends. It ends with
`POST /v1/listen/hangup` (same `X-Program`), after 30 seconds without a call, when **End call**
is clicked, or when LiveChat is turned off. While a call lasts, speech with `"play": true` from
any other program gets the same 409. 409 also means LiveChat is off; 400 means the program did
not name itself.

To answer quickly, the program on the call can send its reply one sentence at a time, each as
its own `POST /v1/audio/speech` with `"play": true`, as soon as it has the sentence. Speech
played through the API is heard one request after another, in the order sent, so the sentences
play back to back without overlapping. For the program on the call, playback starts at the first
sound made instead of gathering a few seconds first (`"play_without_lead": false` turns that
off; any other program can turn it on with `true`).

#### LiveChat stream

An app can also listen to every finished phrase as Server-Sent Events, one per event (this does
not join the call and takes nothing from it):

```
curl -sN http://127.0.0.1:8765/v1/audio/live
```

```
data: {"type":"final","seq":1,"phrase":1,"text":"Hello, can you hear me?","start":3.42,"end":5.1}
```

`start` and `end` are seconds from when LiveChat was turned on; `seq` counts the events. Add
`?partials=true` to also get `partial` events: the phrase being said, so far, sent again as it
grows (as often as the model keeps up, at most once a second), with the same `phrase` number as
the `final` that replaces it. Partial transcripts are made only while a listener asks for them.
When LiveChat is turned off, a last event `{"type":"ended","reason":"..."}` ends the stream. A
comment line (`: keep-alive`) goes out after 15 seconds without events. Two apps can listen at
once (429 for a third); 409 means LiveChat is off. `GET /v1/audio/live/status` says whether it is
on, the microphone, how many listen and the phrases so far (with `?microphones=true`, also the
microphones there are).

#### Seeing API use

While a request is being answered, a green line under the title says so: **API in use by another
app** (or **by the web page**) and the endpoint, then **API used N s ago** for 10 seconds after.
Otherwise it reads **API on, idle** while the Local API is on, and is hidden while it is off.
Click it to open the **API** tab. A round light sits before the status line at the bottom: gray
at rest, lit green while speech is being made, by **Generate** or through the Local API (another
app or the web page). While speech is made through the Local API (another app's or the web
page's) and the window is not busy with its own or showing a problem, the window's status line
reads **Generating API Speech (GPU busy)**, or **(CPU
busy)** when speech runs on the CPU, and goes back to what it said before once that speech ends.

The **API** tab lists each request as it happens, newest first: the time, who sent it (the
program's own name when it gives one, else "app" or "web page"), the method and endpoint, the status (red for an error), the **Server time**
(from when the app read the request to its last byte, counting up in green while it runs; with
`play`, until the sound ends; the caller's own timing also counts the connection and reading the
answer), and the start of the text to speak. The page's own
files and its background refreshes are not listed. Requests refused because they did not come
from this computer show as 403. The last 200 requests are kept until the app
quits; **Clear** empties the list. A **Local API** switch at the top of the tab turns the API on
and off, the same as the one in **Settings**. The web page has the same line, tab and switch;
turning the API off from the page asks first, because the page itself is served by the API. While
the API is off (or the app is closed) the page says **API off, disconnected**, and it reconnects
by itself once the API is turned on again in the window.

### GPU details and the log

- **Threads:** the engine never uses the efficiency cores: the number of threads is capped at the
  performance cores, and its threads ask macOS for user-initiated priority, which keeps them on
  those cores (macOS places threads itself; an app can steer them but not pin them).
- **Built-in GPUs:** a GPU built into the processor is passed over for the CPU, which is usually
  faster than it, except NVIDIA GB10 (DGX Spark) and AMD Ryzen AI Max (Radeon 8040S, 8050S,
  8060S).
- **Choosing the device for one copy:** the `AIVOICETOOLS_DEVICE` variable (`cpu` or `gpu`) sets
  the device for one copy of the app.
- **The GPU check:** on Windows and Linux the app first checks, in a separate copy of itself, that
  the GPU can run the model, so a GPU driver that crashes cannot take the app with it: the app
  then runs on the CPU and says why. With Auto the result is remembered until the next version or
  model; a GPU chosen by name is checked on every start.
- **The engine line:** it says where the engine runs, by the GPU's own name ("Apple GPU" on a
  Mac, for example "MOSS-TTS 1.5 · AMD Radeon RX 9070 XT"), followed while it runs on a GPU by
  "GPU Free:" and the GPU's free memory (on a Mac, Metal's working set for the app less what the
  app's GPU buffers hold, so a bigger model shows less free), or "Breeze-TTS-2 · CPU only (no
  usable GPU found)". Only the open tab's model stays on the GPU: Speak and Voices keep the voice
  model, Transcribe keeps speech to text.
- **On the CPU:** the first job on the CPU measures how fast it is, and a one-time note says so
  (on the CPU speech takes a few times its own length).
- **The log:** the GPU check (with how long it took), any reason for using the CPU, how long the
  window took to first draw and any frame that held the window up are written to
  `aivoicetools.log` in the app's data folder (on Windows `%APPDATA%\aiVoiceTools\data`).

### Running a second copy

A second, separate copy (for trying a new version beside the everyday one) starts with the
`AIVOICETOOLS_HOME` variable naming a folder: its settings, voices, recent audio, log and saved
speech go there, its title says "(separate copy)", and it runs alongside the everyday copy. It
uses the standard models folder (or the one `AIVOICETOOLS_MODELS` names), so nothing is
downloaded twice; it never starts a download by itself, and deleting a model in either copy
deletes it for both. Turn on the local API in only one copy, or give the separate copy another
port in Settings, since both use 8765 otherwise. For example, in PowerShell:
`$env:AIVOICETOOLS_HOME = "C:\claude\test-home"; .\aivoicetools.exe` (the variable stays set
for that PowerShell window); on a Mac:
`AIVOICETOOLS_HOME=~/test-home /Applications/aiVoiceTools.app/Contents/MacOS/aivoicetools`.

## License

aiVoiceTools is released under the MIT License with the Commons Clause License Condition v1.0 ([LICENSE](LICENSE)), copyright 2026 Take Two Partners, LLC. You may use, copy and share aiVoiceTools, but the Commons Clause does not let you sell it, or a product or service whose value comes mainly from it. This license applies from the release after 0.3.0. Version 0.3.0 and earlier releases keep the MIT License they shipped with. The voice model weights
come under their own licenses, listed in Credits below.

## Credits

- **Voice model:** [Breeze-TTS-2](https://huggingface.co/BreezeBlue/Breeze-TTS-2) by BreezeBlue.
  Breeze TTS 2 is licensed under the BreezeBlue Research and Non-Commercial License Agreement.
  Copyright (c) 2026 RESONIA, INC. All Rights Reserved. The weights are for research and
  non-commercial use only; commercial use needs a separate written commercial license from
  BreezeBlue (RESONIA, INC., doing business as BreezeBlue). The weights are not part of the app:
  you download them, and the app shows the
  [license](https://huggingface.co/BreezeBlue/Breeze-TTS-2/blob/main/LICENSE) for you to accept
  first. Its audio tokenizer is based on Qwen3-TTS by the Alibaba Qwen Team (Apache License 2.0).
- **Voice model:** [MOSS-TTS 1.5](https://huggingface.co/OpenMOSS-Team/MOSS-TTS-v1.5) by the
  OpenMOSS team ([Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)), as GGUF files
  converted by [ilintar](https://huggingface.co/ilintar/moss-tts-gguf) (Apache License 2.0). The
  weights are not part of the app: you download them when you choose MOSS. The MOSS engine is our
  own, written for aiVoiceTools in Rust on ggml after the published design.
- **Inference engine:** [Breeze-TTS-2.cpp](https://github.com/HoppouAI/Breeze-TTS-2.cpp) by
  HoppouAI (Apache License 2.0), included with aiVoiceTools changes listed in
  `crates/breeze-sys/VENDORED.md`; the GGUF weights come from
  [HoppouAI/Breeze-TTS-2.cpp](https://huggingface.co/HoppouAI/Breeze-TTS-2.cpp) on Hugging Face.
- **Speech to text:** our own engine, written for aiVoiceTools in Rust on ggml after the
  published Whisper design (Radford et al., "Robust Speech Recognition via Large-Scale Weak
  Supervision", 2022). The weights are OpenAI's Whisper models (MIT), downloaded from
  [Hugging Face](https://huggingface.co/openai).
- **Tensor library:** [ggml](https://github.com/ggml-org/ggml) (MIT).
- **MLX engine:** written for aiVoiceTools in Rust on [MLX](https://github.com/ml-explore/mlx) by
  Apple (MIT) through [mlx-rs](https://github.com/oxideai/mlx-rs) (MIT or Apache License 2.0);
  converted weights are the copies on Hugging Face tagged `breeze_tts` and `mlx`.
