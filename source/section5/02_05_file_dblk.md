# 文件描述块（MTF\_FILE）

**文件描述块**（MTF\_FILE）包含了用于恢复一个文件所需的信息。
它由 MTF\_DB\_HDR，紧跟着的 MTF\_FILE DBLK 指定字段，操作系统特定数据区域和用于保持文件名的字符串存储区域组成。
在操作系统特定数据区域和符串存储区域前的区域保存了各个平台公共的字段信息。
操作系统特定数据区域和符串存储区域可以放在 MTF_FILE DBLK 特定区域后的任意地方。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字</td>
  </tr>
  <tr>
    <td>52 34h</td><td>文件属性</td><td>UINT32</td><td>4 字节<td>
  </tr>
  <tr>
    <td>56 38h</td><td>最后修改日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>61 3Dh</td><td>创建日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>66 42h</td><td>备份日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>71 47h</td><td>最后访问日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>76 4Ch</td><td>目录 ID</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>80 50h</td><td>文件 ID</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>84 54h</td><td>文件名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <caption>**结构 9. 文件描述符（MTF\_FILE）**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 的 DBLK 类型字段将被设置为 'FILE'。
MTF\_DB\_HDR 包含了对所有的 DBLKs 来说都相同的字段以及一个
指向操作系特定数据区域的偏移。
这个区域包含了一个特定操作系的目录相关信息。

## 文件属性 {4 bytes}

文件属性是一个由 4 个字节组成的 32 比特的字段。
这个字段包含了这个 DBLK 所代表的文件的属性。

下表显示了当前已经定义以及预留的比特。
这些比特说明了对大多数系统来说公共的一些文件属性。
有些系统不会使用其中的部分属性，在这种情况下这些比特只是简单的忽略。

<table>
  <caption>**表 14. 文件属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>FILE\_READ\_ONLY\_BIT</td><td>这个比特表示文件是只读的。</td><td>BIT8</td>
  </tr>
  <tr>
    <td>FILE\_HIDDEN\_BIT</td><td>这个比特表示文件是对用户隐藏的。</td><td>BIT9</td>
  </tr>
  <tr>
    <td>FILE\_SYSTEM\_BIT</td><td>这个比特表示这个文件是系统文件。</td><td>BIT10</td>
  </tr>
  <tr>
    <td>FILE\_MODIFIED\_BIT</td><td>这个比特表示文件以及被修改了。同时也被称为“归档”标识。</td><td>BIT11</td>
  </tr>
  <tr>
    <td>FILE\_IN\_USE\_BIT</td><td>这个比特表示当备份这个文件时这个文件正在被使用。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>FILE\_NAME\_IN\_STREAM_BIT</td><td>这个比特表示文件名保存在和这个 DBLK 关联的流中。</td><td>BIT17</td>
  </tr>
  <tr>
    <td>FILE\_CORRUPT\_BIT</td><td>这个比特表示和这个文件关联的数据不能读取。</td><td>BIT18</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT0 – BIT7
BIT12 - BIT15
BIT19 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>销售商特定</td><td>BIT24 - BIT31</td>
  </tr>
</table>

## 最后修改日期 {5 bytes}

最后修改日期是一个包含了文件最后修改日期和时间的 5 字节的字段。
这个字段使用 MTF\_DATE\_TIME 结构。

## 创建日期 {5 bytes}

创建日期 是另一个使用 MTF\_DATE\_TIME 结构的 5 字节字段。
这个字段包含了文件创建时的日期和时间。

## 备份日期 {5 bytes}

备份日期 是另一个使用 MTF\_DATE\_TIME 结构的 5 字节字段，
包含了这个文件最后一次备份的时间和日期。
当这个文件作为数据保护操作的一部分的时候，则认为这个文件是“已备份”的。

## 最后访问日期 {5 bytes}

最后访问日期 同样是一个使用 MTF\_DATE\_TIME 结构的 5 字节字段，它包含了这个文件最后一个访问的日期和时间。
注意：备份程序应该尽可能不改变这个字段。


## 目录 ID {4 bytes}

目录 ID 是一个包含了这个文件所属的目录的 ID 的 4 字节的字段。
这个 ID 应该与最后处理的 MTF\_DIRB DBLK 的目录 ID 一致。
这个字段用于错误处理和恢复。
查看对 MTF\_DIRB DBLK 的描述来获取目录 ID 如何生成的说明。

## 文件 ID {4 bytes}

文件 ID 是一个用于标识文件的 4 字节的字段。
数据集中的第一个文件的文件 ID 从 1（0001h）开始，然后每处理一个文件的时候增加 1。
这个字段用于错误处理和恢复。

## 文件名 {4 bytes}

这个字段使用一个 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定文件名的位置和大小。
MTF\_TAPE\_ADDRESS 中的偏移字段指向包含文件名字符串保存的 MTF_FILE DBLK 的末尾。
这个文件名可以包含任意长度的任意字符。
查看 MTF\_TAPE\_ADDRESS 结构来查看详细信息。

因为文件名的大小有可能导致 DBLK 的大小超过其最大允许值，长文件名需要存储在一个单独的 'FNAM' 数据流中。这个流必须是 MTF\_FILE DBLK 的第一个数据流。

当恢复文件到其他系统时，有可能因为文件名中有目标操作系统不合法的字符或者超过其允许的最长文件名而修改文件名。
文件名是不保存其路径信息的。
