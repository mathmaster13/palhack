# Speedhack, but with a PAL build flag

I used the most up-to-date version of CelestialAmber's Tetris disassembly. I searched for `.if PAL = 1`,
and matched the `.else` block with existing code in order to add PAL support.

This allows the PAL people to have nice things. But my real goal is to use this PAL port to make a much more faithful implementation of PALhack, by using PAL directly as much as possible.

The same idea can hopefully be extended to make a very accurate NTSChack for PAL consoles. 

## Speedhack-PAL: Issues

### Can't find all the PAL code

But! I could not find all of the PAL code. Here is what I am missing:

```asm
@recording:
        jsr     pollController
.if PAL = 1
        lda     heldButtons_player1
        and     #$DF
        sta     heldButtons_player1
.endif
        lda     gameMode
        cmp     #$05
```
I can't find the equivalent code block in speedhack. Maybe it was removed.

`MENU_CURSOR_MASK` is a constant used five times in the Tetris disassembly. I found the first two, but can't find the last three.
Note that this constant is `$03` on NTSC.

The three occurences of `MENU_CURSOR_MASK` are in `@showSelection`, `@skipShowingSelectionLevel`, and `@renderFrame`. 
Only the last of these three labels exists in speedhack, and while I have a hunch that the one `$03` in that part of the code is indeed `MENU_CURSOR_MASK`, I cannot be entirely sure.

## PALhack implementation: Issues

The audio engine (SFX like the Tetris sound, and music) follows the 60hz console framerate rather than the speedhack framerate. I'm pretty sure the timing of the line clear animation is also following 60hz and thus too fast. At the risk of making 6x speed not possible, I would love it if the entire game ran at 50Hz, including the music engine (and if the music engine refuses, at least the sound effects code).

Changing the audio to follow the speedhack framerate will result in the audio speeding up and slowing down if we go faster or slower. This is bad for speedhack, which is intended to be used for practicing Tetris at different possible level speeds, and so speedhack-PAL also doesn't do this. However, the purpose of my PALhack is solely to run the PAL game "as natively as possible" on NTSC. Thus I don't care about any speed other than 5/6, and so this is fine.

# NES Tetris Speedhack

Romhack of NES Tetris that implements consistent subframe controller polling, intended to facilitate faster yet fair killscreen gameplay. The main idea is that the pieces fall in a predictable way, so not refreshing the display isn't a huge detriment.

In making this, a large portion of unnecessary/unused code has been stripped from the original ROM. A lightweight game on its own, it only used about 30% of each frame to run actual gameplay on average. Through optimization, this time has been cut down to less than 10%. We expect that the max polling rate can exceed 780hz in most conditions, and can likely go up to 1000hz.

Based on some crude testing, the main game loop (shift, rotate, drop tetrimino) never takes more than 1500 cycles to execute, while the input polling routine is 268 cycles. Waiting for NMI is at least 24000 cycles, so this is already room for 13 game frames, or 780hz polling. In practice, game frames will be much smaller due to a limit on the number of inputs.

Currently, I have support for speeds of up to 360hz. I plan on adding many more at some point, but maybe in a different repo with a better main file.

## Acknowledgements

CelestialAmber for creating the disassembly that this is based off of, who in turned used the work of ejona86.

Kirjava for answering my endless well of questions.
