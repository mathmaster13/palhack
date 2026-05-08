# Speedhack, but with a PAL build flag

It's what it says it is.

Finished:
- Every piece of code from the original ROM that is region-specific has been added to this version of speedhack.

Current to-dos:
- Any issues with the size of the ROM? PAL does have extra code that NTSC does not, and we didn't include any extra padding data.
- Adjust scanline count code for PAL (scheduling controller polls, etc).
- Check if any other speedhack-specific code, or even just re-implementations of original ROM code, needs to be adjusted for PAL
- Figure out why allegro was turned off
- Figure out how to tune the audio engine without it bugging out

This allows the PAL people to have nice things. But my real goal is to use this PAL version as the basis for a different implementation of PALhack. I'm not sure what will come out of this, or if my idea is feasible, but let's see!

I also am fixing speedhack bugs if I find them, hopefully putting them upstream.

# NES Tetris Speedhack

Romhack of NES Tetris that implements consistent subframe controller polling, intended to facilitate faster yet fair killscreen gameplay. The main idea is that the pieces fall in a predictable way, so not refreshing the display isn't a huge detriment.

In making this, a large portion of unnecessary/unused code has been stripped from the original ROM. A lightweight game on its own, it only used about 30% of each frame to run actual gameplay on average. Through optimization, this time has been cut down to less than 10%. We expect that the max polling rate can exceed 780hz in most conditions, and can likely go up to 1000hz.

Based on some crude testing, the main game loop (shift, rotate, drop tetrimino) never takes more than 1500 cycles to execute, while the input polling routine is 268 cycles. Waiting for NMI is at least 24000 cycles, so this is already room for 13 game frames, or 780hz polling. In practice, game frames will be much smaller due to a limit on the number of inputs.

Currently, I have support for speeds of up to 360hz. I plan on adding many more at some point, but maybe in a different repo with a better main file.

## Acknowledgements

CelestialAmber for creating the disassembly that this is based off of, who in turned used the work of ejona86.

Kirjava for answering my endless well of questions.
