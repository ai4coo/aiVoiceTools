# aiVoiceTools API Manual

For aiVoiceTools **0.3.0** · listens on **127.0.0.1:8765** · every build (Mac, Windows, Linux, including a headless Linux arm64 machine such as an NVIDIA DGX Spark)

aiVoiceTools turns text into speech with the MOSS-TTS and Breeze-TTS-2 voice models, and speech into text with Whisper models, all on one computer. While its **Local API** is on, other programs can ask it for speech (and transcripts) over plain HTTP. This manual lists every call, with `curl` and Python examples, and how to use it on a headless Linux machine such as an NVIDIA DGX Spark reached over SSH.

This manual lives in the repository beside the README and is updated in the same change whenever the README's API text changes. Sections 1 to 8 are the API meant for programs; sections 9 and 11 are the web page's own calls, which give scripts full control (models, settings, voices, subtitles) but may change between versions.

------------------------------------------------------------------------

## 1. Setup on a headless Spark (over SSH)

### 1.1 Install

``` bash
sudo apt install ./aivoicetools_<version>_arm64.deb
```

The program is `aivoicetools`. It is a desktop app; there is no separate server program.

### 1.2 First run: needs a window once

A new install speaks with MOSS-TTS 1.5, whose download starts by itself on the first launch at the size picked for the computer (8-bit by default). The window is needed once, to turn the Local API on (or set it by hand, below), and again only to accept the Breeze-TTS-2 license if you want that family. Do this with X forwarding, or with a remote desktop session on the Spark:

``` bash
# from your own computer (an X server is needed there: XQuartz on a Mac, VcXsrv/X410 on Windows)
ssh -X user@spark
aivoicetools
```

In the window:

1.  Wait until the MOSS-TTS download bar finishes (Settings > Model shows it).
2.  Open **Settings** (the rightmost tab), go to **Local API** and turn it **on**. The port is 8765 unless you change it.
3.  Close the app. The settings are kept in `~/.config/aiVoiceTools/settings.toml`.

The API is **off by default**. If you cannot open the window for step 2, you can turn it on by hand while the app is closed: in `~/.config/aiVoiceTools/settings.toml` set

``` toml
api_enabled = true
api_port = 8765
```

### 1.3 Every later run: headless

``` bash
ssh user@spark
nohup xvfb-run -a aivoicetools > ~/aivoicetools.log 2>&1 &
```

(`sudo apt install xvfb` if `xvfb-run` is missing.) The app runs with an invisible screen and serves the API. Only one copy runs at a time. To stop it: `pkill aivoicetools`.

### 1.4 Reaching the API from your own computer: SSH tunnel

The API listens on `127.0.0.1` of the Spark only, so it cannot be reached over the network directly. Open a tunnel from your computer:

``` bash
ssh -N -L 8765:127.0.0.1:8765 user@spark
```

Leave it running. On your computer, `http://127.0.0.1:8765` now reaches the app on the Spark.

**Keep the local port the same as the app's port (8765 on both sides).** The app only answers requests whose `Host` header is `127.0.0.1:<its port>` or `localhost:<its port>`. A tunnel such as `-L 9000:127.0.0.1:8765` makes your tools send `Host: 127.0.0.1:9000`, and every request is refused with 403 "This API only works locally".

You can also skip the tunnel and run the examples on the Spark itself, inside the SSH session.

### 1.5 The web page

While the API is on, the whole app is also a web page: open <http://127.0.0.1:8765/> in a browser on your computer (through the tunnel). It has the same tabs as the window (Speak, Voices, Transcribe, API, Settings). Saved files can be downloaded from the page.

### 1.6 Quick check

``` bash
curl -s http://127.0.0.1:8765/v1/voices
```

A JSON list of voices means everything works. "Connection refused" means the tunnel is down or the app (or its API) is not running.

------------------------------------------------------------------------

## 2. The calls

| Method       | Path                                           | What it does                                                                                            |
|--------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| `GET`        | `/v1/voices`                                   | Lists the voices and the app's defaults.                                                                |
| `POST`       | `/v1/audio/speech`                             | Text to speech: answers WAV or streamed raw PCM.                                                        |
| `POST`       | `/v1/audio/length`                             | How a text fits a fixed length (MOSS-TTS only), without speaking it (section 4.6).                      |
| `POST`       | `/v1/audio/transcriptions`                     | Speech to text (same form as OpenAI's API).                                                             |
| `POST`       | `/v1/audio/translations`                       | Speech to English text.                                                                                 |
| `GET`        | `/v1/stt/models`                               | Lists the speech-to-text models and which are downloaded.                                               |
| `GET`        | `/v1/listen`                                   | LiveChat: waits for the next phrase you say and answers its text (long poll, section 5.4).              |
| `POST`       | `/v1/listen/hangup`                            | Ends your LiveChat call, so another program may start one.                                              |
| `GET`        | `/v1/audio/live`                               | LiveChat: every phrase as text, as Server-Sent Events (section 5.5).                                    |
| `GET`        | `/v1/audio/live/status`                        | Whether LiveChat is on, its microphone and the phrases so far.                                          |
| `GET`        | `/openapi.json`                                | An OpenAPI 3.1 description of every call, for programs (section 2.1).                                   |
| `GET`        | `/docs`                                        | The same description as a readable page in a browser (section 2.1).                                     |
| `GET`        | `/`                                            | The web page.                                                                                           |
| `GET`/`POST` | `/v1/app/...`, `/v1/page/...`, `/v1/files/...` | The web page's own calls: models, settings, saved voices, subtitles, files, status (sections 9 and 11). |

Paths under `/v1/app/`, `/v1/page/` and `/v1/files/` are the web page's own calls. Any HTTP client can use them too, and section 9 shows the useful ones (switching and downloading models, MOSS included), but they may change from version to version; the calls in the table above are the ones meant for programs.

All error answers are JSON: `{"error": "a sentence saying what went wrong"}`.

### 2.1 The API's own description (`/openapi.json` and `/docs`)

The app describes its own API, so a program (or a tool that builds clients from a spec) can learn every call without this manual:

``` bash
curl -s http://127.0.0.1:8765/openapi.json -o aivoicetools-openapi.json
```

It answers an [OpenAPI 3.1](https://spec.openapis.org/oas/v3.1.0) document: every call in the table above and every web page call, each with its fields, their limits and defaults, request examples, every answer with its status, headers and fields, and a summary of the rules (local only, `X-Program`, errors, the speech line). Its `info.version` is the running app's version and its `servers` entry the port the API listens on. The calls under `/v1/app/`, `/v1/page/` and `/v1/files/` are tagged as the web page's own calls, which may change between versions.

`http://127.0.0.1:8765/docs` shows the same description as a page: open it in a browser on the app's computer (or through the SSH tunnel in 1.4). Each call opens to its fields, answers and a `curl` example with a **Copy** button, and the box at the top filters the calls. The page reads `/openapi.json`, so the two always match, and neither needs an internet connection.

### Naming your program

Every call may say which program sent it, and the app's **API** tab then shows that name instead of "app". Use one word: 1 to 32 letters, digits, `-` and `_`. A speech request puts it in the JSON body as `"program"`; any request, a `GET` included, can send the `X-Program` header instead:

``` bash
curl -s -H 'X-Program: my_tool' http://127.0.0.1:8765/v1/voices
```

A name that is not one word is refused with 400 and a message that says so.

------------------------------------------------------------------------

## 3. `GET /v1/voices`

``` bash
curl -s http://127.0.0.1:8765/v1/voices
```

Answer:

``` json
{
  "voices": ["Calm narrator", "Warm woman", "Deep man", "...", "My saved voice", "designed:News Reader"],
  "presets": [{"label": "Calm narrator", "instruction": "A clear, calm adult narrator."}, "..."],
  "default_instruction": "A clear, calm adult narrator.",
  "default_seed": 1,
  "default_strictness": 1.0
}
```

`voices` holds, in order: the presets, the saved voices (made in the app's **Voices** tab), and the designed voices as `designed:<name>`. Any of these names can go in the `voice` field of a speech request. The `default_*` fields are what a speech request gets for fields it leaves out.

Built-in presets:

| Name                                                | Description                                                                        |
|-----------------------------------------------------|------------------------------------------------------------------------------------|
| Calm narrator                                       | A clear, calm adult narrator.                                                      |
| Warm woman                                          | A warm, friendly adult woman speaking naturally.                                   |
| Deep man                                            | A deep, steady adult man speaking naturally.                                       |
| Bright young voice                                  | A bright, lively young adult speaking with energy.                                 |
| Warm, thoughtful young woman                        | A warm, thoughtful young woman with a clear voice and a calm, reflective delivery. |
| Calm, deep male narrator                            | A calm man with a deep, steady voice and a measured, natural delivery.             |
| Friendly, upbeat narrator                           | A friendly, upbeat adult narrator with clear articulation and a natural pace.      |
| No instruction (a different random voice each seed) | (empty)                                                                            |

------------------------------------------------------------------------

## 4. `POST /v1/audio/speech` (text to speech)

The body must be JSON, sent with `Content-Type: application/json` (otherwise 415).

### 4.1 Fields

| Field             | Type                     | Meaning                                                                                                                                                                          |
|-------------------|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `input`           | string, **required**     | The text to speak, up to 100,000 characters. Voice tags work here (see 4.4).                                                                                                     |
| `response_format` | `"pcm"` or `"wav"`       | `pcm` (the default): raw audio streamed while it is made. `wav`: one complete WAV file.                                                                                          |
| `voice`           | string                   | A name from `GET /v1/voices`: a preset, a saved voice, or `designed:<name>`. A preset brings its description; a designed voice brings its description, seed and strictness.      |
| `instruction`     | string                   | A voice description in plain words, up to 2,000 characters, for example `"A deep, slow male voice."` The app's default when left out. Not used with a saved voice or a clip.     |
| `seed`            | integer, 0 to 4294967295 | Picks one voice for a description: the same seed and description give the same voice. The app's default when left out. Not used with a saved or designed voice, or a clip.       |
| `speed`           | number, 0.5 to 2.0       | 1.0 when left out.                                                                                                                                                               |
| `cfg_scale`       | number, 0 to 100         | Strictness: how closely the voice follows the description. 1 turns it off. The app's default when left out. Not used with a saved or designed voice, or a clip.                  |
| `max_frames`      | integer, 1 to 1500       | The longest piece, in 80 ms frames (1500 = 2 minutes). The app's Max length setting when left out. Long texts are split into pieces anyway.                                      |
| `target_seconds`  | number, 1 to 600         | Fixed length, **MOSS-TTS only**: the speech fills this many seconds. MOSS paces its reading to fit; a WAV or `whole` answer that comes out up to 5% long is sped up evenly to end exactly on the length (a live PCM stream can end a fraction of a second late); a text too short ends naturally and silence fills the rest; a text too long runs over and is never cut. Refused with 400 while Breeze-TTS-2 is in use, or when `speed` other than 1 is sent with it. Never taken from the app's settings: send it on each request that wants it. |
| `ref_audio_path`  | string                   | Voice cloning: a path **on the computer running the app** (the Spark) to a WAV, MP3, FLAC or Ogg Vorbis clip. Dead air is removed and the first 30 s are used. Needs `ref_text`. |
| `ref_text`        | string                   | The exact words spoken in that clip, up to 2,000 characters.                                                                                                                     |
| `save`            | boolean                  | `true` also saves a WAV in the app's save folder; the answer's `X-Saved-As` header names the file.                                                                               |
| `play`            | boolean                  | `true` also plays the speech on the app computer's speakers. On a headless Spark there is usually no speaker (HDMI only), and the request may fail with 500; leave it off.       |
| `play_without_lead` | boolean                | With `play`: `true` starts playing at the first sound made instead of gathering a few seconds first (a gap is possible on a slow computer). On by default for the program on the LiveChat call. Speech played through the API is heard one request after another, in the order sent. |
| `whole`           | boolean                  | With `pcm`: makes the audio like a saved file (whole pieces, Noise Gate applied), for a caller that does not play it while it arrives.                                           |
| `program`         | string                   | The one-word name of your program: 1 to 32 letters, digits, `-` and `_` (such as `Reader` or `my_tool`). The app's **API** tab shows it instead of "app". Send it on every request; a name of more than one word is refused with 400. |
| `language`        | string                   | MOSS-TTS only: a language hint, one of `en`, `zh`, `ja`, `ko`, `de`, `fr`, `es`, `it`, `pt`, `ru`, or its English name (`German`); `auto` or empty means no hint. Left out: the window's Advanced Settings value while that switch is on, otherwise none (the model tells from the text). |
| `audio_temperature` | number, 0 to 3         | MOSS-TTS only. Higher is livelier, lower steadier; 0 always takes the likeliest sound. Model value 1.7. |
| `audio_top_p`     | number, 0.01 to 1         | MOSS-TTS only. The share of likely sounds kept to choose from; 1 keeps all. Model value 0.8. |
| `audio_top_k`     | integer, 0 to 1024       | MOSS-TTS only. How many likeliest sounds are kept; 0 keeps all. Model value 25. |
| `audio_repetition_penalty` | number, 1 to 2  | MOSS-TTS only. Above 1, a sound used in the last 4 seconds is less likely again (can stop loops and stutters; try 1.05 to 1.2). Model value 1 (off). |
| `text_temperature`, `text_top_p`, `text_top_k` | as above | MOSS-TTS only: the same for the channel that decides when to keep speaking or stop. Model values 1.5, 1 and 50. |
| `sampling`        | object                   | The sampling fields above inside one object, as some callers send them. A field at the top level wins. |

Unknown fields are ignored.

### 4.2 The answer

**WAV** (`"response_format": "wav"`): status 200, `Content-Type: audio/wav`, a complete 24 kHz mono 16-bit WAV file. It arrives when all the speech is made.

**PCM** (the default): status 200, `Content-Type: audio/pcm;rate=24000;encoding=signed-int;bits=16;channels=1`, sent in chunks while the speech is made: raw 24,000 Hz, mono, signed 16-bit little-endian samples, with no header. The `X-Lead-Seconds` header says how many seconds of audio a live player should collect before it starts, so playback does not run dry.

Headers either way:

| Header           | When                                                                                                                             |
|------------------|----------------------------------------------------------------------------------------------------------------------------------|
| `X-Saved-As`     | With `"save": true`: the saved file's name, such as `speech-20261107-201530.wav`. Names are never reused (`-2`, `-3` are added). |
| `X-Lead-Seconds` | PCM answers only.                                                                                                                |
| `X-Audio-Seconds`, `X-Audio-Frames` | WAV answers only: the speech's length in seconds and in 80 ms frames. |
| `X-First-Audio-Seconds` | WAV answers only: seconds from when the request reached the speech engine until its first sound was made (a wait for speech ahead of it included). |
| `X-Tuning-Warning` | Either answer, when a value allowed but likely to spoil the take was used: now an `audio_temperature` below 0.5, which can stall a take (seconds of silence, lost words). |
| `X-Generate-Seconds` | WAV answers only: seconds from when the request reached the speech engine until all of it was made. |
| `X-Natural-Seconds` | With `target_seconds`: about how long the text takes at a natural pace, such as `24.3`.                                      |
| `X-Length-Warning`  | With `target_seconds`, when the text does not fit: why (too long, so it runs over; or too short, so silence fills the end).  |

A PCM answer starts before the speech is made, so if something fails later the stream simply ends early. Use WAV when you need to know it worked.

### 4.3 Examples with curl

Simplest, a WAV file:

``` bash
curl -s -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "Hello from the Spark.", "response_format": "wav"}' \
  -o hello.wav
```

A preset voice, a bit faster:

``` bash
curl -s -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "This is the deep voice.", "voice": "Deep man", "speed": 1.15, "response_format": "wav"}' \
  -o deep.wav
```

Your own description and seed (change the seed to hear another voice for the same description):

``` bash
curl -s -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "Good evening.", "instruction": "An elderly woman with a soft, warm voice.", "seed": 52, "response_format": "wav"}' \
  -o evening.wav
```

Exactly 30 seconds as a WAV (MOSS-TTS only; `-D -` prints the headers, including `X-Natural-Seconds` and any `X-Length-Warning`):

``` bash
curl -s -D - -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "Your thirty second promo text goes here.", "target_seconds": 30, "response_format": "wav"}' \
  -o promo.wav
```

Clone a voice from a clip that is on the Spark:

``` bash
curl -s -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "Now I sound like the clip.", "ref_audio_path": "/home/user/clips/me.wav", "ref_text": "The exact words I say in the clip.", "response_format": "wav"}' \
  -o cloned.wav
```

Speak a whole text file (`jq` builds safe JSON from any text):

``` bash
jq -Rs '{input: ., response_format: "wav"}' story.txt |
  curl -s -X POST http://127.0.0.1:8765/v1/audio/speech \
    -H 'Content-Type: application/json' --data-binary @- -o story.wav
```

Stream PCM straight into a player as it is made (needs `ffmpeg`/`ffplay` on your computer):

``` bash
curl -sN -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "Streaming while it is made."}' |
  ffplay -autoexit -nodisp -f s16le -ar 24000 -ch_layout mono -i -
```

(On older ffmpeg use `-ac 1` instead of `-ch_layout mono`.)

Also keep a copy on the Spark, and see its name:

``` bash
curl -s -D - -X POST http://127.0.0.1:8765/v1/audio/speech \
  -H 'Content-Type: application/json' \
  -d '{"input": "Keep this one.", "response_format": "wav", "save": true}' \
  -o keep.wav | grep -i x-saved-as
```

### 4.4 Several voices in one text (voice tags)

Define voices at the very start of `input`, then switch with tags:

    [voice1=1234]
    [voice2=Calm narrator]
    [voice3= "an elderly woman with a soft voice seed 52"]
    [voice1] Good morning. [voice2] And good morning to you. [voice3] Hello, both of you.

- `[voiceN=1234]`: a seed, with the request's description.
- `[voiceN=Name]`: a saved voice or preset by name.
- `[voiceN= "description seed N"]`: a description with its own seed.
- `[voiceN]`: switch to that voice. Text before the first tag uses the request's voice.

A tag that is not defined, or a name with no voice, fails with 400 before any audio. Other brackets such as `[sigh]` stay in the text.

### 4.5 Python

Only the standard library is needed for these (Python 3.8+); `requests` versions follow.

``` python
import json
import urllib.error
import urllib.request

API = "http://127.0.0.1:8765"

def speak_to_wav(text, path, **fields):
    body = {"input": text, "response_format": "wav", **fields}
    req = urllib.request.Request(
        f"{API}/v1/audio/speech",
        data=json.dumps(body).encode("utf-8"),
        headers={"Content-Type": "application/json"},
        method="POST",
    )
    try:
        with urllib.request.urlopen(req, timeout=600) as answer:
            with open(path, "wb") as out_file:
                out_file.write(answer.read())
            return answer.headers.get("X-Saved-As")
    except urllib.error.HTTPError as problem:
        message = json.loads(problem.read() or b"{}").get("error", "")
        raise RuntimeError(f"{problem.code}: {message}") from None

speak_to_wav("Hello from Python.", "hello.wav")
speak_to_wav("A preset voice.", "preset.wav", voice="Warm woman", speed=1.1)
speak_to_wav("My own voice.", "own.wav", instruction="A calm man with a deep voice.", seed=7)
```

With `requests` (`pip install requests`):

``` python
import requests

API = "http://127.0.0.1:8765"

voices = requests.get(f"{API}/v1/voices", timeout=10).json()
print(voices["voices"])

answer = requests.post(
    f"{API}/v1/audio/speech",
    json={"input": "Hello from requests.", "voice": "Calm narrator", "response_format": "wav"},
    timeout=600,
)
if answer.ok:
    open("hello.wav", "wb").write(answer.content)
else:
    print(answer.status_code, answer.json()["error"])
```

Streaming PCM into a WAV file as it arrives (standard library `wave`):

``` python
import wave
import requests

with requests.post(
    "http://127.0.0.1:8765/v1/audio/speech",
    json={"input": "Streamed, then written as a WAV file."},
    stream=True,
    timeout=600,
) as answer:
    answer.raise_for_status()
    print("player lead:", answer.headers.get("X-Lead-Seconds"), "s")
    with wave.open("streamed.wav", "wb") as wav_file:
        wav_file.setnchannels(1)
        wav_file.setsampwidth(2)      # 16-bit
        wav_file.setframerate(24000)
        for chunk in answer.iter_content(chunk_size=None):
            wav_file.writeframes(chunk)
```

------------------------------------------------------------------------

### 4.6 `POST /v1/audio/length` (how a text fits a fixed length)

MOSS-TTS only. Answers without speaking, with the same plan a speech request with `target_seconds` uses. The body is `input` and `target_seconds` (both required):

``` bash
curl -s -X POST http://127.0.0.1:8765/v1/audio/length \
  -H 'Content-Type: application/json' \
  -d '{"input": "Your thirty second promo text goes here.", "target_seconds": 30}'
```

The answer: `{"natural_seconds": 3.0, "target_seconds": 30.0, "fit": "ends_early", "warning": "Most of the 30.0 s will be silence: ..."}`. `fit` is `fits`, `ends_early` (silence fills the rest) or `too_long` (the speech runs over); `warning` is `null` when it fits. With Breeze-TTS-2 in use, or a length outside 1 to 600, it answers 400.

## 5. `POST /v1/audio/transcriptions` and `/v1/audio/translations` (speech to text)

The same form OpenAI's audio API takes: `multipart/form-data` with the audio as `file`. The audio can be WAV, MP3, FLAC or Ogg Vorbis, up to 512 MB and 3 hours. The file name's extension says how to read it.

A speech-to-text model must be downloaded first, on the app's **Transcribe** tab (or the web page's). `GET /v1/stt/models` shows which ones are there.

`/v1/audio/translations` takes the same form and writes the speech in English. Large v3 Turbo cannot translate; use Large v3, Small or Base for that (409 otherwise).

### 5.1 Fields

| Field                       | Meaning                                                                                                                                                                                                                                                                      |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `file`                      | The audio, **required**.                                                                                                                                                                                                                                                     |
| `model`                     | `openai/whisper-large-v3-turbo`, `openai/whisper-large-v3`, `openai/whisper-small`, `openai/whisper-base`, or a short name such as `large-v3-turbo` or `small`. Anything else (`whisper-1` included) uses the app's chosen model: Large v3 Turbo on a GPU, Small on the CPU. |
| `language`                  | A code such as `en`, `de`, `ja`; detected when left out. Not used for translations.                                                                                                                                                                                          |
| `prompt`                    | Words likely to come up (names, terms), given to the model as earlier text.                                                                                                                                                                                                  |
| `response_format`           | `json` (the default): `{"text": "..."}`. `verbose_json`: language, duration, segments with times and scores, and `words` with word timings. `text`: plain text. `srt` or `vtt`: subtitles.                                                                                   |
| `temperature`               | 0 to 1. 0 (the default) tries warmer guesses only when needed.                                                                                                                                                                                                               |
| `timestamp_granularities[]` | `word` turns on word timings.                                                                                                                                                                                                                                                |
| `word_timings`              | `true` or `false`; on when left out. Makes subtitles follow the speech more closely.                                                                                                                                                                                         |
| `speakers`                  | `off` (the default), `auto`, or a count from 2 to 8: tells voices apart and tags subtitles `[voice1]`, `[voice2]`; `verbose_json` gives each segment and word a `speaker` number.                                                                                            |

### 5.2 Examples

``` bash
# plain text in JSON
curl -s http://127.0.0.1:8765/v1/audio/transcriptions -F file=@talk.mp3

# subtitles, two speakers, English
curl -s http://127.0.0.1:8765/v1/audio/transcriptions \
  -F file=@interview.wav -F response_format=srt -F speakers=2 -F language=en -o interview.srt

# translate German speech into English text
curl -s http://127.0.0.1:8765/v1/audio/translations \
  -F file=@german.mp3 -F model=small -F response_format=text
```

Python with `requests`:

``` python
import requests

with open("talk.mp3", "rb") as audio:
    answer = requests.post(
        "http://127.0.0.1:8765/v1/audio/transcriptions",
        files={"file": ("talk.mp3", audio, "audio/mpeg")},
        data={"response_format": "verbose_json", "language": "en"},
        timeout=3600,
    )
answer.raise_for_status()
result = answer.json()
print(result["text"])
for segment in result["segments"]:
    print(f'{segment["start"]:7.2f} {segment["end"]:7.2f} {segment["text"]}')
```

The official `openai` Python package also works, pointed at the app:

``` python
from openai import OpenAI

client = OpenAI(base_url="http://127.0.0.1:8765/v1", api_key="not-used")
with open("talk.mp3", "rb") as audio:
    print(client.audio.transcriptions.create(model="whisper-1", file=audio).text)
```

Transcripts are made one at a time. The model loads on the first request (a few seconds) and then stays loaded.

### 5.4 LiveChat: hearing what is said (`GET /v1/listen`)

Turn on **LiveChat** on the Transcribe tab (in the window or the web page) and choose the input in **Listen to**: a microphone, a virtual device, or `System sound: <output>` for what the computer plays (Windows, and macOS 14.6 or later with Screen & System Audio Recording access). The app then writes down what it hears, one phrase at a time. A phrase ends after a pause: **Pause** on the LiveChat row (0.65 seconds by default, about what a voice assistant waits; 0.5 to 3), so a short breath mid-sentence does not end it (raise it if you pause longer while thinking); a phrase without a pause is cut at its quietest moment at 25 seconds. It uses the Transcribe tab's language, and its model unless **Model** on the LiveChat row names another (Small writes each phrase sooner). While the app plays speech (from the window, the page, or `/v1/audio/speech` with `"play": true`), and for half a second after, the microphone is not heard, so a program talking through the app does not hear itself.

A program asks for the next phrase with a long poll, naming itself in the `X-Program` header (or `?program=`):

``` bash
curl -s -H "X-Program: MyBot" "http://127.0.0.1:8765/v1/listen?timeout=600"
```

- **200**, when a phrase is finished: `{"text": "Hello, can you hear me?", "final": true, "started_at": "2026-11-11T18:40:02.120-06:00", "ended_at": "2026-11-11T18:40:04.900-06:00"}` (local time with its offset).
- **204** (no body), when `timeout` seconds pass with no phrase: ask again. `timeout` is 60 when left out, 3600 at most; keep it below your HTTP client's own time limit.
- One phrase per call. Phrases said between calls wait in order (up to 100), and each call answers the oldest.
- **The call:** the first program to ask is on the call. Another program gets **409** `{"error": "Busy: MyBot is on a call (since 18:39:50).", "busy": true, "on_call": "MyBot", "since": "2026-11-11T18:39:50-06:00"}`. The call ends with `POST /v1/listen/hangup` from the same program, 30 seconds after its last call ended (so keep a call waiting), when **End call** is clicked on the LiveChat row, or when LiveChat is turned off. A new call hears only what is said from then on. During a call, `/v1/audio/speech` with `"play": true` from any other program also gets this 409 (speech into a file still works).
- **409** `{"error": "LiveChat is off: ..."}` while LiveChat is off (or when it is turned off, or the call ended, while a call waits). **400** when the program did not name itself or `timeout` is not a number.

``` bash
# hang up
curl -s -X POST -H "X-Program: MyBot" http://127.0.0.1:8765/v1/listen/hangup
```

**Answering quickly:** send the reply one sentence at a time, each as its own `/v1/audio/speech` with `"play": true`, as soon as the sentence is written. Speech played through the API is heard in the order sent, one after another, so the sentences play back to back; for the program on the call, each starts at its first sound (`play_without_lead`, on by default for it).

A conversation loop in Python, with no extra package (it speaks its answer through the app, which mutes the microphone meanwhile):

``` python
import json, urllib.request

API = "http://127.0.0.1:8765"
HEADERS = {"X-Program": "MyBot"}

def listen():
    request = urllib.request.Request(f"{API}/v1/listen?timeout=600", headers=HEADERS)
    with urllib.request.urlopen(request, timeout=700) as answer:
        return None if answer.status == 204 else json.load(answer)["text"]

def say(text):
    body = json.dumps({"input": text, "play": True, "response_format": "wav"}).encode()
    request = urllib.request.Request(f"{API}/v1/audio/speech", data=body,
                                     headers={**HEADERS, "Content-Type": "application/json"})
    urllib.request.urlopen(request).read()

while True:
    heard = listen()
    if heard:
        # one call per sentence: the first plays while the next is made
        for sentence in [f"You said: {heard}.", "What next?"]:
            say(sentence)
```

### 5.5 LiveChat as a stream (`GET /v1/audio/live`)

An app can also follow every finished phrase with Server-Sent Events; this does not join the call and takes nothing from `/v1/listen`. Each event is one `data:` line of JSON and a blank line.

``` bash
curl -sN http://127.0.0.1:8765/v1/audio/live
```

```
: LiveChat is listening

data: {"type":"final","seq":1,"phrase":1,"text":"Hello, can you hear me?","start":3.42,"end":5.1}

data: {"type":"final","seq":2,"phrase":2,"text":"Good, let's begin.","start":6.0,"end":7.38}

data: {"type":"ended","reason":"LiveChat was turned off."}
```

| Field | Meaning |
| --- | --- |
| `type` | `final`: a finished phrase, which does not change again. `partial`: only with `?partials=true`, the phrase being said, so far. `ended`: LiveChat was turned off or stopped (`reason` says why); the stream ends after it. |
| `seq` | Counts the events since LiveChat was turned on, from 1. |
| `phrase` | The phrase's number, from 1; a phrase's partials and its final share it. |
| `text` | The words. |
| `start`, `end` | Seconds from when LiveChat was turned on. |

- `?partials=true` also sends `partial` events: the phrase so far, sent again as it grows, as often as the model keeps up and at most once a second. The `final` with the same `phrase` number replaces them. Partial transcripts are made only while a listener asks for them, so without it the model runs once per phrase.
- After 15 seconds with no event a comment line `: keep-alive` goes out; clients ignore it.
- Two apps can listen at once; a third gets 429. While LiveChat is off the answer is 409 (`{"error": "LiveChat is off: ..."}`). Listen with HTTP/1.1.
- How soon a phrase arrives after the pause depends on the model and the machine: each phrase is one transcript (Whisper reads a 30-second window, however short the phrase). Small or Base answer sooner than Large v3 Turbo.

Python, with no extra package:

``` python
import json, urllib.request

with urllib.request.urlopen("http://127.0.0.1:8765/v1/audio/live") as stream:
    for line in stream:
        line = line.decode("utf-8").strip()
        if not line.startswith("data: "):
            continue
        event = json.loads(line[6:])
        if event["type"] == "final":
            print(event["text"])
        elif event["type"] == "ended":
            break
```

`GET /v1/audio/live/status` answers `{"on": true, "microphone": "...", "chosen_microphone": null, "listeners": 1, "most_listeners": 2, "phrases": ["..."], "partial": null, "problem": null, "muted": false, "pause_seconds": 0.65, "hint": null, "on_call": "MyBot", "call_since": "..."}`: whether LiveChat is on, the microphone in use (or last used), the one chosen on the tab (`null` for the system's default), the listeners (streams and waiting `/v1/listen` calls), the finished phrases so far (the last 200), the phrase being said when partials are made, whether the microphone is muted because the app is speaking, the pause that ends a phrase, and the program on the call. `problem` says why LiveChat stopped by itself (for example a microphone unplugged); `hint` says when the microphone has sent only silence since it was turned on (on a Mac, usually microphone access refused). Add `?microphones=true` for the list of inputs too (microphones, virtual devices, and `System sound: <output>` entries, any of which the page's `/v1/page/live` takes as `microphone`).

------------------------------------------------------------------------

## 6. `GET /v1/stt/models`

``` bash
curl -s http://127.0.0.1:8765/v1/stt/models
```

Answer (shortened):

``` json
{
  "models": [
    {"id": "openai/whisper-large-v3-turbo", "name": "Large v3 Turbo", "note": "Best for a GPU: near Large v3, several times faster; cannot translate", "about_bytes": 1620000000, "downloaded": true},
    {"id": "openai/whisper-small", "name": "Small", "note": "Best for the CPU", "about_bytes": 967000000, "downloaded": false}
  ],
  "default": "openai/whisper-large-v3-turbo",
  "loaded": {"folder": "openai/whisper-large-v3-turbo", "device": "..."},
  "waiting": 0,
  "download": null,
  "languages": ["en", "zh", "de", "..."]
}
```

------------------------------------------------------------------------

## 7. Errors

Every error is JSON `{"error": "..."}` with one of these status codes:

| Status | Meaning                                                                                                                                                                                                                   |
|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400    | A bad body: not valid JSON, `input` empty or too long, an unknown voice, a voice tag mistake, a value out of range, a clip that cannot be read, a bad form or unreadable audio. The message says which.                   |
| 403    | "This API only works locally": the request did not come from the app's own computer, its `Host` is not `127.0.0.1:<port>` or `localhost:<port>` (see the tunnel note in 1.4), or it came from a web page on another site. |
| 404    | No such call.                                                                                                                                                                                                             |
| 409    | Speech to text: the model is not downloaded, cannot translate, or the transcript was stopped. LiveChat: it is off, or another program is on the call (`"busy": true`, also for speech with `"play": true`).                |
| 413    | The audio upload is larger than 512 MB.                                                                                                                                                                                   |
| 415    | Speech: the body was not sent as `application/json`. Speech to text: not a `multipart/form-data` form.                                                                                                                    |
| 429    | Busy: more than 4 requests at once, 2 transcripts already waiting, or 2 apps already listening to LiveChat. Try again shortly.                                                                                            |
| 500    | The speech could not be made, saved or played (for example `play` with no speaker).                                                                                                                                       |
| 503    | No speech can be made yet. The message says why (below).                                                                                                                                                                  |

The 503 messages for speech:

| Message                                                                                           | What to do                                                          |
|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------|
| `Model not yet downloaded. Speech works once the download finishes.`                              | Wait for the download (see the window or the web page).             |
| `Model missing. Download the voice model first.`                                                  | Download a model: in the window or web page, **Settings \> Model**. |
| `The voice model's license has not been accepted yet. Set up the voice model first.`              | Open the window once and accept the license (1.2).                  |
| `The voice model is loading.` / `The speech engine is not ready; the voice model may be loading.` | Wait a few seconds and try again.                                   |
| `Speech was stopped in the app.`                                                                  | Someone pressed Stop in the window or page.                         |

Speech requests are made one at a time, after any speech the window is making; up to 4 can wait on the line at once.

------------------------------------------------------------------------

## 8. Where things are on Linux

| What                          | Where                                                                                                                                                      |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Settings                      | `~/.config/aiVoiceTools/settings.toml`                                                                                                                     |
| Models and saved voices       | `~/.local/share/aiVoiceTools/` (`models/`, `voices/`)                                                                                                      |
| Saved speech (`"save": true`) | `~/Music/aiVoiceTools` (or `~/Documents/aiVoiceTools`, or `~/aiVoiceTools` when those folders do not exist), unless another save folder is set in Settings |

------------------------------------------------------------------------

## 9. Models: switch and download without the window

Everything on the web page's **Settings \> Model** works from any HTTP client too, because the page itself only sends JSON requests to the app. So yes: downloading a model, switching models, and switching to MOSS can all be done with `curl`, a script, or the web page, whichever is handier.

The one thing that needs the window is **accepting the Breeze-TTS-2 license** on the first run (1.2). A Breeze download asked for before that answers `Accept the model license in the app first.` MOSS needs no license step.

These are the web page's own calls: they answer with the app's whole state as JSON (200), or `{"error": "..."}` with 400 when the app refuses (the message says why, such as `A download is running already.`). Each waits up to 10 s for the app, so the app (under `xvfb-run`) must be running. They may change in a later version.

### 9.1 What is there now

``` bash
curl -s http://127.0.0.1:8765/v1/app/state | jq '{family: .settings.family,
  breeze: [.breeze_models[] | {label, downloaded, in_use, downloading}],
  moss:   [.moss_models[]   | {weights, label, downloaded, in_use, downloading}],
  engine: .engine, status: .status.text}'
```

- `settings.family`: `breeze` or `moss`, the model family speaking now.
- `breeze_models`: the Breeze-TTS-2 models offered (the same list as the window), each with `label`, `size_bytes`, `downloaded`, `in_use`, and while downloading `downloading` (text) and `download_fraction` (0 to 1). Its `choice` object names the model in the calls below.
- `moss_models`: MOSS-TTS weights `bf16` (16-bit), `q8_0` (8-bit, the default) and `q4_k_m` (Small, 4-bit, made on this computer from the 16-bit file), the same fields.
- `engine.ready`, `engine.on_gpu`, `engine.busy`: whether speech can be made now, and where.

### 9.2 Breeze-TTS-2 models

`POST /v1/app/model` with `{"action": ..., "choice": <a choice object from breeze_models>}`. `action` is `download`, `cancel`, `use` or `delete`; `{"action": "refresh"}` (no `choice`) reads the list from Hugging Face again. Download first, then `use`: downloading alone does not change the model in use. Only models on the list can be named.

``` bash
API=http://127.0.0.1:8765
# download the "Standard" model (pick by any part of its label)
curl -s $API/v1/app/state |
  jq -c '{action: "download", choice: first(.breeze_models[] | select(.label | contains("Standard")) | .choice)}' |
  curl -s -X POST $API/v1/app/model -H 'Content-Type: application/json' --data-binary @- > /dev/null

# watch the download
watch -n 5 "curl -s $API/v1/app/state | jq -r '.breeze_models[] | select(.downloading) | .downloading'"

# then use it (the model reloads; speech answers 503 until it is ready)
curl -s $API/v1/app/state |
  jq -c '{action: "use", choice: first(.breeze_models[] | select(.label | contains("Standard")) | .choice)}' |
  curl -s -X POST $API/v1/app/model -H 'Content-Type: application/json' --data-binary @- | jq .status
```

### 9.3 Switching to MOSS-TTS

MOSS takes three steps: download its weights, pick them, then switch the family.

``` bash
API=http://127.0.0.1:8765
# 1. download the 8-bit weights (or "bf16" for 16-bit)
curl -s -X POST $API/v1/app/moss -H 'Content-Type: application/json' \
  -d '{"action": "download", "weights": "q8_0"}' > /dev/null
# watch: .moss_models[].downloading / .download_fraction in /v1/app/state

# 2. pick those weights
curl -s -X POST $API/v1/app/moss -H 'Content-Type: application/json' \
  -d '{"action": "use", "weights": "q8_0"}' > /dev/null

# 3. switch the family to MOSS (the engine reloads)
curl -s -X POST $API/v1/app/settings -H 'Content-Type: application/json' \
  -d '{"changes": {"family": "moss"}}' | jq '{family: .settings.family, engine: .engine}'

# back to Breeze-TTS-2
curl -s -X POST $API/v1/app/settings -H 'Content-Type: application/json' \
  -d '{"changes": {"family": "breeze"}}' > /dev/null
```

`/v1/app/moss` also takes `cancel` and `delete` (the weights in use cannot be deleted). A family switch is refused with `The engine is busy...` while speech is being made; try again after. `/v1/audio/speech` then speaks with MOSS, with the same fields. Saved voices are made with one family and say so in the app.

### 9.4 Other settings

`POST /v1/app/settings` with `{"changes": {...}}` changes settings by their name in `settings.toml`. Allowed: `instruction`, `seed`, `max_frames`, `fixed_length`, `target_seconds`, `voice_source`, `saved_voice`, `theme`, `play_after_subtitles`, `subtitle_timing`, `speed`, `strictness`, `output`, `api_enabled`, `engine_threads`, `device`, `gpu`, `noise_gate`, `compression`, `advanced_settings`, `speech_tuning`, `hq8`, `family`, `stt`. The defaults in `/v1/voices` (`default_instruction`, `default_seed`, `default_strictness`) follow these. For example:

``` bash
curl -s -X POST http://127.0.0.1:8765/v1/app/settings -H 'Content-Type: application/json' \
  -d '{"changes": {"seed": 7, "instruction": "A calm man with a deep voice."}}' > /dev/null
```

Values for the settings most worth changing:

| Setting           | Values                                                                                           |
|-------------------|--------------------------------------------------------------------------------------------------|
| `instruction`     | The default voice description (up to 2,000 characters).                                          |
| `seed`            | The default seed, 0 to 4294967295.                                                               |
| `speed`           | 0.5 to 2.0 (used by the window and page; an API request sends its own `speed`, default 1.0).     |
| `strictness`      | 0 to 100; 1 is off and fastest.                                                                  |
| `max_frames`      | 1 to 1500, or `null` for Auto.                                                                   |
| `fixed_length`    | `true` or `false`: the window's and page's Fixed length (MOSS-TTS only). Speech requests send their own `target_seconds` instead. |
| `target_seconds`  | 1 to 600: the window's and page's fixed length in seconds.                                       |
| `device`          | `auto`, `gpu` or `cpu`. Changing it reloads the model.                                           |
| `gpu`             | With `device` `gpu`, the GPU by its name; `null` takes the best one. Reloads the model.           |
| `engine_threads`  | A number of CPU threads, or `null` for all. Reloads the model.                                   |
| `hq8`             | `true` or `false` (HQ8-bit for HQ16-bit models). Reloads the model.                              |
| `noise_gate`      | `true` or `false`: smooths the quiet between words in saved speech and WAV answers.              |
| `compression`     | `true` or `false`: a gentle compressor (2:1, ceiling -3 dBFS) evens out loud and soft parts.     |
| `advanced_settings` | `true` or `false`: the Advanced Settings (Danger) switch. While it is on, the window, the page and API requests use `speech_tuning`. |
| `speech_tuning`   | An object with any of the sampling fields and `language` from 4.1, for example `{"audio_temperature": 1.4, "language": "de"}`; `{}` goes back to the model's values. A value out of range is refused with its field named. |
| `family`          | `breeze` or `moss`. Reloads the model.                                                           |
| `subtitle_timing` | `fit` (match the timecodes) or `natural`.                                                        |
| `output`          | `play`, `save` or `play_and_save` (the window's Output; API requests use `play`/`save` instead). |
| `voice_source`    | `designed` or `saved` (the window's Voice source).                                               |

A change that reloads the model is refused with `The engine is busy...` while speech is being made. Setting `api_enabled` to `false` turns the API off, and the API cannot turn itself back on.

### 9.5 Speech-to-text models

``` bash
# download one (names from GET /v1/stt/models)
curl -s -X POST http://127.0.0.1:8765/v1/page/stt/download -H 'Content-Type: application/json' \
  -d '{"model": "openai/whisper-small"}'
# progress: the "download" field of GET /v1/stt/models
curl -s http://127.0.0.1:8765/v1/stt/models | jq .download
```

`POST /v1/page/stt/download/cancel` with the body `{}` stops it.

------------------------------------------------------------------------

## 10. Getting good results

- **Same voice every time.** A description and seed give the same voice, but with Breeze-TTS-2 the opening words of a text also shape a voice made from a description. For a voice that must not drift across requests, use a **saved voice** (or keep a designed one, see 11.2): it follows one fixed clip and sound the same whatever the words.
- **Trying voices.** Keep the text and description, change only `seed`, and listen. Seed 1 is a good neutral start. The preset "No instruction" gives a quite different voice per seed.
- **Pace.** `speed` changes the tempo without changing the pitch (0.5 to 2.0). Words in the description such as "speaks slowly" or "speaks quickly" also work; a number such as "120 words per minute" or "in 30 seconds" does not reliably work. To hit an exact length, adjust `speed`.
- **Strictness** (`cfg_scale`): 1 (off) is fastest. Higher values follow the description more closely but take about twice as long.
- **Long texts** are split into pieces automatically and joined, so a whole chapter in one `input` is fine (up to 100,000 characters per request).
- **Text the models read.** Every text (speech requests, subtitles, the web page) goes through the same filter as the window's text box: letters, digits, normal punctuation and a few symbols are kept; emoji, emoticons and odd runs of symbols are not read. A text with nothing left to read (only emoji, say) is refused with an error that says so, instead of silent audio. The full list is in the README under [Text restrictions](README.md#text-restrictions).
- **Sounds and brackets.** Text such as `[sigh]` or `(laugh)` is left in the text and spoken as the model reads it; only `[voiceN]` tags are treated specially. Results vary by model and voice.
- **Faster first sound.** A `pcm` answer starts arriving after the first piece is made; a `wav` answer only after everything is made. On the CPU, speech takes a few times its own length.
- **MOSS vs Breeze.** The same seed gives different voices on the two families (and on different engines). Saved voices are tied to the family they were made with.

------------------------------------------------------------------------

## 11. More of the web page's calls

All of these answer JSON. Like section 9, they are the web page's own calls and may change in a later version. File uploads go inside the JSON as `{"file_name": "...", "data_base64": "..."}`. A small helper for the shell examples:

``` bash
API=http://127.0.0.1:8765
# (on a Mac use base64 -i "$1" instead of base64 -w0 "$1")
upload() { jq -n --arg name "$(basename "$1")" --rawfile data <(base64 -w0 "$1") '{file_name: $name, data_base64: $data}'; }
```

### 11.1 Saved voices (cloning from a clip)

Saving a voice from a clip once is faster and steadier than sending `ref_audio_path` on every request: the saved voice is then named in `voice`. The clip (WAV, MP3, FLAC or Ogg, up to 40 MB) is uploaded from your computer; dead air is removed and the first 30 s of speech are used, so the transcript must be exactly what is said in that part.

``` bash
# how much of the clip is used (and the used part as base64 WAV in used_wav_base64)
jq -n --argjson clip "$(upload me.wav)" '{clip: $clip}' |
  curl -s -X POST $API/v1/page/voices/clip-info -H 'Content-Type: application/json' --data-binary @- |
  jq '{kept_seconds, note}'

# hear a short line in that voice first (answers a WAV)
jq -n --argjson clip "$(upload me.wav)" --arg t "The exact words said in the clip." '{clip: $clip, transcript: $t}' |
  curl -s -X POST $API/v1/page/voices/try -H 'Content-Type: application/json' --data-binary @- -o try.wav

# save it as "Me" (answers once the voice is ready)
jq -n --argjson clip "$(upload me.wav)" --arg t "The exact words said in the clip." '{clip: $clip, transcript: $t, name: "Me"}' |
  curl -s -X POST $API/v1/app/voices/save -H 'Content-Type: application/json' --data-binary @- > /dev/null

# then speak with it
curl -s -X POST $API/v1/audio/speech -H 'Content-Type: application/json' \
  -d '{"input": "Now in my saved voice.", "voice": "Me", "response_format": "wav"}' -o me-speaks.wav
```

Rename and delete:

``` bash
curl -s -X POST $API/v1/app/voices/rename -H 'Content-Type: application/json' -d '{"name": "Me", "new_name": "Me 2"}' > /dev/null
curl -s -X POST $API/v1/app/voices/delete -H 'Content-Type: application/json' -d '{"name": "Me 2"}' > /dev/null
```

`/v1/app/voices/use` with `{"name": ...}` makes it the window's and page's voice (API requests still name their own `voice`).

### 11.2 Keeping a designed voice (a description and seed, saved as a clone)

Since 0.2.78, keeping a voice made from a description and seed saves it as a **saved voice**: the app speaks the short script once in that voice and clones the voice from it, so it then plays the same on every model and engine. The answer comes at once; the saving runs on, and the bottom light is lit while it does. The new voice appears in `GET /v1/voices` among the saved voices when it is done.

``` bash
curl -s -X POST $API/v1/app/keep -H 'Content-Type: application/json' \
  -d '{"seed": 7, "instruction": "A calm man with a deep, steady voice."}' > /dev/null
```

The app names it after the first words of the description and the seed. It is refused while another voice is being saved (`A voice is being saved; try again when it is done.`), while the model is loading, and on a model that cannot clone voices. Designed voices kept before 0.2.78 still appear as `designed:<name>` until each is saved as a clone, once, by itself, when the model it was made with is loaded. To rename, forget or use one of those, send its slot and current name:

``` bash
curl -s -X POST $API/v1/app/kept -H 'Content-Type: application/json' \
  -d '{"index": 0, "voice": "<current name>", "action": "rename", "name": "Narrator"}' > /dev/null
# "action": "forget" removes it, "use" makes it the window's voice
```

### 11.3 Speaking a subtitle file (.srt) into one timed WAV

The app can speak a whole `.srt` so each line starts at its timecode, with one voice per speaker, and save it as one WAV in its save folder. Upload it, look at the speakers, then speak:

``` bash
# 1. upload (an .srt, a .voices.txt or a plain .txt, up to 2 MB)
jq -n --argjson f "$(upload film.srt)" '{subtitles: $f}' |
  curl -s -X POST $API/v1/page/subtitles/open -H 'Content-Type: application/json' --data-binary @- |
  jq '{file_name, has_timecodes, speakers}'

# 2. optional: change a speaker's voice (by its tag number); "saved_voice" names a saved voice
curl -s -X POST $API/v1/page/subtitles/speakers -H 'Content-Type: application/json' \
  -d '{"speakers": [{"tag": 1, "gender": "male", "description": "A deep, steady adult man.", "seed": 12}]}' > /dev/null

# 3. speak it (waits until the whole file is made); takes the same voice fields as speech
curl -s -X POST $API/v1/page/subtitles/speak -H 'Content-Type: application/json' \
  -d '{"timing": "fit", "speed": 1.0}' | jq '{saved_as, fit_summary, seconds}'

# 4. download the result (and its fit summary)
curl -s -o film-fit.wav "$API/v1/files/saved/film-fit.wav"
```

- Speakers are found from cue lines like `[John] Hello.`, `JOHN: Hello.` or `<v John>Hello.</v>`; each gets a `[voiceN]` tag and a voice, kept in a `.voices.txt` next to the upload. `gender` is `male`, `female` or `unspecified`. `/v1/page/subtitles/recreate` (body `{}`) starts the speakers afresh.
- `"timing": "fit"` (the default) starts every cue at its timecode: short cues are padded with silence, long ones sped up (up to 1.35x, or spoken again faster, up to 2x), and a cue that still runs over pushes the next ones later. `"timing": "natural"` ignores the timecodes and reads the cues in order with 0.3 s pauses. A file without timecodes is always read naturally.
- The answer lists each cue with how it was fitted (`fit`, such as `sped 1.12x`), the WAV's name (`saved_as`) and the summary's (`fit_summary`). `GET /v1/page/subtitles` shows the open file again.
- One subtitle job runs at a time; `POST /v1/page/stop` (body `{}`) stops it.

### 11.4 Downloading saved files

Any file in the app's save folder can be downloaded by name, for example one named in an `X-Saved-As` header:

``` bash
curl -s -o speech.wav "$API/v1/files/saved/speech-20261107-201530.wav"
```

Only plain names in that folder work (404 otherwise). Names with spaces must be URL-encoded (`%20`).

### 11.5 Status, version and engine (health check)

``` bash
curl -s $API/v1/app/state | jq '{version: .about, engine, api, status: .status.text,
  family: .settings.family, license_accepted_at}'
```

- `about`: `version`, `build` and `commit` of the app.
- `engine`: `name` (for example `Breeze-TTS-2 · GPU (Vulkan)`), `ready` (speech can be made), `on_gpu`, `busy` (speech is being made now), `clones_voices`.
- `status.text`: the window's status line, such as `Ready.` or why no speech can be made.
- `download_problem`, and `downloading` / `download_fraction` in the model lists: download state.

A simple wait-until-ready loop for scripts:

``` bash
until curl -s $API/v1/app/state | jq -e '.engine.ready and (.engine.busy | not)' > /dev/null; do sleep 5; done
```

`GET /v1/page/activity` lists the last 200 API requests (time, caller, endpoint, status, server time), the same list as the app's **API** tab; `POST /v1/page/activity/clear` (body `{}`) empties it. Since 0.2.87 its answer also has `transcribing`: `true` while speech to text writes words down (a transcript, or a clip's words for a voice), from the window, the page or the API; the page's status light is lit while it is.

### 11.6 Stopping speech

`POST /v1/page/stop` (body `{}`) stops speech that was asked for with an `Origin` header, which the web page always sends. A script that wants its requests to be stoppable can send the same header on them:

``` bash
curl -s -X POST $API/v1/audio/speech -H 'Content-Type: application/json' \
  -H 'Origin: http://127.0.0.1:8765' -d '{"input": "A long text...", "response_format": "wav"}' -o long.wav &
curl -s -X POST $API/v1/page/stop -H 'Content-Type: application/json' -d '{}'
```

Requests without `Origin` run to the end (or until Stop is pressed in the window).

------------------------------------------------------------------------

## 12. Troubleshooting

| Symptom                                                                   | Likely cause and fix                                                                                                                                                                      |
|---------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Connection refused`                                                      | The tunnel is not running, the app is not running, or the Local API is off (1.2). Check with `pgrep aivoicetools` on the Spark.                                                           |
| 403 `This API only works locally`                                         | The tunnel uses another local port than the app (use 8765 on both sides), or the request names another host than `127.0.0.1`/`localhost`.                                                 |
| 503 on every speech request                                               | No model yet, still downloading, license not accepted, or the model is loading: read the message, and see 9.1 and 11.5.                                                                   |
| 400 `The app window did not answer.` / `The app window is not connected.` | A page call (sections 9 and 11) needs the app's window loop; restart the app under `xvfb-run`.                                                                                            |
| 429 `Busy`                                                                | More than 4 requests at once. Send speech one at a time and wait for each answer.                                                                                                         |
| A `pcm` stream ends early                                                 | A failure after the stream began. Use `"response_format": "wav"` to get a proper error.                                                                                                   |
| Speech is slow                                                            | It runs on the CPU: `engine.on_gpu` in `/v1/app/state` is false. Try `{"changes": {"device": "gpu"}}`; the reason for using the CPU is in `~/.local/share/aiVoiceTools/aivoicetools.log`. |
| The voice changes between requests                                        | Use a saved or designed voice (section 10).                                                                                                                                               |
| 500 with `play`                                                           | The Spark has no usable speaker. Leave `play` off and play the answer on your computer.                                                                                                   |
| The app stopped after logging out                                         | Start it with `nohup` (1.3), or inside `tmux`/`screen`.                                                                                                                                   |

------------------------------------------------------------------------

## 13. Voice file formats

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

------------------------------------------------------------------------

## 14. License note

The Breeze-TTS-2 voice model is licensed under the BreezeBlue Research and Non-Commercial License Agreement. Copyright (c) 2026 RESONIA, INC. All Rights Reserved. Speech made with it may be used only as that license allows (non-commercial). aiVoiceTools itself is under the MIT License.
