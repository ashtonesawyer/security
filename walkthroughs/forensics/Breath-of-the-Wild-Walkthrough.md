This is a walkthrough of the NahamCon CTF 2024 challenge **Breath of the Wild**

# Description
Author: @JohnHammond

I got a sweet desktop background for my favorite video game, but now I want more! Problem is, I forget where I downloaded it from... can you help me remember where I got this old one?

Here's a backup of all my wallpapers. For security, I set the drive password to be videogames.

Download the file(s) below. Note, this is a 50MB compressed archive download, and 100MB uncompressed.

# Provided Files
The challenge provides `breath-of-the-wild.7z`. 

```
 $ 7z x breath-of-the-wild.7z
7-Zip 23.01 (x64) : Copyright (c) 1999-2023 Igor Pavlov : 2023-06-20
 64-bit locale=en_US.UTF-8 Threads:8 OPEN_MAX:1024

Scanning the drive for archives:
1 file, 53062388 bytes (51 MiB)

Extracting archive: breath-of-the-wild.7z
--
Path = breath-of-the-wild.7z
Type = 7z
Physical Size = 53062388
Headers Size = 146
Method = LZMA2:24
Solid = -
Blocks = 1

Everything is Ok

Size:       104857600
Compressed: 53062388

 $ file breath-of-the-wild
breath-of-the-wild: , 1st region INVALID

 $ xxd breath-of-the-wild
00000000: 7668 6478 6669 6c65 4d00 6900 6300 7200  vhdxfileM.i.c.r.
00000010: 6f00 7300 6f00 6600 7400 2000 5700 6900  o.s.o.f.t. .W.i.
00000020: 6e00 6400 6f00 7700 7300 2000 3100 3000  n.d.o.w.s. .1.0.
00000030: 2e00 3000 2e00 3200 3200 3600 3300 3100  ..0...2.2.6.3.1.

 $ mv breath-of-the-wild breath-of-the-wild-vhdx
```

# Examining the drive
I mounted the drive and unlocked it using the provided password (`videogames`). 
Looking through the images, there were only two that were clearly Zelda related: `46 - oVMOimE.jpg` and `66 - lWYimrK.jpg`
Because the name of the challenge was Zelda based, I focused on these. 

```
 $ exiftool 46\ -\ oVMOimE.jpg
ExifTool Version Number         : 12.70
File Name                       : 46 - oVMOimE.jpg
Directory                       : .
File Size                       : 129 kB
File Modification Date/Time     : 2024:05:22 01:54:36-07:00
File Access Date/Time           : 2024:05:23 14:56:49-07:00
File Inode Change Date/Time     : 2024:05:22 01:54:36-07:00
File Permissions                : -rwxrwxrwx
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 1400
Image Height                    : 1400
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1400x1400
Megapixels                      : 2.0

 $ exiftool 66\ -\ lWYimrK.jpg
ExifTool Version Number         : 12.70
File Name                       : 66 - lWYimrK.jpg
Directory                       : .
File Size                       : 209 kB
File Modification Date/Time     : 2024:05:22 01:54:35-07:00
File Access Date/Time           : 2024:05:23 15:14:44-07:00
File Inode Change Date/Time     : 2024:05:22 01:54:35-07:00
File Permissions                : -rwxrwxrwx
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Profile CMM Type                : Linotronic
Profile Version                 : 2.1.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 1998:02:09 06:49:00
Profile File Signature          : acsp
Primary Platform                : Microsoft Corporation
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : Hewlett-Packard
Device Model                    : sRGB
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Perceptual
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : Hewlett-Packard
Profile ID                      : 0
Profile Copyright               : Copyright (c) 1998 Hewlett-Packard Company
Profile Description             : sRGB IEC61966-2.1
Media White Point               : 0.95045 1 1.08905
Media Black Point               : 0 0 0
Red Matrix Column               : 0.43607 0.22249 0.01392
Green Matrix Column             : 0.38515 0.71687 0.09708
Blue Matrix Column              : 0.14307 0.06061 0.7141
Device Mfg Desc                 : IEC http://www.iec.ch
Device Model Desc               : IEC 61966-2.1 Default RGB colour space - sRGB
Viewing Cond Desc               : Reference Viewing Condition in IEC61966-2.1
Viewing Cond Illuminant         : 19.6445 20.3718 16.8089
Viewing Cond Surround           : 3.92889 4.07439 3.36179
Viewing Cond Illuminant Type    : D50
Luminance                       : 76.03647 80 87.12462
Measurement Observer            : CIE 1931
Measurement Backing             : 0 0 0
Measurement Geometry            : Unknown
Measurement Flare               : 0.999%
Measurement Illuminant          : D65
Technology                      : Cathode Ray Tube Display
Red Tone Reproduction Curve     : (Binary data 2060 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 2060 bytes, use -b option to extract)
Blue Tone Reproduction Curve    : (Binary data 2060 bytes, use -b option to extract)
Image Width                     : 1920
Image Height                    : 1080
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:4:4 (1 1)
Image Size                      : 1920x1080
Megapixels                      : 2.1
```

This didn't reveal anything, so I loaded the drive into FTKImager to see what else there was. 

# FTKImager
Looking at the photos in FTKImager revealed the `Zone.Identifer` file stream within the images. 

```
# 66
[ZoneTransfer]
ZoneId=3
HostUrl=https://imgur.com/a/gaming-wallpapers-1080p-6EPQG

# 46
[ZoneTransfer]
ZoneId=3
HostUrl=https://www.gamewallpapers.com/wallpapers_slechte_compressie/01wallpapers/&#102;&%23108;&%2397;&%23103;&%23123;&%2356;&%2351;&%23102;&%2350;&%2398;&%2348;&%2397;&%2356;&%2399;&%23101;&%2351;&%2357;&%23102;&%2350;&%23101;&%2353;&%2398;&%2397;&%2349;&%23100;&%2354;&%2399;&%2355;&%2348;&%23101;&%2357;&%2355;&%23102;&%2350;&%2357;&%2349;&%23101;&%23125;
```
# Decoding
I took the extra part of the url from 46 
(`&#102;&%23108;&%2397;&%23103;&%23123;&%2356;&%2351;&%23102;&%2350;&%2398;&%2348;&%2397;&%2356;&%2399;&%23101;&%2351;&%2357;&%23102;&%2350;&%23101;&%2353;&%2398;&%2397;&%2349;&%23100;&%2354;&%2399;&%2355;&%2348;&%23101;&%2357;&%2355;&%23102;&%2350;&%2357;&%2349;&%23101;&%23125;`)
and ran it through a URL decoder to get `&#102;&#108;&#97;&#103;&#123;&#56;&#51;&#102;&#50;&#98;&#48;&#97;&#56;&#99;&#101;&#51;&#57;&#102;&#50;&#101;&#53;&#98;&#97;&#49;&#100;&#54;&#99;&#55;&#48;&#101;&#57;&#55;&#102;&#50;&#57;&#49;&#101;&#125;`
and then UTF-8 decoded the numbers as decimals to get `flag{83f2b0a8ce39f2e5ba1d6c70e97f291e}`
