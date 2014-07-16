# 附录 D 实现问题

## Field Size and Alignment

Due to the nature of many of the 32-bit processors, all 32-bit elements are aligned on 4 byte boundaries, and all 16-bit elements
are aligned on even byte boundaries.
Without this requirement, the actual size of the structure would vary depending on the type of processor and the compiler.
All media structures are packed to 1 byte boundaries to ensure compatibility.

## Software Compression Algorithm

The Data Encryption Algorithm in the MTF_SSET DBLK has been changed to Software Compression Algorithm.
MTF Version 1.00a limits a backup set to a single software compression algorithm.
The addition of the Software Compression Algorithm allows software to determine if Data Sets have compatible software compression algorithms.
Data Encryption has not been defined and this change should have no impact on existing products.

## Block Alignment Pad

In Version 1.0 of MTF, a Block Alignment Pad is used between the end of a DBLK and the start of the next DBLK.
The Block Alignment Pad can be a few bytes or hundreds of bytes depending on the length necessary to fill to the next Format Logical
Block.
A block alignment pad has no header, it is simply NULL data (binary zero).
Block Alignment Pads are only used following DBLKs which don’t have a data stream section immediately following.
MTF Version 1.00a uses a stream pad (SPAD) in place of the Block Alignment Pad.

## Offset To First Event

The Offset To First Event field in the Common Block Header normally points to the header of the first stream associated with
that DBLK.
Early drafts of the MTF 1.0 specification did not require the SPAD Data Stream.
The following method is suggested for determining whether the Offset To First Event field points to is a Stream Header or DBLK:

1. Check to see if it is a known DBLK or Stream type, then use the checksum to verify the integrity of the data.
2. If step 1 fails use the checksum to determine if it is an unknown Stream Header.
3. If step 2 fails use the checksum to determine if it is an unknown DBLK.

## Device Specific Physical Block Addressing

There are two types of positioning: absolute (physical) and logical. Not all drives support both types of positioning: the drive's
feature bits indicate the type(s) supported. Also, on some drives, the media (tape) can affect whether or not positioning is
supported; i.e., a drive's feature bits can change depending on whether or not there is media in the drive and/or the type of
media in the drive.

Some Windows NT tape (class) drivers do a software simulation of logical tape positioning in the driver; i.e., they implement
pseudo-logical tape positioning. This provides a means to write/read MTF on media and drives that intrinsically support only
absolute tape positioning.

On all drives, positioning support is intended to provide a means to do a "get position" while writing to tape in order to be able
to later do a "set position" to that same position and then begin reading the tape at/from that same position. This technique will
always "work" and it can be done on any media and drive that supports either type of positioning; i.e., it is always possible to
"set position" to either an absolute position or a logical position obtained by means of a "get position". It is not certain that a
"set position" to a "synthetic" position (i.e., any position not directly obtained by means of a "get position") will "work".
Arbitrary, "random access" positioning capability on tape is not intended; in fact, it is not supported by/on many drives.

However, it is usually possible to "set position" to a position relative to (i.e., at an offset from) a logical position (pseudo or
real) obtained by means of a "get position". On all media and drives where logical positioning is supported, this relative
(offset) type of "set positioning" can at least be done:

* in a forward direction (positive offset),
* from the logical position obtained by means of a "get position",
* to/at any relative, calculated position (sum of offset and logical position obtained by means of a "get position"),
* within and throughout the/any region of data that is/was consecutively and contiguously written immediately following
the logical position obtained by means of a "get position" (i.e., a data "zone" produced by consecutive and contiguous
"writes" and nothing else -- no other intermediate tape mark writes, "get positions", or whatever).

The foregoing is true both on media and drives that intrinsically support true (SCSI-2) logical positioning and on media and
drives where (driver implemented) pseudo-logical positioning is supported.

In addition to the foregoing, on media and drives that intrinsically support true logical positioning it is possible to do both
forward and reverse relative "set positions" (negative or positive offset) and relative "set positions" that cross tape mark
divisions (filemarks and/or setmarks).

In the set of tape media and tape drives handled by the current set of Windows NT tape drivers (4mmdat.sys, archqic.sys,
exabyte1.sys, exabyte2.sys, tandqic.sys, wangqic.sys, and qic117.sys), the media and drives that intrinsically support true
(SCSI-2) logical positioning are all DAT media and drives (4mmdat.sys), the 1.35 gigabyte (9135) and 2.1 gigabyte (9210)
QIC media and Archive Anaconda (model 2750 and model 2800) QIC drives (archqic.sys), and 5+ gigabyte 8mm media and
SCSI-2 8mm drives (exabyte2.sys -- Exabyte 8500 series and compatible). In all other cases where logical positioning support
is indicated in the drive features bits, it is pseudo-logical tape positioning support; i.e., it is done by software simulation of
logical tape positioning in the driver.

Thus, although it is sometimes possible to "set position" to a position relative to (i.e., at an offset from) an absolute position
obtained by means of a "get position", it is unnecessary to do so: pseudo-logical tape positioning is implemented in the
Windows NT tape drivers where it is possible to do so. Doing so is very media/drive unique and hence requires very
media/drive specific knowledge. An understanding of how this is accomplished can be acquired by studying the technical
standards that govern the physical format of recorded information on the specific tape media, the tape drive technical manual(s)
and the Windows NT tape driver source code. Translation between absolute position and pseudo-logical position and vice versa
is accomplished by module "physlogi" in the Windows NT tape drivers. The source code for this module (physlogi.c) is
included in the Windows NT DDK.
