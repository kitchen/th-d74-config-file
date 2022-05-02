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

## Channels

Channel names are stored as 16 ASCII characters, occupying bytes 0x10100 to 0x13f7f (inclusive).
The name of channel CH, therefore, can be accessed at (0x10100 + 0x10 * CH).
If the name is shorter than 16 characters, it is padded with 0x00's.

Other channel data begins at 0x4100 (for channel 0). 
Each channel takes up 0x28 (40) bytes.
Every sixth channel, 16 0xff bytes are inserted in order to align the data to 0x100's.
The data for channel CH, therefore, can be accessed at (0x4100 + 40 * CH + (CH / 6) * 16), where '/' is integer division.

Of the forty channel data bytes:
 * bytes 0-3 are the RX frequency in Hz, stored as a little-endian integer
 * bytes 4-7 are the shift size (or TX frequency in case of split channels) encoded in the same manner
 * byte 8 is divided into two 4-bit fields, both appearing to be indexed step sizes (table: [5, 6.25, 8.33, 9, 10, 12.5, 15, 20, 25, 30, 50, 100])
 * byte 9 is the mode index (table: [FM, DV, AM, LSB, USB, CW, NFM, DR])
 * byte 10 is divided into two 4-bit fields (in order of bit significance):
   * indexed squelch type (table: [Off, Cross, DCS, ?, CTCSS, ?, ?, ?, Tone, ?, ?, ?])
   * indexed shift direction (table: [simplex, +, -, --, split])
 * byte 11 is the encoded CTCSS tone (as table index, table given below)
 * byte 12 is the decoded CTCSS tone (as before)
 * byte 13 is the DCS code (as table index, table given below)
 * byte 14 ???, likely "digital > squelch type"
 * bytes 15-22 are "URCALL", as ASCII characters (0x00 padded)
 * bytes 23-30 are "RPT1", as ASCII characters (0x00 padded)
 * bytes 31-39 are "RPT2", as ASCII characters (0x00 padded)
 * byte 40 ???, likely "digital > digital code"

CTCSS tone table: [67.0, 69.3, 71.9, 74.4, 77.0, 79.7, 82.5, 85.4, 88.5, 91.5, 94.8, 97.4, 100.0, 103.5, 107.2, 110.9, 114.8, 118.8, 123.0, 127.3, 131.8, 136.5, 141.3, 146.2, 151.4, 156.7, 159.8, 162.2, 165.5, 167.9, 171.3, 173.8, 177.3, 179.9, 183.5, 186.2, 189.9, 192.8, 196.6, 199.5, 203.5, 206.5, 210.7, 218.1, 225.7, 229.1, 233.6, 241.8, 250.3, 254.1]

DCS code table: [23, 25, 26, 31, 32, 36, 43, 47, 51, 53, 54, 65, 71, 72, 73, 74, 114, 115, 116, 122, 125, 131, 132, 134, 143, 145, 152, 155, 156, 162, 165, 172, 174, 205, 212, 223, 225, 226, 243, 244, 245, 246, 251, 252, 255, 261, 263, 265, 266, 271, 274, 306, 311, 315, 325, 331, 332, 343, 346, 351, 356, 364, 365, 371, 411, 412, 413, 423, 431, 432, 445, 446, 452, 454, 455, 462, 464, 465, 466, 503, 506, 516, 523, 526, 532, 546, 565, 606, 612, 624, 627, 631, 632, 654, 662, 664, 703, 712, 723, 731, 732, 734, 743, 754]


# Notes

The current state of radio data seems to directly correspond with the data used for programming memories, which should make things a lot easier since I can change 2 knobs at once when trying to figure this out.

The file appears to be a fixed size, though I will try to identify any section separators or anything of the sort rather than relying on fixed positions to try to be more future-resistant.
Within each section I suspect things will be very rigidly structured. I already know that at least the memory names are fixed 16 bytes, because they align very nicely in hexdump output.

Memory names appear to be stored as fixed length 16 byte fields. Given that the max name for a memory is 16 bytes, this leads me to believe it's using 8 bit ASCII for its character set. I'm slightly surprised, given that this is a Japanese radio, that it doesn't support non-ASCII character encodings, like utf-8 or the like.
