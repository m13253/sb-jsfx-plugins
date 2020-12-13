# sb-jsfx-plugins
StarBrilliant's JSFX (Jesusonic Effects) audio plugins

## Installation

You need a JSFX compatible audio host application. One example is [Reaper](https://www.reaper.fm).

Put the effects into the path `%AppData%\REAPER\Effects\StarBrilliant` (Windows) or `~/Library/Application Support/REAPER/Effects/StarBrilliant` (macOS) or `~/.config/REAPER/Effects/StarBrilliant` (Linux). Then you will be able to see the plugins under the "JS" type.

## Live Loudness Normalizer

This plugin is used for live audio streaming. A typical scenario is a live program with music and speech, but the loudness is changing rapidly so it is hard to predict or adjust the volume. Other loudness normalizer may not be able to handle realtime situation. Therefore, I wrote this plugin.

The algorithm determines the current Momentary Loudness, and compare it with the Target Loudness. When the difference between these values are high, the algorithm tries to adjust the gain at a higher rate. When the difference gradually approaches to zero, the rate of change also decreases to maintain audio quality and dynamic range. Additionally, special care is taken to handle the quiet sections and the silence between songs.

There are five parameters:

1. Target Loudness (LUFS), default is -23 LUFS.
   The higher this value, the louder output will be.

2. Decay Half-life (sec), default is 1 second.
   The higher this value, the slower gain adjustment will be.

3. Upper Hard Limit (LUFS), default is -10 LUFS.
   This is a hard limit to prevent sudden shock. Do not try to rely on this, or the audio quality will be dramatically damaged.

4. Lower Inflection Level (LU), default is -5 LU.
   This value is added to Target Loudness (e.g. -23 LUFS + -5 LU = -28 LUFS). The changing rate decreases instead of increasing when the Momentary Loudness is below this level. This feature is designed to handle transitions from quiet sections and loud sections carefully, to prevent audiences from being shocked.

5. Bottom Gate Level (LUFS), default -70 LUFS.
   When the Momentary Loudness is below this level, the algorithm treats the signal as silence, either between two songs, or when no one is speaking.

## Licenses

Most of these plugins are released under MIT licenses. However, I recommend you to check the header of each file to ensure the license.

If you are willing to include my plugins in other projects, I recommend you to contact me before doing that. This will allow me to provide further updates more easily.
