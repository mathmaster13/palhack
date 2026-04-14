# Speedhack, but with a PAL build flag

I used the most up-to-date version of CelestialAmber's Tetris disassembly. I searched for `.if PAL = 1`,
and matched the `.else` block with existing code in order to add PAL support.

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

The goal of this port is to make a much more faithful implementation of PALhack, by basing it off of the original PAL version of the game.
On NTSC, this will become PAL60, so to fix this, we run the game at 5/6 speed using speedhack, just like the original PALhack does.

This allows for easy replication of all of the weird audio quirks of PAL (e.g. the Tetris sound effect) because it...just is PAL.

Issues:
The audio engine (SFX like the Tetris sound, and music) follows the 60hz console framerate rather than the speedhack framerate.

Changing the audio to follow the speedhack framerate will result in the audio speeding up and slowing down if we go faster or slower. I'm not sure how much this matters.

The one thing that you do NOT want speeding up or slowing down is the music. But if it does, it isn't a huge deal. I may use the tuning, tempo, or both from the NTSC ROM in my version of PALhack, since they might sound better on NTSC consoles. In any case, the sound effects and visuals will have as close to correct timing as I can make them,
even if the music will not be (I care about the music, but it is not trivial to do it well).

Also, the speed select screen ought to be modified for PAL to say 50hz at 1/1, 25hz at 1/2, etc. Of course, PALhack will use NTSC's numbers, but this is speedhack (PAL port). Just plain old speedhack. It should be fairly close to PALhack (ignoring audio and line clear/Tetris animation delay issues) if you run it on NTSC at 5/6 speed.

# NES Tetris Speedhack

Romhack of NES Tetris that implements consistent subframe controller polling, intended to facilitate faster yet fair killscreen gameplay. The main idea is that the pieces fall in a predictable way, so not refreshing the display isn't a huge detriment.

In making this, a large portion of unnecessary/unused code has been stripped from the original ROM. A lightweight game on its own, it only used about 30% of each frame to run actual gameplay on average. Through optimization, this time has been cut down to less than 10%. We expect that the max polling rate can exceed 780hz in most conditions, and can likely go up to 1000hz.

Based on some crude testing, the main game loop (shift, rotate, drop tetrimino) never takes more than 1500 cycles to execute, while the input polling routine is 268 cycles. Waiting for NMI is at least 24000 cycles, so this is already room for 13 game frames, or 780hz polling. In practice, game frames will be much smaller due to a limit on the number of inputs.

Currently, I have support for speeds of up to 360hz. I plan on adding many more at some point, but maybe in a different repo with a better main file.

## Acknowledgements

CelestialAmber for creating the disassembly that this is based off of, who in turned used the work of ejona86.

Kirjava for answering my endless well of questions.
