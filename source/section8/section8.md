# 结束媒体处理

This section is devoted to End Of Media (EOM) processing.
The following diagram is an example of a 1.0 format Data Set
with marks at all unique points at which End Of Media (EOM) early warning may be detected.
This is followed by diagrams and brief explanations of what is written on the original and continuation media in each case.

![](images/eom_01.png)

Before beginning the detailed explanation of how each case is handled, there are certain general concepts which need to be
explained.

What will be referred to as "normal EOM processing" consists of writing a filemark, an End Of Tape Marker (MTF_EOTM)
block and another filemark, getting a continuation tape and writing a tape header with the continuation bit set in its attribute
field followed by a filemark. Any exceptions to this process will be noted in the detail for that case.

While the only block shown to have associated data is the MTF_FILE, methods for handling data associated with any block
should be handled in a similar fashion. It is important to note that MTF_SSET, MTF_VOLB and MTF_DIRB blocks can be
repeated on the continuation tape, with the continuation bit set in its attribute field, even when they are not the current block
being processed. This is because they contain information which is necessary for reading and restoring data from the
continuation tape without the need for the tape where the data management operation was started. However, if they have any
associated data, it is not repeated, and the data size should be zero.

The split across EOM always occurs on Format Logical Block boundaries. For purposes of EOM processing, an image block
and data is treated the same as a MTF_FILE block and data.

> NOTE: In all the diagrams that follow, '*' indicates that the continuation bit (MTF_CONTINUATION) is set in the Block
Attributes filed of the MTF_DB_HDR in the DBLK.

1. EOM after MTF_SSET - Process EOM normally, write the MTF_SSET again with the continuation bit set, and begin
writing again from the point you left off.
![](images/eom_a.png)
2. EOM after MTF_VOLB - Process EOM normally, write the MTF_SSET and the current MTF_VOLB again with the
continuation bit set in each, and begin writing again from the point you left off.
![](images/eom_b.png)
3. EOM after MTF_DIRB - Process EOM normally, write the MTF_SSET, and the current MTF_VOLB and
MTF_DIRB again with the continuation bit set in all three, and begin writing again from the point you left off.
![](images/eom_c.png)
4. EOM after MTF_FILE - Process EOM normally, and write the MTF_SSET, and the current MTF_VOLB and
MTF_DIRB again with the continuation bit set in all three. Write the MTF_FILE again with the continuation bit set,
then write the data associated with that MTF_FILE, and continue on. Note that the data is written immediately
following the MTF_FILE block, and since EOM always occurs at a Format Logical Block boundary, the chances of
EOM occurring at this point are very low.
![](images/eom_d.png)
5. EOM in mid MTF_FILE data - Process the EOM in the same manner as example d. Since the data was split at a
Format Logical Block boundary, and the pad at the end of the data is already calculated to align the next block on a
Format Logical Block boundary, the remaining data is written beginning at the next Format Logical Block boundary,
rather than flush against the end of the continuation MTF_FILE block.
![](images/eom_e.png)
6. EOM at end of MTF_FILE data - Unlike the MTF_SSET, MTF_VOLB and MTF_DIRB, the information in the
MTF_FILE block is not needed on the continuation tape if the MTF_FILE data is written completely. Therefore,
writing a continuation MTF_FILE DBLK is optional, and the continuation processing is done in the same manner as
example c. i.e. The continuation DBLKs are written, and then the write operation continues with the block that was
due to be written when EOM occurred.
7. EOM at End Of Set - In this case, all set information is on tape, but the MTF_EOTM is still written as if the set
continues on the next tape. Note that if the first filemark has been written, we do not write another. Only the
continuation MTF_SSET needs to be written before closing out the set normally, but it must also have the bit set to
indicate that the data for this set is contained fully on the previous tape.
![](images/eom_g_and_h.png)
8. EOM between sets - In these two cases, the MTF_ESET has already been written, and the set is completed, but we do
not want another set started on this tape. Therefore, we write an MTF_EOTM where the next MTF_SSET would be
expected, followed by a filemark. A continuation tape is written identical to the one written for cases g & h. This is
done to guarantee the existence of a unique continuation tape for beginning the next set. Note that while a
MTF_TAPE DBLK alone is sufficient to mark a unique continuation tape, information such as the number of the last
Data Set is necessary to append to the Media Family without requesting the previous tape.
![](images/eom_i_and_j.png)
The following diagram is an example of an MTF Version 1.00a format Data Set with Media Based Catalogs (MBC) showing
marks at all unique points at which EOM early warning may be detected. This is followed by diagrams and brief explanations
of what is written on the original and continuation tapes is each case. Note that the MBC lies between the two filemarks at the
end of the set, and all EOM cases outside of MBC are handled in the same manner as with tapes which do not have MBC as
specified above. Cases a, g, and h are shown below to relate this diagram to the non-MBC diagram above. Cases k - q are
specific to MBC and detailed below.
![](images/eom_i_and_j_02.png)
There are some further general concepts which need to be explained before detailing the MBC cases.
In all cases, the MTF_EOTM will contain the physical block address of the second MTF_ESET of the last set which finished
completely (including MBC) on the tape. Attribute bits will be defined to indicate whether the address field is invalid (not
supported by drive or no MBC on tape), and to indicate if no ending MTF_ESET exists on the tape (i.e. one set spans the entire
tape).
What will be referred to as "normal EOM processing" for MBC cases consists of writing a filemark, an End Of Tape Marker
(MTF_EOTM) block and another filemark, getting a continuation tape and writing a tape header with the continuation bit set in
its attribute field followed by a filemark, then writing the MTF_SSET with continuation bit set, another filemark, and finally
the starting MTF_ESET with continuation bit set. Any exceptions to this process will be noted in the detail for that case.
File/Directory Data will be referred to as FDD, and the Set Map as SM.
9. EOM after first MTF_ESET - Process EOM normally, then begin writing the FDD.
10. EOM in mid FDD - Process EOM normally, then continue writing the FDD.
11. EOM after FDD - Process EOM normally, then begin writing the SM.
12. EOM in mid Set Map - Process EOM normally. The Set Map is then rewritten from the start. The Set Map is never split between tapes!
13. EOM after Set Map - This case is handled the same way as in case n. The goal here is to make the Set Map available
on the last tape in the Media Family. This makes the MBC processing a lot cleaner, and eliminates requiring the user
to switch back and forth between tapes when searching for the last Set Map in a Media Family.
14. EOM between sets - As in cases i and j, the MTF_ESET is already written before we hit EOM, and the set is
complete. So we write an MTF_EOTM where the next MTF_SSET would be expected, followed by a filemark.
However, we still want a copy of the Set Map on the last tape in the Media Family. Therefore, we write the
continuation tape in the same manner as case o.
