# PDBDOC3A
Back in the days of the Palm Pilot, a company called Aportis produced a compressed file format that allowed you to store large text files, such as books, on these memory-restricted devices. They used a form of compression known as Run Length Encoding (RLE) which is slow to compress (so you'll do that on your Desktop), but fast to uncompress, so it's suitable for the low-power device. The "Aportis Doc" format was sometimes also called TealDoc - Aportis went out of business, and the lore of how to decompress wasn't exactly lost, but became harder to find.

This was mainly the late 1990s and early 2000s - the Psion Series 3 was on the way out, and the Series 5 was in the ascendant, so I wrote some apps to allow you to read Palm Pilot (PDB & PRC) files on the 5mx. Somewhere I must have found the Aportis Doc decompression format, and implemented it in the Psion OPL programming language. One app was called PdbRead, which was mainly for Palm databases, but could also handle some bits of AportisDoc files. The other was [HebRead](https://web.archive.org/web/20030207095620/http://palmaris.com/hebread.htm), which was really intended to allow you to read Hebrew AportisDoc books on the Psion. (I don't speak much Hebrew - I just thought this would be an interesting project!). HebRead handles AportisDoc files reasonably well in Latin character languages too.

![HebRead Text viewer](https://github.com/user-attachments/assets/3667cb43-d41e-4f89-851c-baee140b51ca)

However the poor wee Series 3a/c/mx range (as far as I know) never had an AportisDoc reader, which is sad, because their memory constraints make a compressed text reader even more pressing. And here we are in 2025!

Long story short, I'm working on **PdbDoc3a** - an app to allow you to open these old files (which are still kicking around the internet - lots of Project Gutenberg books have about 40% compression). My hope is that it will encourage more people to get their Series 3 machines out and used in daily life.

I hope to provide more detail on this project on this page as it moves on - early days yet! (October 2025).

# Aportis Doc format (AportisDoc)
The files are essentially databases in Palm's proprietary format. Here's how it breaks down:
* HEADER of 78 bytes, containing the database name, the "creator" and "reader" names, and the number of RECORDS in the database
* INDEX - a series of 8-byte blocks containing the byte locations of each RECORD in the file, as well as the number for each RECORD - 0,1,2,3 etc.
* RECORDS - this is the main content, and consists of a solid block of data, usually with no dividing indicators - those are provided by the INDEX section

So, to read a RECORD you need to verify the file time in the HEADER, check the RECORD number is less than the total, then read the INDEX for that particular RECORD, as well as for the *next* RECORD (to get the size), then read the appropriate number of bytes from the file into a buffer.

So far, so good. But when we get a RECORD, what do we do with it? This requires an explanation of the decompression rules.
1. We have loaded the data from this RECORD into an INPUT buffer. I call this PDBIN.
2. We'll create an OUTPUT buffer called PDBOUT.
3. The AportisDoc format breaks the text data into 4096-byte chunks for compression, so both buffers need to be large enough to accommodate this.
4. Decompression proceeds sequentially, reading from PDBIN and outputting into PDBOUT.

Right. Let's start decompressing!
1. Read a byte from PDBIN (we'll call it 'pb') - this tells us what to do:
  1. pb = ZERO - IGNORE: (which I don't like doing, so I typically pass a '^' character to PDBOUT)
  2. pb = 1-8 - BLOCK_PASS: read the *next* pb number of bytes as a block directly into PDBOUT
  3. pb = 9-127 - PASS_THRU: just pass pb directly into PDBOUT (the easy one)
  4. pb = 128-191 - COPY_SEQUENCE: we need to read the NEXT byte in PDBIN (let's call it 'nb'), combine it with pb to get a LOOKBACK address, which tells the program to look back in PDBOUT and then copy a specified number of characters into the current position. The LOOKBACK is calculated from the 6 least significant bits of pb and the five most significant bits of nb, so that's 11 bits, which is a maximum of 2048 bytes to look back. The **three least significant bits of nb (0-7) plus 3** tell you how may characters to copy (ie 3-10).
  5. pb = 192-255 - pass a space to PDBOUT, followed by the alphanumeric character corresponding to pb - 128

In OPL on my Psion 3mx this takes around one second to decompress 4096 bytes into text (of course then we need to display it). I'm hoping to optimise this code to improve that speed, particularly since I think the older 3a and 3c devices will struggle a bit. The decompression works on-the-fly as required, in order to keep memory requirements down.

## Bookmarks and tags
The AportisDoc/TealDoc format allows for bookmarks and tags - I haven't looked at implementing these yet, but that may be for another day.

## Additional notes
This project is only for *decompressing* these files. There are a number of tools on-line for compressing, but that is such a pain in the ear. Part of the purpose of **permacomputing** and **retrocomputing** is to do as much of this stuff off-grid as possible. I'll see if I can find a suitable download candidate...
