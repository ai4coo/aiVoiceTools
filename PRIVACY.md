# Privacy

aiVoiceTools runs on your own computer. It has no account, no sign-in, no ads, no analytics and no
usage reports. Nothing you type, open, record or make is sent anywhere by the app.

## What stays on your computer

- The text you speak, the subtitle and text files you open, and the WAV files you save.
- Your voices: designed voices, cloned voices and the clips they are made from.
- Audio you transcribe, and what the microphone hears in LiveChat.
- Settings, the downloaded models and the log file (`aivoicetools.log`), all in the app's own
  folders (see [Where things are kept](README.md#where-things-are-kept)).

## What goes over the internet

The app connects to one place: [Hugging Face](https://huggingface.co), to download the voice and
transcription models, to check whether a model you have has a newer revision, and to read the list
of models in Settings. These requests ask only for model files and lists. Like any download,
Hugging Face sees your computer's internet address and that the request came from aiVoiceTools.
Their own [privacy policy](https://huggingface.co/privacy) covers what they do with that.

Once the models you use are downloaded, the app works with no internet connection.

## The microphone

The microphone is used only while you have LiveChat turned on, and only for the device you chose
in **Listen to**. What it hears is turned into text on your computer and is not recorded to a file
unless you save it. Your system asks your permission the first time.

## The local API and web page

When the Local API is on, it listens on `127.0.0.1` only, so only programs on the same computer can
reach it. Other computers on your network and web pages from other sites cannot use it.

## Files you share

An exported `.aivoice` file carries the voice and some facts about where it was made (the app
version, model, engine and the type of computer). It goes only where you send it.

## Changes

If a later version changes any of this, this page will say so with that release.

Questions: open an issue on this repository.
