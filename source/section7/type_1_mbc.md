# 第一类 MBC

本节说明第一类 MBC。
可以在第三部分的 格式说明 查看 基于媒介的 Catalog 的概述。
第一类 MBC 包括 FDD 和 Set Map。
都是和 MTF\_ESET DBLK 关联的流。
FDD 包括数据集中 MTF\_VOLB，MTF\_DIRB 和 MTF\_FILE DBLKs 的条目。
第一类 MBC 被设计为允许其他 DBLK 类型的条目，
包括销售特定类型，如果应用程序不认识 FDD 中的某些条目可以简单地跳过。
然而，我们只讨论上面提到的 DBLKs。

为了常见第一类 MBC，MTF\_TAPE DBLK 的 基于媒介的 Catalog 类型字段应该设为 1，而 MTF\_SSET DBLK 的 媒介 Catalog 版本字段应该设为 2。

## 物理层次

Set Map 和 FDD 都是作为与 MTF\_ESET 关联的数据流，以及与物理块边界对齐从而允许应用程序可以直接找到它。
先写 FDD，然后是 Set Map。
它允许 FDD 和 Set Map 都写，或者只写 Set Map 流，但是只写 FDD 是不允许的。
注意：FDD 可以只在某些数据集添加，但是 Set Map 必须用于每个数据集的 Set Map 是必须的，因为它会添加到媒体簇。

第二个 MTF\_ESET DBLK 跟在 Set Map 流的下一个物理边界，并且它的后面会跟在一个卷标来结束整个数据集。
FDD 和 Set Map 和物理块地址包含在第二个 MTF\_ESET DBLK 的 8 字节的字段中。
其他的两个字节的字段提供了 FDD 开始的媒介序号。
请查看 MTF\_ESET DBLK 的说明来获取这些字段的更多信息。

FDD 的最后一个条目是一个特殊的“结束条目”，它允许 FDD 流填充从而保证
Set Map 从物理块边界开始。
Set Map 和第二个 MTF\_ESET 的间隙使用填充流填充，
第二个 MTF\_ESET 和 卷标之间的间隙也是一样。

当媒体簇跨越多个媒介时，每个媒介结尾的 MTF\_EOTM DBLK 都
包含最后一个数据集的第二个 MTF\_ESET 的 PBA。

第二个 MTF\_ESET 的第一个预留给 MBC 的字段用于存储 'TFDD' 流头的 PBA。
第二个 MTF\_ESET 的第二个预留给 MBC 的字段用于存储 'TSMP' 流头的 PBA。

下图演示了 catalogs 的物理位置。

![](images/type_1_mbc_layout.png)

## 文件/目录 细节

文件/目录 细节（FDD）用于说明属于某个数据集的卷，目标和文件树。
FDD 可以用于快速确定单独的卷，目录和文件在媒介的哪个地方。
FDD 可以认为是数据集的 DBLKs 除去跟随的数据流后简短的拷贝。
只有 DBLK 中用于定位和获取数据集中单独项的信息需要放在 FDD 流中。

### FDD 物理层次

FDD 就如 Set Map，在物理块边界开始。
FDD 的流头的 流ID 字段的值为 'TFDD'。
请参考数据流部分来获取更多信息。
MTF 流头标识是 FDD 然后跟随着一系列的 FDD 条目。

一共有 4 种的 FDD 条目：MTF\_FDD\_VOLB，MTF\_FDD\_DIRB，
MTF\_FDD\_FILE 和 MTF\_FDD\_FEND。
FDD 的每个条目都以公共头开始（MTF\_FDD\_HDR），
并且跟着的是若干字段的信息。
这和 DBLKs 在开头使用 MTF\_DB\_HDR 类似。
在数据集中，每个 FDD 都有一个与之关联的 DBLK 结构。
FDD 中 FDD 条目的数量和顺序与数据集中 DBLKs 出现的一一对应，
唯一的例外是跨越的 DBLK 的续集的情况。
FDD 中的最后条目是 FEND 条目。

### FDD 公共头

FDD 公共头是一个 36 字节的放在每个 FDD 条目开头的公共头。
FDD 公共头由条目的长度，类型，属于的媒介和其他通常与 DBLK 重复的字段组成。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>LENGTH</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;2 &nbsp;2h</td><td>TYPE</td><td>UINT8[4]</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;6 &nbsp;6h</td><td>MEDIA\_SEQ\_NUMBER</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;8 &nbsp;8h</td><td>COMMON\_BLOCK\_ATTRIBUTES</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>12 0Ch</td><td>FORMAT\_LOGICAL\_ADDRESS</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>20 14h</td><td>DISPLAYABLE\_SIZE</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>28 1Ch</td><td>LINK</td><td>INT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>32 20h</td><td>OS\_ID</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>33 21h</td><td>OS\_VERSION</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>34 22h</td><td>STRING\_TYPE</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>35 23h</td><td>PAD UINT8</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <caption>**结构 26. 第一类 MBC FDD 公共头（MTF\_FDD\_HDR）**</caption>
</table>

#### LENGTH {2 bytes}

LENGTH 字段指明 FDD 记录流的大小。
这个大小应该等于 MTF\_FDD\_FILE，MTF\_FDD\_DIRB，MTF\_FDD\_VOLB 或者 MTF\_FDD\_FEND 记录流的大小加上任何附加到这个结构的字符串的大小。
跟在特定 FDD 条目的正式字段后的附加字符串包括机器名，卷，目录和文件。

#### TYPE {4 bytes}

TYPE 字段指明这个头属于哪个 MTF\_FDD 记录。
TYPE 的值应该为 'VOLB','DIRB','FILE' 或 'FEND'。
如果块的类型是 'FEND'，块中剩余的字段是未定义的应该设为 0。

#### MEDIA\_SEQ\_NUMBER {2 bytes}

MEDIA\_SEQ\_NUMBER 标识这个 FDD 属于媒体簇中哪个媒介。

#### COMMON\_BLOCK\_ATTRIBUTES {4 bytes}

COMMON\_BLOCK\_ATTRIBUTES 字段应该与关联的数据集中的 MTF\_DB\_HDR
的相应字段相等。
因此，关于续集，压缩，媒介尾，变长流，等。
可以从这些属性中获得。

#### FORMAT LOGICAL ADDRESS {8 bytes}

FORMAT LOGICAL ADDRESS 字段应该与关联的数据集中的 MTF\_DB\_HDR
的相应字段相等。
这个值用于定位和这个 FDD 流条目关联的 DBLK。

#### DISPLAYABLE\_SIZE {8 bytes}

DISPLAYABLE\_SIZE 字段应该与关联的数据集中的 MTF\_DB\_HDR
的相应字段相等。
在这种情况下，只要查看 FDD 条目的这个字段，应用程序就可以快速的决定
和显示数据集中所有文件的大小。

#### LINK {4 bytes}

MTF\_FDD\_HDR 结构的 LINK 字段指示另一个 FDD 条目的偏移。
依赖与 FDD 条目使用的类型 LINK 有不同的含义。

* File entries The stream offset of their parent directory.
* Volume entries The stream offset of the next volume entry, or zero for the last MTF_FDD_VOLB entry.
* Directory entries The stream offset of the next sibling directory (i.e. next directory having same parent), or
zero for the last sibling under any given parent.

#### OS\_ID {1 byte}

OS\_ID 字段是另一个与关联的数据集中的 MTF\_DB\_HDR
的相应字段相等。

#### OS\_VERSION {1 byte}

OS\_VERSION 字段也与关联的数据集中的 MTF\_DB\_HDR
的相应字段相等。

#### STRING\_TYPE {1 byte}

STRING\_ID 字段与关联的数据集中的 MTF\_DB\_HDR
的相应字段相等。

#### PAD {1 byte}

PAD 字段只是为了提高 RISC 处理器使用 MTF 的性能而填充 0 到下一个 4 字节边界。
其他的 FDD 条目从这个边界开始。

### FDD 条目

FDD 中一共有四种的记录类型。
其中的三种用于表示本 FDD 说明的数据集中的卷，目录和文件对象。
这些 FDD 条目的很多字段都和 MTF\_VOLB，MTF\_DIRB 和 MTF\_FILE DBLK 的字段重复。
这四种 FDD 条目为：

<table>
  <caption>**表 26. 第一类 FDD 条目类型**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>MTF\_FDD\_VOLB</td><td>FDD 卷条目</td><td>'VOLB'</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_DIRB</td><td>FDD 目录条目</td><td>'DIRB'</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_FILE</td><td>FDD 文件条目</td><td>'FILE'</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_FEND</td><td>FDD 结束条目</td><td>'FEND'</td>
  </tr>
</table>

#### FDD 卷条目（MTF_FDD_VOLB）

FDD 卷条目和它表示的数据集的 VOLB DBLK 关联。
这个结构的很多字段都是拷贝自 VOLB DBLK 字段。

<table>
  <tr>
    <th>偏移</th><th>字段名称</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>FDD 公共头</td><td>MTF\_FDD\_HDR</td><td>36 字节</td>
  </tr>
  <tr>
    <td>36 24h</td><td>VOLB 属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>40 28h</td><td>设备名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>44 2Ch</td><td>卷名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>48 30h</td><td>机器名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>52 34h</td><td>OS\_SPECIFIC\_DATA</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>57 38h</td><td>媒介写日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <caption>**结构 27. 第一类 MBC FDD 卷条目（MTF\_FDD\_VOLB）**</caption>
</table>

##### FDD 公共头 {36 bytes}

FDD 公共头包含了每个 FDD 条目开头都有的 36 字节的 MTF\_FDD\_HDR 结构。
在前面已经介绍了这个结构。
MTF\_FDD\_HDR 的 TYPE 字段的值为 'VOLB'。

##### VOLB Attributes {4 bytes}

The VOLB Attributes field is the same as that found in the corresponding MTF_VOLB DBLK in the Data Set. Refer to the MTF_VOLB DBLK description for information on the bits in this field.

##### Device Name {4 bytes}

The Device Name field uses the four byte MTF_TAPE_ADDRESS low level structure. This field is the same as the corresponding Device Name field in the MTF_VOLB DBLK of the Data Set, with one exception: The second two bytes used for the Offset field is an offset from the start of this MTF_FDD_VOLB entry to the start of the string containing the Device Name.

##### Volume Name {4 bytes}

The Volume Name field also uses the four byte MTF_TAPE_ADDRESS structure and is the same as the corresponding Volume Name field in the MTF_VOLB DBLK. The Offset field in the MTF_TAPE_ADDRESS low level structure is an offset from the start of this MTF_FDD_VOLB entry to the start of the string containing the Volume Name.

##### Machine Name {4 bytes}

The Machine Name field also uses the four byte MTF_TAPE_ADDRESS structure and is the same as the Machine Name field in the corresponding MTF_VOLB DBLK. The Offset field in the MTF_TAPE_ADDRESS structure is an offset from the start of this MTF_FDD_VOLB entry to the start of the string containing the Machine Name.

##### OS_SPECIFIC_DATA {4 bytes}

The OS Specific Data field uses the four byte MTF_TAPE_ADDRESS structure. Its contents are either zero or the same as the corresponding OS Specific Data field in the MTF_DB_HDR structure within the MTF_VOLB DBLK. The Offset field contains the offset from the start of this MTF_FDD_VOLB entry to the string containing a copy of the OS information used for the corresponding MTF_VOLB DBLK written to media.

##### Media Write Date {5 bytes}

The Media Write Date field uses the five byte MTF_DATE_TIME low level structure and is the same as the Media Write Date field in the corresponding MTF_VOLB DBLK.

#### FDD 目录条目（MTF\_FDD\_DIRB）

The FDD Directory Entry corresponds with the MTF_DIRB DBLK it represents in the Data Set. Many of the data fields found in this structure contain copies of the data found in the MTF_DIRB DBLK fields.

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>FDD 公共头</td><td>MBC\_FDD\_HDR</td><td>36 字节</td>
  </tr>
  <tr>
    <td>36 24h</td><td>最后访问日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>41 29h</td><td>创建日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>46 2Eh</td><td>备份日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>51 33h</td><td>最后访问日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>56 38h</td><td>目录属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>60 3Ch</td><td>目录名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>64 40h</td><td>OS\_SPECIFIC\_DATA</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <caption>**结构 28. 第一个类 MBC FDD 目录条目（MTF\_FDD\_DIRB）**</caption>
</table>

##### FDD Common Header {36 bytes}

The FDD Common Header field contains the 36 byte MTF_FDD_HDR structure that is found at the beginning of every FDD entry Stream. This structure was described on the preceding pages. The TYPE field within the MTF_FDD_HDR structure will be set to ‘DIRB’.

##### Last Modification Date {5 bytes}

The Last Modification Date field uses the five byte MTF_DATE_TIME structure and contains the same data as the Last Modification Date field in the corresponding MTF_DIRB DBLK.

##### Creation Date {5 bytes}

The Creation Date field is another five byte field using the MTF_DATE_TIME low level structure. This field contains the date and time when the directory was first created. The data contained here is the same as that found in the corresponding MTF_DIRB DBLK.

##### Backup Date {5 bytes}

The Backup Date field is another five byte MTF_DATE_TIME field containing the date and time that the directory was last backed up. This is the same value as that found in the corresponding MTF_DIRB DBLK in the Data Set.

##### Last Access Date {5 bytes}

The Last Access Date field also uses the five byte MTF_DATE_TIME low level structure describing the date and time that the directory was last accessed. The data found here is a duplicate of the same field in the MTF_DIRB DBLK.

##### DIRB Attributes {4 bytes}

The DIRB Attributes field is four bytes in length organized as a 32-bit field. DIRB Attributes define characteristics of the directory represented by this MTF_DIRB DBLK. This field is the same as that found in the corresponding MTF_DIRB DBLK.

##### Directory Name {4 bytes}

The Directory Name field is four bytes in length using an MTF_TAPE_ADDRESS low level structure that specifies the location and size of the name associated with this directory. The Offset field used in this structure specifies the offset from the beginning of this MTF_FDD_DIRB entry to the beginning of the string containing the directory name.

##### OS_SPECIFIC_DATA {4 bytes}

The OS_SPECIFIC_DATA field uses the four byte MTF_TAPE_ADDRESS structure; its contents are either zero or a copy of the data found in the corresponding field of the MTF_DB_HDR structure within the MTF_DIRB DBLK. The Offset field contains the offset from the start of this MTF_FDD_DIRB entry to the string containing a copy of the OS information used for the corresponding MTF_DIRB DBLK written to media.

#### FDD 文件条目（MTF\_FDD\_FILE）

The FDD File Entry corresponds with the MTF_FILE DBLK it represents in the Data Set. Many of the data fields found in this structure contain copies of the data found in the MTF_FILE DBLK fields.

<table>
  <tr>
    <th>偏移</th><th>字段名</td><td>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>FDD 公共头</td><td>MTF\_FDD\_HDR</td><td>36 字节</td>
  </tr>
  <tr>
    <td>36 24h</td><td>最后修改时间</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>41 29h</td><td>创建日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>46 2Eh</td><td>备份日期</td><td>MTF\_TAPE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>51 33h</td><td>最后访问日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>55 37h</td><td>文件属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>60 3Ch</td><td>文件名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>64 40h</td><td>OS\_SPECIFIC\_DATA</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <caption>**结构 29. 第一类 MBC FDD 文件条目**</caption>
</table>

##### FDD Common Header {36 bytes}

The FDD Common Header field contains the 36 byte MTF_FDD_HDR structure that is found at the beginning of every FDD entry Stream. This structure was described on the preceding pages. The TYPE field within the MTF_FDD_HDR structure will be set to ‘FILE’.

##### Last Modification Date {5 bytes}

The Last Modification Date field uses the five byte MTF_DATE_TIME structure and contains the same data as the Last Modification Date field in the corresponding MTF_FILE DBLK.

##### Creation Date {5 bytes}

The Creation Date field also uses the MTF_DATE_TIME low level structure containing the date and time the directory was first created. The data contained here is a duplicate of the same field in the corresponding MTF_FILE DBLK.

##### Backup Date {5 bytes}

The Backup Date field is an MTF_DATE_TIME low level structure containing the date and time that the directory was last backed up. This is the same as the data found in the corresponding MTF_FILE DBLK.

##### Last Access Date {5 bytes}

The Last Access Date field is also a duplicate of the same field in the corresponding MTF_FILE DBLK. FILE Attributes {4 bytes}
The FILE Attributes field is a 32-bit field containing the same data as found in the FILE Attributes field of the corresponding MTF_FILE DBLK in the Data Set.

##### File Name {4 bytes}

The File Name field uses the four byte MTF_TAPE_ADDRESS low level structure that specifies the location and size of the name associated with this file. The Offset field in this low level structure specifies the offset from the beginning of this MTF_FDD_FILE entry to the string containing the file name.

##### OS_SPECIFIC_DATA {4 bytes}

The OS_SPECIFIC_DATA field uses the four byte MTF_TAPE_ADDRESS structure. Its contents are either zero or a copy of the data found in the corresponding field of the MTF_DB_HDR structure within the MTF_FILE DBLK. The Offset field contains the offset from the start of this MTF_FDD_FILE entry to the string containing a copy of the OS information used for the corresponding MTF_FILE DBLK written to media.

#### FDD 结束条目（MTF_FDD_FEND）

The End of FDD Entry does not corresponds with a DBLK in the Data Set. It is used to indicate the end of the FDD.

<table>
  <tr>
    <th>偏移</th><th>字段名</td><td>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>FDD 公共头</td><td>MTF\_FDD\_HDR</td><td>36 字节</td>
  </tr>
  <caption>**结构 30. 第一类 MBC FDD 结束条目**</caption>
</table>


##### FDD Common Header {36 bytes}

The FDD Common Header field contains the 36 byte MTF_FDD_HDR structure that is found at the beginning of every FDD entry Stream. This structure was described on the preceding pages. The TYPE field within the MTF_FDD_HDR structure will be set to ‘FEND’.

The FEND entry is unique in that it does not correspond to a DBLK within the Data Set and does not have a record specific section. It is used to indicate the end of the FDD entries. The space following the FEND entry is zero padded up to the next Physical Block boundary. The Length field in the FDD Common Header specifies the offset to the next PBA. Typically, the Set Map will begin at the start of the next Physical Block boundary.

## Set Map

The Set Map is used to list all of the Data Sets of a media or Media Family. Each successive Set Map written to a media contains information about the Data Sets previously written to media. The Set Map, like the FDD, is written as a stream and can follow the FDD or be located on an alternate partition.

The Set Map is written as a stream with the Stream ID field of the Stream Header is set to **‘TSMP ’**. Please refer to the Data Stream section for information on the Stream Header. The Stream Header identifies the stream as being the Set Map stream and is followed by three distinct parts.

1. Set Map Header
2. Set Map Entries
3. Volume Entries

### Set Map Physical Layout

The Set Map begins with the Set Map Header which specifies the number of Set Map Entries which follow.
Each Set Map Entry is in turn followed by a number of Volume Entries as specified in the Set Map Entry.
There is a one-to-one correspondence between the number of Set Map Entries and Volume Entries in the Set Map, and the number of
MTF_SSET and MTF_VOLB DBLKs in the Media Family.
This includes continuation MTF_SSET and MTF_VOLB DBLKs written during EOM processing conditions.
See Appendix J for details on End Of Media and spanning information. The order in which the Set Map Entries and Volume Entries appear in the Set Map is identical to the order in which their corresponding MTF_SSET and MTF_VOLB DBLKs are written to media.

### Set Map Header (MTF_SM_HDR)

The Set Map Header is an eight byte header that contains information about the Media Family to which the Set Map belongs, the number of Set Map Entries to follow, and a pad to the next stream alignment boundary.

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>Media Family ID</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;4 &nbsp;4h</td><td>Number Of Set Map Entries</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;6 &nbsp;6h</td><td>Pad</td><td>UINT8[2]</td><td>2 字节</td>
  </tr>
  <caption>**结构 31. 第一类 MBC Set Map 头（MTF\_SM\_HDR）**</caption>
</table>

#### Media Family ID {4 bytes}

The Media Family ID field corresponds to the same field specified in the MTF_TAPE DBLK for this media.
Please refer to the MTF_TAPE DBLK description for more information on this field.

#### Number Of Set Map Entries {2 bytes}

The Number Of Set Map Entries field is two bytes in length and tells how many Set Map Entry structures are to follow this Set Map Header.
One Set Map Entry is written for every Data Set written to the Media Family.

#### Pad {2 bytes}

The Pad field exists to maintain 32-bit alignment. The field should be initialized to zero.

### Set Map Entry (MTF_SM_ENTRY)

The Set Map Entry corresponds with the MTF_SSET DBLK it represents in the Data Set.
Many of the data fields found in this structure contain copies of the data found in the MTF_TAPE, MTF_SSET and MTF_ESET DBLK fields.

> **TODO** Structure 32. Type 1 MBC Set Map Entry (MTF_SM_ENTRY)

#### Length {2 bytes}

The Length is the size of the MTF_SM_ENTRY plus the size of any appended strings.

#### Media Sequence Number {2 bytes}

The Media Sequence Number field corresponds to the Media Sequence Number field in the MTF_TAPE DBLK to which this Data Set belongs.

#### Common Block Attributes {4 bytes}

The Common Block Attributes field has the same organization as the field of the same name in the MTF_DB_HDR structure.

#### SSET Attributes {4 bytes}

The SSET Attributes field is the same as the SSET Attributes field in the MTF_SSET DBLK.

#### SSET PBA {8 bytes}

The SSET PBA field corresponds to the Physical Block Address (PBA) field in the MTF\_SSET DBLK and identifies the PBA of the MTF_SSET DBLK.

#### FDD PBA {8 bytes}

The FDD PBA field contains the same information as the File/Directory Detail PBA field of the MTF_ESET DBLK.
This number specifies the Physical Block Address of the FDD associated with this Data Set.

#### FDD Media Sequence Number {2 bytes}

The FDD Media Sequence Number is a duplicate of the field of the same name in the MTF_ESET DBLK.

#### Data Set Number {2 bytes}

The Data Set Number field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Number Of Directories {4 bytes}

The Number Of Directories field indicates the number of directories written as part of this Data Set.

#### Number Of Files {4 bytes}

The Number Of Files field indicates the number of files written as part of this Data Set.

#### Number Of Corrupt Files {4 bytes}

The Number Of Corrupt Files field indicates the number of corrupt files written as part of this Data Set.

#### Data Set Displayable Size {8 bytes}

The Data Set Displayable Size field indicates the cumulative size of the Data Set.
This should be the sum of the displayable size of every file in the Data Set.

#### Number Of Volumes {2 bytes}

The Number Of Volumes field should correspond with the number of MTF_ VOLB DBLKs in the Data Set and with the number of Volume Entries that will follow this Set Map Entry (MTF_SM_ENTRY) structure.

#### Password Encryption Algorithm {2 bytes}

The Password Encryption Algorithm field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Data Set Name {4 bytes}

The Data Set Name field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Data Set Password {4 bytes}

The Data Set Password field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Data Set Description {4 bytes}

The Data Set Description field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### User Name {4 bytes}

The User Name field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Media Write Date {5 bytes}

The Media Write Date field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Time Zone {1 bytes}

The Time Zone field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### OS_ID (1 byte}

The OS_ID field is a duplicate of the field of the same name in the MTF_DB_HDR structure of the MTF_SSET DBLK.

#### OS_VERSION (1 byte}

The OS_VERSION field is a duplicate of the field of the same name in the MTF_DB_HDR structure of the MTF_SSET DBLK.

#### STRING_TYPE (1 byte}

The STRING_TYPE field specifies the format of strings stored in the Set Map.
Refer to the definition of this field in the description of the MTF_DB_HDR structure.

#### MTF Minor Version {1 byte}

The MTF Minor Version field is a duplicate of the field of the same name in the MTF_SSET DBLK.

#### Media Catalog Version {1 byte}

The Media Catalog Version field is a duplicate of the field of the same name in the MTF_SSET DBLK.

> Note: All strings associated with a Set Map Entry are appended immediately after, and pointed to by the
MTF_TAPE_ADDRESS entries. The Offset field within the MTF_TAPE_ADDRESS structure specifies
offsets from the start of this MTF_SM_ENTRY structure to the string being referred to.

### Volume Entry

The Volume Entry structure in the Set Map is identical to the MTF_FDD_VOLB entry in the File/Directory Detail. Please
refer to the description of the MTF_FDD_VOLB earlier in this section.

### End of Media Issues

It is possible to encounter EOM while writing MBC information to media. Refer to Appendix J for detailed information on
End Of Media processing and the way it is handled under different conditions.
