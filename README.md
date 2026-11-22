# Life Planner

A simple button-operated voice-to-calendar project. Hold the button, wait for
the ready light, speak, and release. Home Assistant transcribes the request;
one AI conversation agent reads the calendar and creates a timed event.
View events on a native calendar card and results in a Home Assistant notice.

## Start here

Copy [prompts/setup-prompt.txt](prompts/setup-prompt.txt) into your setup chat.
The assistant asks about your parts, AI provider, calendar and speech services,
then adapts the files. Use [v0.1.0](https://github.com/kriya-edu/life-planner/tree/v0.1.0)
for matching firmware, scripts and prompts.

Direct setup prompt:
https://raw.githubusercontent.com/kriya-edu/life-planner/v0.1.0/prompts/setup-prompt.txt

The calendar and prompt files are generic templates. The ESPHome file is a
hardware-specific reference, not universal firmware. Confirm parts and adapt
it before wiring or flashing. These files have not been installed or physically
tested as this simplified package.

## Project files

| File | Use |
| --- | --- |
| `README.md` | Setup, file inventory, and hardware-reference notes |
| `.gitignore` | Excludes local credentials and build outputs |
| `prompts/setup-prompt.txt` | Discovery questions and setup workflow |
| `prompts/planner-instructions.txt` | Instructions for the chosen AI agent |
| `esphome/huzzah32-reference.yaml` | Reference hold-to-talk firmware to adapt |
| `esphome/secrets.yaml.example` | Placeholder secret names only |
| `home-assistant/read-calendar.yaml` | Individual read script |
| `home-assistant/create-event.yaml` | Individual create script with checks |
| `home-assistant/report-status.yaml` | Individual result-notice script |
| `home-assistant/calendar-card.yaml` | Native calendar card |

The blog draft is delivered separately. No live credentials are needed in
this repository. Do not upload your real secrets.yaml, API keys, access URLs,
calendar exports or compiled firmware containing credentials.

## Simple setup order

1. Identify existing hardware, calendar, AI and speech services.
2. Select a writable calendar; add read-only availability calendars if wanted.
3. Adapt and install the three separate scripts, then configure the AI prompt.
4. Test text-to-calendar before troubleshooting audio.
5. Configure a dedicated Assist pipeline with your chosen STT, agent and TTS.
6. Adapt, validate and flash the firmware, then pair and select that pipeline.
7. Add the native calendar card and test a physical spoken request.

Each home-assistant script YAML is the body for one script's YAML editor,
not a scripts.yaml mapping or configuration.yaml file. Confirm the actual
script entity IDs after installation. Replace calendar.life_planner in all
files. Add the same read-only calendars to calendar_entities in both scripts.

The create script serializes its own writes, rejects past/invalid times,
checks timed conflicts, and suppresses exact-title duplicates across the
queried dates. It does not provide semantic deduplication or a lock against
other calendar clients writing simultaneously. All-day events are context
and do not block a whole day. The agent reads back events before confirming;
external calendars can have sync delays. On an uncertain write, read before
retrying. Use the normal calendar interface for edits or deletion.

## Reference hardware only

The firmware was derived from the original HUZZAH32 build. It targets an
Adafruit HUZZAH32 Feather ESP32-WROOM-32E, 4 MB flash, no PSRAM, ESP-IDF,
with a standard I2S microphone and ESPHome 2026.11.0 or newer. Its release
handler uses ESPHome C++ APIs: validate and compile against your installed
version. A different board or mic requires a reviewed adaptation.

| Connection | Reference pin |
| --- | --- |
| Normally-open button contact | GPIO27, other switch contact to GND |
| Built-in red status LED | GPIO13, already connected |
| Microphone power | 3V and GND |
| Microphone BCLK/SCK | GPIO14 |
| Microphone WS/LRCLK | GPIO25, marked A1 |
| Microphone SD/DOUT | GPIO32 |
| Microphone channel select | GND only if its datasheet specifies left channel |

Disconnect power before wiring. The microphone model must be identified;
pin names alone do not establish voltage or channel selection. The reference
expects left-channel standard I2S, 16 kHz and 32-bit samples. It does not
configure an analog/PDM microphone or an illuminated button's LED terminals.

The LED is solid when listening is ready, blinks during processing, and
turns off at session end. Release ends input without aborting transcription.
Presses during processing are ignored. Sessions time out after 120 seconds;
a slow provider can outlast the LED session, so check Home Assistant before
repeating the request. There is no speaker, wake word or offline queue.

## Official references

- [ESPHome Voice Assistant](https://esphome.io/components/voice_assistant/)
- [ESPHome I2S microphone](https://esphome.io/components/microphone/i2s_audio/)
- [Home Assistant calendar actions](https://www.home-assistant.io/integrations/calendar/)
- [Home Assistant Local calendar](https://www.home-assistant.io/integrations/local_calendar/)
