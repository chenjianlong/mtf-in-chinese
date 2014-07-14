# 第二类 MBC

This section describes the Type 2 Media Based Catalog.
The Type 2 Media Based Catalog includes both a Set Map and
File/Directory Detail (FDD).
Both of these are implemented as fixed length data streams attached to the End Of Set (MTF_ESET) DBLK.

![](images/type_2_mbc_layout.png)

The MAP2 and FDD2 Stream Headers are aligned on the standard MTF stream header alignment of 4 bytes and the SPAD
data stream pads to the next physical block boundary. The first Reserved for MBC field of the MTF_ESET is used to store
the physical block address of the MTF_ESET.

To create Type 2 MBC, the Media Based Catalog Type field of the MTF_TAPE DBLK is set to a value of 2 and the Media
Catalog Version field of the MTF_SSET DBLK is set to a value of 1.

## Set Map

The Set Map is written as a stream with the Stream ID field of the Stream Header is set to ‘ MAP2’. The Stream Header
identifies the stream as being a Type 2 MBC Set Map and is followed by a series of DBLKs. A Type 2 Set Map is comprised
of MTF_TAPE, MTF_SSET, MTF_VOLB, and MTF_ESET DBLKs. All DBLKs are packed. The Offset To First Event field
of the MTF_DB_HDR is modified to point to the next DBLK in the data stream.

![](images/type_2_set_map_example.png)

## File/Directory Detail

The FDD is written as a stream with the Stream ID field of the Stream Header is set to ‘FDD2’. The Stream Header identifies
the stream as being a Type 2 MBC FDD and is followed by a series of DBLKs.
A Type 2 FDD is comprised of MTF_VOLB, MTF_DIRB, MTF_FILE, and MTF_CFIL DBLKs.
All DBLKs are packed.
The Reserved for MBC and Offset To First Event fields of the Common Block Header modified.
The Reserved for MBC is used to indicate the media number that the DBLK was written to and the Offset To First Event is used to point to the next DBLK in the FDD.

![](images/type_2_mbc_fdd.png)

## End of Media Issues

It is possible to encounter EOM while writing MBC information to media.
Refer to the section “End of Media Processing” for detailed information on the way it is handled under different conditions.
When spanning from one media to the next, the set map is written as a data stream attached to the MTF_TAPE DBLK.

![](images/type_2_mbc_spanning.png)
