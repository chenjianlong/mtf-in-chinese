# 介绍

This document describes the logical data format used in Microsoft Tape Format (MTF). Media types which can use this data
format include magnetic tapes of many types (QIC, 4mm DAT, 8mm, DLT, etc.), optical disks (Power Drive, CD-ROM),
magnetic disks, etc. MTF is used while writing and reading data to and from removable storage devices during storage
management or data protection operations such as data transfers, copies, backup and restore.
Throughout this document, the term “tape” is used when referring to the removable media. Tapes are shown in most of the
diagrams depicting the physical layout of data. Even some of the data structures include the name “TAPE”. Keep in mind that
disk based media is equally suitable for MTF and tape is used only as an example, and because this specification originated as
a tape format specification before optical disk media became a viable solution for storage management.
This format is compatible with the data format used in the NT Backup applet program that comes bundled with Microsoft ®
Windows NT TM version 3.X and 4.X.
