# 附录 A 操作系统的特性数据

MTF\_DB\_HDR 的 _OS Specific Data_ 字段提用于指存储操作系统特性数据的位置。
MTF\_DB\_HDR 中的 OS ID 和 OS Version 字段用于定义 OS Specific Data 字段存储的操作系特性数据类型。
这些结构是使用原始的数据类型定义在它们各自的平台的。
所有的结构都是经过封装的。

<table>
  <tr>
    <th>操作系统</th><th>OS ID Number</th><th>OS Version Number</th>
  </tr>
  <tr>
    <td>NetWare</td><td>1</td><td>0</td>
  </tr>
  <tr>
    <td>NetWare SMS</td><td>13</td><td>1</td>
  </tr>
  <tr>
    <td></td><td></td><td>2</td>
  </tr>
  <tr>
    <td>Windows NT</td><td>14</td><td>0</td>
  </tr>
  <tr>
    <td>DOS / Windows 3.X</td><td>24</td><td>0</td>
  </tr>
   <tr>
    <td>OS/2</td><td>25</td><td>0</td>
  </tr>
  <tr>
    <td>Windows 95</td><td>26</td><td>0</td>
  </tr>
  <tr>
    <td>Macintosh</td><td>27</td><td>0</td>
  </tr>
  <tr>
    <td>UNIX</td><td>28</td><td>0</td>
  </tr>
  <tr>
    <td>To Be Assigned</td><td>33 - 127</td><td></td>
  </tr>
  <tr>
    <td>Vendor Specific</td><td>128 - 255</td><td></td>
  </tr>
  <caption>**表 29. OS ID 和 OS Version 矩阵**</caption>
</table>

小于 128 的 OS ID 由 MTF Review Committee 来定义。
在 128 - 255 之间的 OS ID 是为销售商预留的。

## NetWare (OS ID Number 1, OS Version Number 0)

略

## NetWare SMS (OS ID Number 13, OS Version Number 1)

略

## NetWare SME (OS ID Number 13, OS Version Number 2)

略

## Windows NT (OS ID Number 14, OS Version Number 0)

下面的结构是为 Windows NT 的 _OS Specific Data_ 定义的。
MTF\_DB\_HDR 的 OS ID 字段必须是 14，而 MTF\_DB\_HDR 的 OS Version 字段必须是 0。
目录和文件的信息从 WIN32\_FIND\_DATA 结构获得。

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>目录属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <caption>**结构 39. MTF\_DIRB OS Specific Data for Windows NT**</caption>
</table>

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>文件属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;4 &nbsp;4h</td><td>短名称偏移</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;6 &nbsp;6h</td><td>短名称大小</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;8 &nbsp;8h</td><td>If non-zero signifies that the file is a link to a previously written file.</td><td>BOOLEAN</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;8 &nbsp;Ah</td><td>预留</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <caption>**Structure 40. MTF_FILE OS Specific Data for Windows NT**</caption>
</table>


## Windows NT (OS ID Number 14, OS Version Number 1)

下面的结构是为 Windows NT 的 _OS Specific Data_ 定义的。
MTF\_DB\_HDR 的 OS ID 字段必须是 14，而 MTF\_DB\_HDR 的 OS Version 字段必须是 1。
目录和文件的信息从 WIN32\_FIND\_DATA 结构获得。

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>File System Flags
(lpFileSystemFlags parameter from GetVolumeInformation API).</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;4 &nbsp;4h</td><td>NT Backup Set Attributes</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <caption>**Structure 41. MTF_VOLB OS Specific Data for Windows NT**</caption>
</table>

**NT Backup Set Attributes {4 bytes}**

NT Backup Set Attributes 字段是一个 4 字节（32 位）的字段，指定了所属的 NT 卷的属性。
Bit 0 如下所定义。
Bits 1 - 23 为将来使用而预留，最高的 8 位（BIT24 - BIT31）是为销售商预留的。

<table>
  <caption>**Table 27. TAPE Attributes**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>NT\_VOLB\_IS\_DR\_CANDIDATE</td><td>If set, then the data following the VOLB should be suitable for an NT system recovery.</td><td>BIT0</td>
  </tr>
  <tr>
    <td></td><td>Reserved (set to zero)</td><td>BIT2 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>Vendor Specific</td><td>BIT24 - BIT31</td>
  </tr>
</table>

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>目录属性（WIN32\_FIND\_DATA 结构的 dwFileAttributes 字段）</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;4 &nbsp;4h</td><td>Short name offset</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;6 &nbsp;6h</td><td>Short name size</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <caption>**Structure 42. MTF_DIRB OS Specific Data for Windows NT**</caption>
</table>











