# sb-jsfx-plugins
StarBrilliant's JSFX (Jesusonic Effects) audio plugins

## Installation

You need a JSFX compatible audio host application. One example is [Reaper](https://www.reaper.fm) (paid software).

Put the effects into the following path, creating empty folders if needed:
- `%AppData%\REAPER\Effects\StarBrilliant` (Windows)
- `~/Library/Application Support/REAPER/Effects/StarBrilliant` (macOS)
- `~/.config/REAPER/Effects/StarBrilliant` (Linux)

Then you will be able to see the plugins under the "JS" type.

If you are more comfortable using another DAW instead of Reaper, you can also load JSFX plugin using the freeware [ReaPlugs](https://www.reaper.fm/reaplugs/) VST suite.

## Live Loudness Normalizer

This plugin is used for live audio streaming. A typical scenario is a live program with music and speech, but the loudness is changing rapidly so it is hard to predict or adjust the volume. Other loudness normalizer may not be able to handle realtime situation. Therefore, I wrote this plugin.

Note: if you want to normalize non-realtime materials such as prerecorded albums, try ReplayGain 2.0 compatible [loudgain](https://github.com/Moonbase59/loudgain).

The algorithm measures the current Momentary Loudness, and compare it with the Target Loudness. When the difference between these values are high, the algorithm tries to adjust the gain at a higher rate. When the difference gradually approaches to zero, the rate of change also decreases to maintain audio quality and dynamic range. Additionally, special care is taken to handle the quiet sections and the silence between songs.

There are five parameters:

1. **Target Loudness (LUFS)**, default is -23 LUFS.

   The higher this value, the louder output will be. The standard loudness for television and radio is -23 LUFS, for YouTube and Spotify is -14 LUFS. If you are mastering for a background music, set this 20 LU lower than your foreground speech.

2. **Decay Half-life (sec)**, default is 3 second.

   The gain adjustment follows an exponential decay curve. The higher this value, the slower gain adjustment will be.

3. **Upper Hard Limit (LU)**, default is +13 LU.

   This value is added to Target Loudness (e.g. -23 LUFS + +13 LU = -10 LUFS). This is a hard limit to prevent sudden shock. Do not try to rely on this, or the audio quality will be dramatically damaged. If you dynamic range is very high, you might need to append a peak limiter (e.g. NP1136 Peak Limiter which is pre-installed with Reaper).

4. **Lower Inflection Level (LU)**, default is -6 LU.

   This value is added to Target Loudness (e.g. -23 LUFS + -6 LU = -29 LUFS). The changing rate decreases instead of increasing when the measured Momentary Loudness is below this level. This feature is designed to handle transitions from quiet sections and loud sections carefully, to prevent sudden loudness increase.

5. **Bottom Gate Level (LUFS)**, default -48 LUFS.

   When the measured Momentary Loudness is below this level, either between two songs, or when no one is speaking, the algorithm treats the signal as silence. If your material has background noise, set this value higher than the loudness of that noise, or the noise will be amplified to the target loudness. A more ideal solution is to prepend a noise remover plugin.

## Licenses

Most of these plugins are released under MIT licenses. However, I recommend you to check the header of each file to ensure the license.

If you are willing to include my plugins in other projects, I recommend you to contact me before doing that. This will allow me to provide further updates more easily.
