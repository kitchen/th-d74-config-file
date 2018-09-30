This file will contain my canonical reference for the file format.

If you're reading this line right now, I don't consider this to be a complete description of the format. I'll change this line later, should that change.

# Placement

These files can be found in `KENWOOD/TH-D74/SETTINGS/DATA` directory with the extension `.d74`. The filename is a date and timestamp in `%m%d%Y_%H%M%S` format. The timestamp appears to use the local timezone offset. I would love to know how it handles leap seconds, in any way at all. ;-)

One can also place a properly formatted file in this location and the radio will offer it up to be loaded as configuration. There does not appear to be any sort of checksum involved to verify file integrity, and I don't yet know how the radio handles improperly formatted files (improper being anything from complete garbage to impossible values in correct locations to whatever)

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

Memory names appear to be stored as fixed length 16 byte fields. Given that the max name for a memory is 16 bytes, this leads me to believe it's using 8 bit ASCII for its character set. I'm slightly surprised, given that this is a Japanese radio, that it doesn't support non-ASCII character encodings, like utf-8 or the like.
