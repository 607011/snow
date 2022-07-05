# SNOW

This is a copy of the original [SNOW](https://web.archive.org/web/20200701063014/www.darkside.com.au/snow/) sources.

The program is used to conceal messages in ASCII text by appending whitespace to the end of lines. Because spaces and tabs are generally not visible in text viewers, the message is effectively hidden from casual observers. And if the built-in encryption is used, the message cannot be read even if it is detected.

## What's in a name?

SNOW exploits the Steganographic Nature Of Whitespace. Locating trailing whitespace in text is like finding a polar bear in a snowstorm (which, by the way, explains the logo). And it uses the ICE encryption algorithm, so the name is thematically consistent.

## How SNOW works

### The Nature of Steganography

Steganography is the science of concealing messages in other messages. Some historical techniques have involved invisible ink, subtle indentations in paper, and even tattooing messages under the hair of messengers. In this digital age, steganography provides means for hiding messages in digital audio files, in some kinds of images, and even for generating pseudo-English text which encodes the message.

Ideally, the original message is not noticeably degraded by presence of a hidden message. As a result, the most effective techniques tend to make use of data that contains a lot of redundancy, such as raw audio and image files. Steganography works much less effectively, if at all, with efficient compressed formats such as JPEG and MPEG.

Unfortunately, sending large amounts of raw audio and image data can arouse suspicion, and the pseudo-English encoding schemes are not sophisticated enough to fool a human observer.

### Whitespace Steganography

The encoding scheme used by snow relies on the fact that spaces and tabs (known as whitespace), when appearing at the end of lines, are invisible when displayed in pretty well all text viewing programs. This allows messages to be hidden in ASCII text without affecting the text's visual representation. And since trailing spaces and tabs occasionally occur naturally, their existence should not be sufficient to immediately alert an observer who stumbles across them.

The snow program runs in two modes - message concealment, and message extraction. During concealment, the following steps are taken:

    Message -> optional compression -> optional encryption -> concealment in text

Extraction reverses the process:

    Extract data from text -> optional decryption -> optional uncompression -> message

Each of the steps are described in detail below.

### Compression

The compression scheme used by snow is a fairly rudimentary Huffman encoding scheme, where the tables are optimised for English text. This was chosen because the whitespace encoding scheme provides very limited storage space in some situations, and a compression algorithm with low overhead was needed. In other words, short messages had to compress to even shorter data. Depending on the text, you can usually get 25 - 40% compression.

If you want to compress a long message, or one not containing standard text, you would be better off compressing the message externally with a specialized compression program, and bypassing snow's optional compression step. This usually results in a better compression ratio.

### Encryption

The encryption algorithm built in to snow is ICE, a 64-bit block cipher also designed by the author of snow. It runs in 1-bit cipher-feedback (CFB) mode, which although inefficient (requiring a full 64-bit encryption for each bit of output), provides the best possible security when different messages are encrypted with the same password. Although using the same password many times is theoretically a big no-no, in the real world it often can't be avoided.

The lower 7 bits of each character in the password are packed into an array, which is used to set the encryption key. The ICE encryption algorithm can operate at different levels, with higher levels using longer keys and providing more security. The ICE level appropriate for the password length is used.

CFB mode makes use of an initialization vector (IV), which is initially set to the first 64 bits of the key encrypted by itself. Each time a bit is encrypted, the IV is encrypted, and the leftmost bit of the encrypted IV is XORed with the bit. The IV is then shifted left one bit, and the ciphertext bit is added to the right. Decryption reverses this process.

### The Encoding Scheme

To show the beginning of a message, a tab is added immediately after the text on the first line where it will fit. This prevents the insertion of mail and news headers containing trailing spaces from corrupting the message, since a trailing tab must be found before extraction begins.

Data is written 3 bits at a time, coding for 0 to 7 spaces. Any messages not a multiple of 3 bits will be padded by zeroes. During extraction, an extra one or two bits at the end will be ignored (fortunately there are no two-bit Huffman codes to confuse things).

An alternative scheme was considered, where bits were written one at a time as either a space or a tab. Although this scheme adds fewer characters per bit (1 vs 1.5), it requires more columns per bit (4.5 vs 2.67), and column space is the limiting factor.

Tabs are used to separate the blocks of spaces. Thus 3 bits are usually coded in 8 columns of text, and given that the default line length is 80 characters, this allows 30 bits to be stored on empty lines. A tab is not appended to the end of a line unless the last 3 bits coded to zero spaces, in which case it is needed to show some bits are actually there.

If a message will not fit into the available text, empty lines will be appended and used to contain the overflow. A warning message will also be produced, since this affects the look of the original text.

---

Copyright (c) 2013 Matthew Kwan

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

