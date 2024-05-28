This is a walkthrough of the May 2024 BYU CTF forensics challenge **Advanced Steak**

# Intro

The Mad Cow APT has been responsible for a significant amount of attacks against organizations in the technology, finance, and
energy domains. They first started their attacks around 3 years ago, though they have been dormant in the last 2 years. Recently,
the APT started to resurface.

Field agents were able to conduct a raid against a known Mad Cow hideout. In the process, the agents pulled a flash drive from
one of the Mad Cow computers. Unfortunately, it looks like one of the threat actors was able to begin wiping the flash drive with
random data before the analyst pulled the drive. The field agent pulled the drive before the wipe was complete, but parts of
the drive have been corrupted. Our team has already created a forensic image of the drive: Mad Cow.001.

Analysts who studied the Mad Cow APT two years ago are highly confident that the APT is utilizing the same tactics as they have
in previous years. One such tactic is communicating through a custom file type. Fortunately, one of analysts built a decryptor
for this custom file type. Using this, we can turn any .cow files into a standard PNG. The decryptor does not offer any useful
information on finding these files, however.

Your job is to find any .cow files on the corrupted flash drive (Mad Cow.001) and pull them from the image file. You can use
the two provided .cow files as reference to help understand what the files look like. Then, run any discovered .cow file(s)
through the decryptor to determine if there is any useful data. The data will be in the format byuctf{flag}.

Note: this is not a reverse engineering challenge. The cow_decryptor.py is intentionally missing important data. You do not
need to reverse engineer the script in order to find the file(s). This challenge can be solved without examining the cow_decryptor.py
file.

Author: FiredPeppers

# Provided Files

The player is given `steak.zip` which contains a subfolder `src` that has the disk image `Mad Cow.001`, two examples of `.cow` files
(`Cow 1.cow` and `Cow 2.cow`), and the decryptor script `cow_decryptor.py`.

# `.cow` Format

```
 $ xxd Cow\ 1.cow | less
00000000: 1337 beef f2f5 e5f5 ffff fff2 b6b7 bbad  .7.............
...
000a2cf0: 5b3f 0447 8056 ffff ffff b6ba b1bb 4d6f  [?.G.V........Mo
000a2d00: 6f6f                                     oo

 $ xxd Cow\ 2.cow | less
00000000: 1337 beef f2f5 e5f5 ffff fff2 b6b7 bbad  .7..............
...
000e7a60: ffb6 bab1 bb4d 6f6f 6f                   .....Mooo
```

Exmining both of the provided `.cow` files shows that they begin with the magic
bytes `1337 beef` and end with the ASCII bytes for 'Mooo'.

# Finding Files

```
 $ xxd Mad\ Cow.001 | grep '1337 beef'
00e00000: 1337 beef f2f5 e5f5 ffff fff2 b6b7 bbad  .7..............

 $ xxd -s 0x00e00000 Mad\ Cow.001 | grep Mooo
00e8f8b0: c16f ffff ffff b6ba b1bb 4d6f 6f6f 0000  .o........Mooo..
```

# File Carving

```
 $ dd if=Mad\ Cow.001 of=img.cow skip=14680064 bs=1 count=587966
587966+0 records in
587966+0 records out
587966 bytes (588 kB, 574 KiB) copied, 193.323 s, 3.0 kB/s
```

- `dd` Arguments
  - if: in-file (disk image)
  - of: out-file (file to create)
  - skip: number of bytes to skip from the start of the image (0xe00000 in decimal)
  - bs: block-size (1 byte)
  - count: number of blocks to include (0xe8f8be - 0xe00000 in decimal)

```
 $ xxd img.cow | less
00000000: 1337 beef f2f5 e5f5 ffff fff2 b6b7 bbad  .7..............
...
0008f8b0: c16f ffff ffff b6ba b1bb 4d6f 6f6f       .o........Mooo
```

# Conversion

```
 $ python3 cow_decryptor.py -i img.cow -o img

 $ file img
img: PNG image data, 915 x 526, 8-bit/color RGBA, non-interlaced
```

![img with flag](./img/cow-flag.png)
