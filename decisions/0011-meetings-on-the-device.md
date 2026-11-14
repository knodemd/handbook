# 0011 - Meetings and media on the device

**Status:** proposed, 2026-11-14

This decision amends [0004](0004-product.md), which said "No AI inside the app".

## Context

Meetings are where many decisions are made, and they leave no trace in Knode. Tools that record
them usually send the audio to a cloud service and summarise it there. The same is true of in-person
meetings recorded on a phone or a camera, and of other personal recordings. A one-hour transcript
is too long to read or to hand an agent in full. Decision 0004 keeps all AI out of the app: the
agent is the AI and Knode is its memory. macOS 26 brings on-device speech recognition
(SpeechAnalyzer) and an on-device language model (the Foundation Models framework), so the raw
work of turning audio into notes can be done without anything leaving the Mac.

## Decision

- **Meetings are a built-in Mac connector** (a source, decision 0010). It records system audio
  through Core Audio process taps and the microphone, on the device.
- **Transcribed on the device** with SpeechAnalyzer, with whisper.cpp and a local model as the
  fallback for languages it does not cover. Transcripts keep timestamps and speakers.
- **Summarised on the device.** The app turns the transcript into a linked note, with decisions
  and action items, using Apple's on-device Foundation Models framework. No cloud and no Knode
  server. The transcript is always kept.
- **Personal media use the same pipeline.** Audio and video added to a space, including in-person
  meetings recorded on a phone or a camera, are transcribed and can be summarised the same way;
  photos are read with Vision.
- **Amends 0004.** The agent stays the AI that reasons over the memory. Knode may use on-device
  models, opt-in, to turn raw sources (audio, video, photos, long transcripts) into notes. Nothing
  leaves the device.
- **Recording consent.** A recording indicator is visible for as long as it records. Recording is
  a per-meeting toggle, never automatic. The user can post an optional notice in the meeting chat.
  The first time, Knode explains that some places require every participant's consent before
  recording. In-person recordings, on the Mac or the iPhone, follow the same rules.

## Consequences

- On-device summaries need a Mac with Apple Intelligence; without it, Knode keeps the transcript
  and says why there is no summary.
- Summaries carry provenance that names the on-device model, so they are never mistaken for the
  user's or an agent's writing.
- Audio and video are large; the storage view and limits from decision 0010 apply.
- The line between the app and the agent is now: the app may turn raw material into text on the
  device; it does not answer questions about the memory.
