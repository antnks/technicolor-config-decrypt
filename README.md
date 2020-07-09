# Technicolor config decrypt

This repo contains info how to decrypt `config.bin`, edit and encrypt back

**YOU MUST HAVE HAD ROOT AT LEAST ONCE BEFORE YOU CAN DECRYPT**

This method is useful if you need:

1. See your saved settings
2. Recover VoIP password to use with different device
3. Recover root access after ISP pushed an update and kicked you out

## File format

HEADER + IV + DATA + SIGNATURE

## Method

* HEADER: ascii '\n' separated, '\n\n' at the end
* IV: 16 random bytes, generated for each export
* DATA: AES 128 CBC encrypted using hardware key
* SIGNATURE: 20 bytes, HMAC-SHA1 of `HEADER + IV + DATA`

## Key

The key is located in `/proc/rip/0108`. First 32 bytes used for AES encryption, entire 64 bytes are used for HMAC.
Also, the key may be exctracted from `mtd5` using this method: [https://github.com/pedro-n-rocha/secr](https://github.com/pedro-n-rocha/secr)

## Encryption

## Decryption

Decryption using command line: extract aes data and decrypt using hex values

```
dd if=config.bin bs=1 skip=246 count=16 > iv.bin
dd if=config.bin bs=1 skip=262 count=FILE-SIZE-MINUS-20> config.aes
openssl enc -aes-256-cbc -nosalt -d -in config.aes \
-K '72ab46c7e90c66feb4af261437bd738204c640dc7f0b9959ea18d582b6876783' -iv 'c6b1c0aac15c13e5ab18a8aabafc14e0'
```

## Header example
```
PREAMBLE=THENC
BACKUPVERSION=1.00
BOARDMNEMONIC=GANT-1
PRODUCTNAME=MediaAccess TG
SERIALNUMBER=CPxxxxxx
MAC=xx:xx:xx:xx:xx:xx
BUILDVERSION=17.1.7992-0001012-20180515151413-4f071fd23e90c774b6827546188a38d6c91cff38
CIPHERKEY=GW
SIGNATUREKEY=GW

```

## Export example
```
00000000: 5052 4541 4d42 4c45 3d54 4845 4e43 0a42  PREAMBLE=THENC.B
00000010: 4143 4b55 5056 4552 5349 4f4e 3d31 2e30  ACKUPVERSION=1.0
00000020: 300a 424f 4152 444d 4e45 4d4f 4e49 433d  0.BOARDMNEMONIC=
00000030: 4741 4e54 2d31 0a50 524f 4455 4354 4e41  GANT-1.PRODUCTNA
00000040: 4d45 3d4d 6564 6961 4163 6365 7373 2054  ME=MediaAccess T
00000050: 470a 5345 5249 414c 4e55 4d42 4552 3d43  G.SERIALNUMBER=C
00000060: xxxx xxxx xxxx xxxx xxxx 0a4d 4143 3dxx  xxxxxxxxxx.MAC=x
00000070: xxxx xxxx xxxx xxxx xxxx xxxx xxxx xxxx  xxxxxxxxxxxxxxxx
00000080: 0a42 5549 4c44 5645 5253 494f 4e3d 3137  .BUILDVERSION=17
00000090: 2e31 2e37 3939 322d 3030 3031 3031 322d  .1.7992-0001012-
000000a0: 3230 3138 3035 3135 3135 3134 3133 2d34  20180515151413-4
000000b0: 6630 3731 6664 3233 6539 3063 3737 3462  f071fd23e90c774b
000000c0: 3638 3237 3534 3631 3838 6133 3864 3663  6827546188a38d6c
000000d0: 3931 6366 6633 380a 4349 5048 4552 4b45  91cff38.CIPHERKE
000000e0: 593d 4757 0a53 4947 4e41 5455 5245 4b45  Y=GW.SIGNATUREKE
000000f0: 593d 4757 0a0a c6b1 c0aa c15c 13e5 ab18  Y=GW.......\....
00000100: a8aa bafc 14e0 b694 f4bf ff6c 5f3a 1db5  ...........l_:..
00000110: 3d35 9222 3433 01c0 f6a6 e90e 6599 e52e  =5."43......e...
00000120: 79fe 23f8 da03 aaad cbeb c7ac ba07 5505  y.#...........U.
00000130: 7702 12c3 5b7c 66ec 7c8f 1e30 1250 9d1d  w...[|f.|..0.P..
..
..
```
Here, IV is `c6b1c0aac15c13e5ab18a8aabafc14e0`, next goes encrypted data, 20 bytes at the end will be the signature
