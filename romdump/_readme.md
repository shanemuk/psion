# Psion Series 3 ROM files
This folder contains tools and ROMs from the Series 3 range.
* S3_177F.ROM - Series 3, v1.77F/ENG - English language only
* S3_180F.ROM - Series 3, v1.80F/ENG - select English, French, German, Spanish
* S3_191F.ROM - Series 3, v1.91F/ENG - as above plus Italian

Extracting ROMs from the Series 3 is a bit of an involved process. You need to create a dump of the ROM as memory. At present you need to do this in two batches (I think this was because of old smaller format SSD cards - larger capacity cards may be able to do it in one go, but I don't have the utilities for that).

## YOU NEED:
* Psion Series 3
* 3-Link cable
* 1MB capacity SSD card (or 512KB, but you'll need to do it in two goes)
* Computer that has a serial port.
* Optional: Psion Series 3c or above, for faster file transfer speeds, plus a small 256KB SSD.

Here is what I did:
* I connected my Psion 3c to my PC using DOSBOX running the Series 3c emulator. This works fairly well, in that it supports bidirectional file transfer at 57600 bps, and you can run the Files app on both sides, so transferring files is easier.
* I copied the S38BDUMP.IMG and S3CFDUMP.IMG files into a folder on the 256KB SSD called "\ETC\"
* I used this to copy these IMG files onto the S3. (Note: you could just as easily copy them across using your 3Link cable and PsiWin if you have it - it'll be slower as the S3 can only go to 9600 bps)
* I used the S3 to format a 1MB SSD - this is the disk I'm going to dump the files to.
* I installed the RunIMG app on the S3 - this is a "standard" app, so from the system screen, press Psion-J, select RunIMG, and off you go.
* In the system screen, I selected RunIMG, then hit Psion-* ("Directory") and navigated to the \ETC\ Directory, and selected S38BDUMP.IMG - this program exports the first half of the S3 ROM.
* It'll ask you what Disk to export to. Choose the one with your 1MB SSD in it.
* You can rename the file, but it's probably easier to do this afterwards. It'll be something like S38B.ROM - that's grand.
* It'll chug away for a while and write the S38B.ROM file to the SSD.
* When it is complete, repeat the process for S3CFDUMP.IMG.
* My 1MB SSD now has two .ROM files on it. I transferred the SSD to the Series 3c (for speed of transfer), and using the Files app on the S3c, copied these files onto my PC. I chose a folder called C:\PSION - you can choose what you want, but if you're using DOSBOX, remember that this folder will be in your dosfiles folder.
* I've got a Linux system, so I opened up a terminal window in the folder containing the files and joined them together with the "cat" command:
```
cat S38B.ROM S3CF.ROM > S3_ROM.ROM
```
* and there you have it! A dumped ROM!

I did run across something a bit weird. If you insert your 1MB SSD in a Series 3c (or possibly even 3a or above), something happens that means that the S3 won't be able to read it! It may say it is "Unrecognised". To fix this, toggle the SSD to PROTECT mode using the little switch on the side, insert into your S3 and verify that you can now read it as "Protected". Then remove the SSD, toggle it back to "WRITE", and it should be OK until the next time it ends up in a S3c. I'm not sure of the reason for this, and it may just be my experience with this particular SSD, so if you don't have this problem, that's good!
