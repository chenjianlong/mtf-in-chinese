# 目录描述块（MTF\_DIRB）

**目录描述块**（MTF\_DIRB）包含了恢复目录所需要的信息。
它由 MTF\_DB\_HDR，MTF\_DIRB DBLK 特定的字段空间，
操作系统特定的数据区域以及用于恢复文件名的字符串存储区域组成。
操作系统特定数据以及字符串存储区域之前的区域包含了在各个平台都合法的文件相关字段信息。
操作系统特定的数据区和字符串存储区域可以放在继 MTF\_DIRB DBLK 特定区域的任何地方，并且可以颠倒顺序。
MTF\_DIRB DBLK(s) 必须在目录的任何文件描述块（MTF\_FILE DBLKs）前面。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h</td><td>目录属性</td><td>UINT32</td><td>4 字节</td>
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
    <td>80 50h</td><td>目录名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <caption>**结构 8. 目录描述块（MTF\_DIRB）**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 的 DBLK 类型字段将被设置为 'DIRB'。
MTF\_DB\_HDR 包含了对所有的 DBLKs 来说都相同的字段以及一个
指向操作系特定数据区域的偏移。
这个区域包含了一个特定操作系的目录相关信息。

## 目录属性 {4 bytes}

目录属性是一个由 4 个字节组成的 32 比特的字段。
目录属性定义该 MTF\_DIRB DBLK 代表的目录的特性。
比特 0 - 7 为预留比特。
比特 8 - 11 和 16 - 18 如下表所示。
直到比特 23 的其他比特预留。
这些比特描了大多数系统共用的目录属性。
有些操作系不使用其中的一些属性，在这种情况下这些比特被忽略掉。

<table>
  <caption>**表 13. 目录属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>DIRB\_READ\_ONLY\_BIT</td><td>这个比特表示目录是只读的。</td><td>BIT8</td>
  </tr>
  <tr>
    <td>DIRB\_HIDDEN\_BIT</td><td>这个比特表示目录是隐藏的。</td><td>BIT9</td>
  </tr>
  <tr>
    <td>DIRB\_SYSTEM\_BIT</td><td>这个比特表示目录是系统目录。</td><td>BIT10</td>
  </tr>
  <tr>
    <td>DIRB\_MODIFIED\_BIT</td><td>这个比特表示目录已被修改。这个比特也被称为“归档”标识。</td><td>BIT11</td>
  </tr>
  <tr>
    <td>DIRB\_EMPTY\_BIT</td><td>这个比特表示目录不包含文件和子目录。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>DIRB\_PATH\_IN\_STREAM\_BIT</td><td>这个比特表示目录的路径保持在和这个 DBLK 关联的流中。</td><td>BIT17</td>
  </tr>
  <tr>
    <td>DIRE_CORRUPT_BIT</td><td>这个比特表示和此目录关联的数据不能读。</td><td>BIT18</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT0 - BIT7
BIT12 - BIT15
BIT19 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>销售商特定</td><td>BIT24 - BIT31</td>
  </tr>
</table>

## 最后修改日期 {5 bytes}

最后修改日期 是一个包含目录最后修改日期和时间的 5 字节的字段。
当属于这个目录的文件和子目录添加或者删除后，这个目录就被认为修改了。
这个字段使用 MTF\_DATE\_TIME 结构。

## 创建日期 {5 bytes}

创建日期 是另一个使用 MTF\_DATE\_TIME 结构的 5 字节字段。
这个字段包含了目录创建时的日期和时间。

## 备份日期 {5 bytes}

备份日期 是另一个使用 MTF\_DATE\_TIME 结构的 5 字节字段，
包含了这个目录最后一次备份的时间和日期。
当这个目录的所有内容备份后，这个目录被认为是 “已备份” 的。

## 最后访问日期 {5 bytes}

最后访问日期 同样是一个使用 MTF\_DATE\_TIME 结构的 5 字节字段，它包含了这个目录最后一个访问的日期和时间。
当目录的内容修改后，则认为这个目录“被访问”了。

> 注意：备份程序需要尽量不影响这个字段。

## 目录 ID {4 bytes}

目录 ID 是一个包含了目录 ID 的 4 字节的字段。
这个 ID 以“1”开始于数据集的第一个目录并且每当处理另一个目录时加 1。
这个字段用于错误处理和恢复。

## 目录名 {4 bytes}

目录名字段是一个使用 MTF\_TAPE\_ADDRESS 结构来指定本目录的名称的
位置和大小的字段。
这个目录名不包括服务，卷或盘符。
也就是说，"root" 指示 '\' 不能是第一个字符。
它表示所有目录从 root 开始。
系统的路径分隔符必须替换为 NULL 字符（'\0'）。
MTF\_TAPE\_ADDRESS 中的 Size 字段必须用来确定字符串的长度。

"root" 目录需要保持为值为 NULL 的一个字符长度的字符串。
为了 "root" 规格的一致性，所有的目录名以 NULL 结尾。
需要注意的是，这是路径分隔符，不是字符串结尾符。

"root" 目录入口（比如："C:\"）保持在 DBLK 的时候为：

    '\0'

目录 "C:\apps\fred\bloggs\" 入口保存在 DBLK 为：

    apps'\0'fred'\0'bloggs'\0'

因为路径的大小可能比 DBLK 所允许的最大值还大，更大的目录名保存在
'PNAM' 数据流中。
这个流必须是 MTF\_DIRB 后的第一个数据流。
当恢复这个目录到另一个系统的时候，创建的目录名称可以会因为目标系统不支持某些字符或者名字太长而被修改。
