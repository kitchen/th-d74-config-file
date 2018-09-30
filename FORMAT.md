This file will contain my canonical reference for the file format.

If you're reading this line right now, I don't consider this to be a complete description of the format. I'll change this line later, should that change.

# Format

At a very high level, there are several sections in the file:

* header
* current state of radio (frequency, tone settings, etc)
* channel configuration
* logs (call sign logs, transmit history, etc)


# Notes

The current state of radio data seems to directly correspond with the data used for programming memories, which should make things a lot easier since I can change 2 knobs at once when trying to figure this out.

The file appears to be a fixed size, though I will try to identify any section separators or anything of the sort rather than relying on fixed positions to try to be more future-resistant.
Within each section I suspect things will be very rigidly structured. I already know that at least the memory names are fixed 16 bytes, because they align very nicely in hexdump output.
