# DC26 Badge Emulator
---
This code has been tested on an Arduino Uno and Nano (we used the latter to create
the OG badge emulator). 

The badges use a stripped down version of wireengineer's 
[Badge Bus protocol](https://github.com/Wireb/badge_bus/wiki).

### Header structure

Section  | Size in bytes
:---: | :---:
Preamble | 2
Packet Start | 2
Status | 1
Command | 1
Payload Len | 1
Payload | Varies
CRC16 | 2

The communication begins with two `0x55 (0b1010101)` bytes -- a preamble
which is used for baud rate detection (if supported). The following two
bytes `0x4242` indicate the start of the packet.

The DC26 badge protocol only uses two statuses:

   * `0x80 (0b10000000)` -- Checksum is valid; Request. This packet will carry
     no payload.

   * `0xC0 (0b11000000)` -- Checksum is valid; Response. This packet carries a
     payload identifying the originator.

The command field is always `0xAF` and has no apparent purpose. This field is 
left pretty flexible by the specification, see the wiki page for more info.

The header ends with a byte long length of the payload. This badge only has two
types of payload, so this field will be `0x00` for the requests and `0x05` for
the responses.

### Payload Structure

Only the responses carry a payload. It's always 5 bytes long. The first three
bytes are 0x000100. It is unclear to me at the moment what these bytes really do, 
we weren't able to capture any traffic where these bytes differed.

The next two bytes represent the badge type and the badge state.

Badge Type | Value
:---: | :---:
Human | 1
Contest | 2
Goon | 3
Artist | 4
Press | 5
Vendor | 6
Speaker | 7
CFP | 8

Only two badge states were captured over the wire:
  * `0x00` -- neutral/red.
  * `0x01` -- happy/green.

The packet ends with two bytes -- CRC16 checksum of the packet (starting from
the `0x42` byte).

P.S. The arduino schematic and instructions are coming tomorrow -- promise!
