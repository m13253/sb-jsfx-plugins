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

![Screenshot](screenshots/Live%20Loudness%20Normalizer.png)

This plugin is used for live audio streaming. A typical scenario is a live program with music and speech, but the loudness is changing from time to time so it is hard to predict or adjust the volume. Other loudness normalizer may not be able to handle realtime situation. Therefore, I wrote this plugin.

(Note: if you want to normalize non-realtime materials such as prerecorded albums, try ReplayGain 2.0 compatible [loudgain](https://github.com/Moonbase59/loudgain).)

The algorithm measures the current Momentary Loudness, and compare it with the Target Loudness. When the difference between these values are high, the algorithm tries to adjust the gain at a higher rate. When the difference gradually approaches to zero, the rate of change also decreases to maintain audio quality and dynamic range. Additionally, special care is taken to handle the quiet sections and the silence between songs.

There are five parameters:

1. **Target Loudness (LUFS)**, default is -23 LUFS.

   The higher this value, the louder output will be.

   | Platform of delivery | Reference loudness |
   |----------------------|--------------------|
   | YouTube              | -14 LUFS           |
   | Spotify              | -14 LUFS           |
   | Apple Music          | -16 LUFS           |
   | TV & Radio           | -23 LUFS           |

   | Content of delivery  | Reference loudness            |
   |----------------------|-------------------------------|
   | Pop music            | -14 LUFS or lower             |
   | Speech               | -16 LUFS                      |
   | Classical music      | -23 LUFS or lower             |
   | Background music     | Foreground speech minus 20 LU |

2. **Decay Half-life (sec)**, default is 3 seconds.

   The gain adjustment follows an exponential decay curve: The farther difference from target, the faster gain adjustment will be. The longer decay half-life, the slower gain adjustment will be.

   | Content of delivery  | Reference value |
   |----------------------|-----------------|
   | Music                | 3 - 10 seconds  |
   | Speech               | 3 seconds       |

   If you are curious, the gain adjustment formula is:
   ```
   diff_lu = target_lufs - current_lufs - gain_db

   d gain_db   diff_lu * ln(2)
   --------- = ---------------
       dt       half_life_sec
   ```
   when `diff_lu > 0`.

3. **Upper Hard Limit (LU)**, default is +13 LU.

   Values in "LU" are added to Target Loudness (e.g. -23 LUFS + +13 LU = -10 LUFS).

   This is a hard limit to prevent sudden shock. Do not try to rely regularly on this, or the audio quality will be damaged.

   If really need a peak limiter, you can try NP1136 Peak Limiter, pre-installed with Reaper.

   | Content of delivery  | Reference value |
   |----------------------|-----------------|
   | Pop music            | +13 LU          |
   | Speech               | +15 LU          |
   | Classical music      | +22 LU          |

4. **Lower Inflection Level (LU)**, default is -6 LU.

   Value in "LU" are added to Target Loudness (e.g. -23 LUFS + -6 LU = -29 LUFS).

   | Content of delivery  | Reference value |
   |----------------------|-----------------|
   | Pop music            | -6 LU           |
   | Speech               | -9 LU           |
   | Classical music      | -13 LU          |
   | Disable this feature | 0 LU            |

   In order to handle transitions from quiet sections and loud sections carefully, the gain adjustment curve is slightly different: When the current loudness is below the lower inflection level, the farther different from target, the slower (instead of faster) gain adjustment will become.

   If you are curious, the gain adjustment formula is:
   ```
                                                 2
   d gain_db   diff_lu * ln(2)         inflect_lu
   --------- = --------------- * ----------------------
       dt       half_life_sec           2             2
                                 diff_lu  + inflect_lu 
   ```
   when `diff_lu < 0` and `inflect_lu < 0`.

5. **Bottom Gate Level (LUFS)**, default -48 LUFS.

   When the measured Momentary Loudness is below this level, either between two songs, or when no one is speaking, the algorithm treats the signal as silence.

   If your material has background noise, set this value higher than the loudness of that noise, or the noise will be amplified to the target loudness. A more ideal solution is to prepend a noise remover plugin.

## Licenses

Most of these plugins are released under MIT licenses. However, I recommend you to check the header of each file to ensure the license.

If you are willing to include my plugins in other projects, I recommend you to contact me before doing that. This will allow me to provide further updates more easily.
