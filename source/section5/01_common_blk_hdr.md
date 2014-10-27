# 公共的块头

_公共的块头（MTF_DB_HDR）_ 结构可以在每个 DBLK 的开头找到。
MTF\_DB\_HDR 包含了每个 DBLK 需要的概要信息以及描述了 DBLK 的类型的成员，它的属性（连续性，压缩，MBC 呈现技术，等）,操作系统相关信息以及 DBLK 定义的对象的可显示大小（比如：文件大小）。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 00h</td><td>DBLK Type</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;4 04h</td><td>Block Attributes</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;8 08h</td><td>Offset To First Event</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>10 0Ah</td><td>OS ID</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>11 0Bh</td><td>OS Version</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>12 0Ch</td><td>Displayable Size</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>20 14h</td><td>Format Logical Address</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>28 1Ch</td><td>Reserved for MBC</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>30 1Eh</td><td>Reserved</td><td>- - -</td><td>6 字节</td>
  </tr>
  <tr>
    <td>36 24h</td><td>Control Block ID</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>40 28h</td><td>Reserved</td><td>- - -</td><td>4 字节</td>
  </tr>
  <tr>
    <td>44 2Ch</td><td>OS Specific Data</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>48 30h</td><td>String Type</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>49 31h</td><td>Reserved</td><td>- - -</td><td>1 字节</td>
  </tr>
  <tr>
    <td>50 32h</td><td>Header Checksum</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <caption>**结构 4. 公共的块头（MTF_DB_HDR）**</caption>
</table>

**DBLK Type {4 bytes}**

_DBLK Type_ 成员用于指定 DBLK 的类型（MTF\_SSET，MTF\_VOLB，等）。
开发者可以添加他们自己的 DBLK Type 但他们必须先被 MTF Review Committee批准并且实现。
应用程序必须可以处理那些未知的 DBLKs。
如果遇到一个未知的 DBLK Type，应用程序应该使用 MTF\_DB\_HDR部分的信息去跳过和那个DBLK关联的信息。

> **注意：** IDs 的值被选出来了，因此当你查看它的 16 进制的值时，可以容易通过 ASCII 部分来分辨出来，并且通过描述中的缩写可以知道它的由来。

<table>
  <caption>**表2. 块 ID 表**</caption>
  <tr>
    <th>DBLK 名称</th><th>描述</th><th>四字母 ID</th><th>十六进制值</th>
  </tr>
  <tr>
    <td>MTF\_TAPE</td><td>**磁带头** 描述块</td><td>'TAPE'</td><td>0x45504154</td>
  </tr>
  <tr>
    <td>MTF\_SSET</td><td>数据集起始描述块</td><td>'SSET'</td><td>0x54455353</td>
  </tr>
  <tr>
    <td>MTF\_VOLB</td><td>卷描述块</td><td>'VOLB'</td><td>0x424C4F56</td>
  </tr>
  <tr>
    <td>MTF\_DIRB</td><td>目录描述块</td><td>'DIRB'</td><td>0x42524944</td>
  </tr>
  <tr>
    <td>MTF\_FILE</td><td>文件描述块</td><td>'FILE'</td><td>0x454C4946</td>
  </tr>
  <tr>
    <td>MTF\_CFIL</td><td>损坏对象描述块</td><td>'CFIL'</td><td>0x4C494643</td>
  </tr>
  <tr>
  <td>MTF\_ESPB</td><td>填充结束描述块</td><td>'ESPB'</td><td>0x42505345</td>
  </tr>
  <tr>
  <td>MTF\_ESET</td><td>数据集结束描述块</td><td>'ESET'</td><td>0x54455345</td>
  </tr>
  <tr>
  <td>MTF\_EOTM</td><td>磁带标记结束描述块</td><td>'EOTM'</td><td>0x4D544F45</td>
  </tr>
  <td>MTF\_SFMB</td><td>软卷标描述块</td><td>'SFMB'</td><td>0x424D4653</td>
  </tr>
</table>

**Block Attributes {4 bytes}**

 _Block Attributes_ 是一个用于指定 DBLK 的属性的 32 比的成员。
下表显示和描述了这个成员中每一位的定义。
这些属性和磁带格式问题有直接的关联。

低16位（BIT0 - BIT15）是通用语所有类型的 DBLK 而高16位（BIT16 - BIT31）是特定类型的 DBLK 才有用的。
这种方法可以使得同一个比特的含随 DBLK 的不同而有不同含义。
需要注意的是一些在 DBLK 类型一栏值为 _any_ 的比特不一定在所有的DBLK中都有效，只是可以用于不止一种类型。
下表中没有列出的比特保留下来作为将来扩展之用。

<table>
  <caption>**表3. 块属性**</caption>
  <tr>
    <th>名称</th><th>描述</th><th>DBLK 类型</th><th>值</th>
  </tr>
  <tr>
    <td>MTF\_CONTINUATION</td><td>Bit set if DBLK is a continuation from the previous tape.</td><td>any</td><td>BIT0</td>
  </tr>
  <tr>
    <td>MTF\_COMPRESSION</td><td>Bit set if compression may be active.</td><td>any</td><td>BIT2</td>
  </tr>
  <tr>
    <td>MTF\_EOS\_AT\_EOM</td><td>Bit set if the End Of Medium was hit during end of set processing.</td><td>any</td><td>BIT3</td>
  </tr>
  <tr>
    <td>MTF\_SET\_MAP\_EXISTS</td><td>Bit set if an Media Based Catalog Set Map can be found on the
tape.</td><td>MTF\_TAPE</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_ALLOWED</td><td>Bit set if an attempt will be made to put a Media Based Catalog File/Directory Detail section on the tape.</td><td>MTF\_TAPE</td><td>BIT17</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_EXISTS</td><td>Bit set if a Media Based Catalog File/Directory Detail section has been successfully put on the tape for this Data Set.</td><td>MTF\_SSET</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_ENCRYPTION</td><td>Bit set if encryption is active for the data streams within this Data Set.</td><td>MTF\_SSET</td><td>BIT17</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_ABORTED</td><td>Bit set if a Media Based Catalog File/Directory Detail section was aborted for any reason during the write operation.</td><td>MTF\_ESET</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_END\_OF\_FAMILY</td><td>Bit set if the Media Based Catalog Set Map has been aborted. This condition means that additional Data Sets cannot be appended to the tape.</td><td>MTF\_ESET</td><td>BIT17</td>
  </tr>
  <tr>
    <td>MTF\_ABORTED\_SET</td><td>Bit set if the Data Set was aborted while being written. This can happen if a fatal error occurs while writing data, or if the user terminates the data management operation. An MTF\_ESET DBLK containing this flag is put at the end of the Data Set even if it was aborted.</td><td>MTF_ESET</td><td>BIT18</td>
  </tr>
  <tr>
    <td>MTF\_NO\_ESET\_PBA</td><td>Bit set if no Data Set ends on this tape (i.e. continuation tape must follow this tape).</td><td>MTF\_EOTM</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_INVALID\_ESET\_PBA</td><td>Bit set if the Physical Block Address (PBA) of the MTF\_ESET is invalid because the tape drive doesn't support physical block addressing.</td><td>MTF\_EOTM</td><td>BIT17</td>
  </tr>
</table>

> ***注意：*** BIT0 - BIT31 表示这个 32 位的值中的一个单独的比特。
> BIT0 指最低位的比特而 BIT31 指最高位的比特。

**Offset To First Event {2 bytes}**

_Offset To First Event_ 字段用于指定第一个和 DBLK 关联的数据流的偏移（从 DBLK 的开始地址算起）。
如果没有数据流和 DBLK 关联，那么这个成员将包含下一个 DBLK 的偏移。

> 注意：这是为了和 MTF Version 1.0 草案之前的版本向下兼容。
> MTF Version 1.00a 指定了所有的 DBLKs 至少有一个数据流和它关联以及最后一个数据流为 SPAD 数据流。

**OS ID {2 bytes}**

_OS ID_ 指定这个和 DBLK 关联的操作系统的信息。
目前在 附录 A 操作系统的相关数据 中列出了这个成员以及 OS Version 成员预定义的值。
开发者可以增加这个成员中的值，但是新的值必须先“注册”。

**OS Version {2 bytes}**

_OS Version_ 成员定义了在 _OS ID_ 成员定义的特定操作系统的版本。
在这里指定的“版本号”并不是操作系统的发布版本号（例如Windows NT Version 3.5）而是用于在 DBLK 中表现特定 OS 信息的结构的版本。
查看 附录 A 操作系统的相关数据 来获取更多的信息。

**Displayable Size {8 bytes}**

_Displayable Size_ 成员使用 UINT64 结构去指定 DBLK 中可以被应用程序显示的大小。
例如：一个文件被保存在 _文件描述块_ 中。
可以显示的大小或许和文件的物理大小不一样，因此仅用于显示的目的。

**Format Logical Address {8 bytes}**

_Format Logical Address_ 成员同样使用 UINT64 结构来指定这个DBLK的FLA。
查看 3.4 小节获取更多关于 FLA 的信息。

**Reserved for  MBC {2 bytes}**

_Reserved for Media Based Catalog(MBC)_ 成员用于在 第二类 MBC-SLO Set Map 和 FDD 中保存应用程序相关的信息。
如果不是 第二类 MBC-SLO Set Map 和 FDD 这个结构为 0。

**Control Block ID {4 bytes}**

_Control Block ID_ 成员用于错误恢复。
MTF SSET DBLK 中的 Control Block ID 的值为0。
数据集中所有后续的 DBLKs 中的 Conrol Block ID 的值都是后面的比前面的大。
这个成员只有在数据集中 MTF\_SSET 之后以及 MTF\_ESET 之前的 DBLKs 有定义。

**OS Specific Data {4 bytes}**

_OS Specific Data_ 成员使用 MTF\_TAPE ADDRESS 结构来指定 操作系统相关结构 中的位置和大小。
这个结构的值依赖于 OS ID 和 OS Version 成员的值以及 DBLK 的类型。
鉴定操作系统以及版本号的结构在附录 C 中定义。

**String Type {1 byte}**

_String Type_ 是一个用于指定这个 DBLK 中存储的字符串格式的单字节的成员。
下表列出了这个成员可能的值。

<table>
  <caption>表 4. String 类型</caption>
  <tr>
    <th>名称</th><th>描述</th><th>值</th>
  </tr>
  <tr>
    <td>NO_STRINGS</td><td>指明没有字符串和 DBLK 关联</td><td>0</td>
  </tr>
  <tr>
    <td>ANSI_STR</td><td>指明字符串是单字节的 ANSI 字符</td><td>1</td>
  </tr>
  <tr>
    <td>UNICODE_STR</td><td>指明字符串是双字节的 Unicode</td><td>2</td>
  </tr>
</table>

**Header Checksum {2 bytes}**

_Header Checksum_ 成员是一个在 MTF\_DB\_HDR 中的除了它本身外的其他的成员的 16 比特的字的异或（XOR）和。
这个成员用于检测磁带中的数据是否损坏。

**Reserved {1 byte}**

Reserved 成员用于预留出来作为将来使用，因此现在这个成员不应该用来存储信息。
这个成员应该用 0 填充。

## 特定 DBLK 的比特

除了 DBLK 的头 中的 attribute 成员外，还有一个存在于除了 MTF\_EOTM 和 MTF\_ESPB 之外的其他 DBLK 包含在 block specific section 中的 attribute 成员。
这些属性属于数据的内容，而不是磁带这一层。
例如，在MTF\_SSET DBLK 中的 attributes 成员中有一个比特指定了创建数据集所用的数据管理操作的类型（传输，拷贝，普通备份，增量备份，等。）。
在 MTF\_DIRB 和 MTF\_FILE DBLKs 中这些比特用于指定这个目录或者文件是否是只读，隐藏，系统或者自从上次备份以来有没有修改过。

可以在下面关于单独的 DBLK 的描述中找到 _特定 DBLK 的比特_ 的定义以及作用。
目前并不是所有包含这个成员的 DBLKs 都定义了这些比特。
这些成员预先添加作为将来使用。

对于所有 特定 DBLK specific attribute 成员来说有一个共同点。
这个 32 比特成员的高位字节（BIT24 - BIT31）用于销售商特定（vendor specific）的属性。
这些比特不需要注册。
需要注意的是，为了保护数据的可交换性，这些销售商特定的比特 **不应该** 包含任何与恢复数据有关的数据。

## DBLKs 中的字符串

DBLKs中的字符串的长度由 MTF\_TAPE\_ADDRESS 结构中的 Size 成员来决定。
除非特别指出，这些字符串并 **不是** 以 NULL 结尾并且由 DBLK 中的 MTF\_DB\_HDR 中的 _String Type_ 成员指定字符串类型。
