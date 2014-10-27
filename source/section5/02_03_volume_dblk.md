# 卷描述块（MTF\_VOLB）

**卷描述块**（MTF\_VOLB）包含了描述数据集中一个卷的信息。
MTF\_VOLB DBLK 结构包含了描述读写文件的物理位置的物理卷信息。
它由 MTF\_DB\_HDR，MTF\_VOLB DBLK 特定的字段空间，
用于存储设备，卷和机器名的字符串存储空间组成。
字符串存储空间可以由定义的字段定位。

当写一个用户从网络看到的文件时，这些文件可能出现在 E盘，F盘，G盘，
但是实际情况可能是有些在本地硬盘和其它的在网络服务器。
在进行数据管理操作时，MTF\_VOLB DBLK 会基于用户的逻辑角度引用
数据的物理位置。
在一个给定的卷中，MTF\_VOLB DBLK 必须先任何的目录描述块。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h</td><td>卷属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>56 38h</td><td>设备名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>60 3Ch</td><td>卷名称</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>64 40h</td><td>机器名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>68 44h</td><td>媒介写日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <caption>**结构 7. 卷描述（MTF_VOLB）**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 的 DBLK 类型字段将被设置为 'VOLB'。
MTF\_DB\_HDR 包含了对所有的 DBLKs 来说都相同的字段以及一个
指向操作系特定数据区域的偏移。
这个区域包含了一个特定操作系的目录相关信息。

## 卷属性 {4 bytes}

卷属性是一个由 4 个字节组成的 32 比特的字段。
卷属性定义该 MTF\_VOLB DBLK 代表的卷块的特性。
目前只有比特 0 - 5 有定义。
比特 6 -23 预留作将来使用。
比特 2 - 5 定义了设备名称的格式。
在 MTF\_VOLB DBLKs 有且仅有一个比特设置了。

<table>
  <caption>**表 12. 卷属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>VOLB\_NO\_REDIRECT\_RESTORE\_BIT</td><td>跟随本 DBLK 的对象只能恢复到它们备份的那个设备。</td><td>BIT0</td>
  </tr>
  <tr>
    <td>VOLB\_NON\_VOLUME\_BIT</td><td>跟随本 DBLK 的对象不与某个卷关联。</td><td>BIT1</td>
  </tr>
  <tr>
    <td>VOLB\_DEV\_DRIVE\_BIT</td><td>设备名称格式为，"<drive letter>:"</td><td>BIT2</td>
  </tr>
  <tr>
    <td>VOLB\_DEV\_UNC\_BIT</td><td>设备名称格式为 UNC</td><td>BIT3</td>
  </tr>
  <tr>
    <td>VOLB\_DEV\_OS\_SPEC\_BIT</td><td>设备名称格式是操作系统相关的（查看 附录 C 来查看详细内容）</td><td>BIT4</td>
  </tr>
  <tr>
    <td>VOLB\_DEV\_VEND\_SPEC\_BIT</td><td>设备名称格式是销售商相关的</td><td>BIT5</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT6 - BIT23</td>
  </tr>
</table>

> 注意：在数据对象不与卷关联的情况下，MTF_VOLB DBLK 也需要保存设备和机器名。

## 设备名 {4 bytes}

设备名 是一个由 4 字节的 MTF\_TAPE\_ADDRESS 结构指定在字符串存储区域的位置和大小的字段。
这个字段引用的字符串标识了真正的备份源名，用作默认的恢复目标（或许是唯一允许的目录，如果不允许重定向数据的话）。

当前大多数的程序都使用盘符（C:，D:，E:，等。）或者 UNC 名称作为设备名，这是非常便于移植的，但是其他的：比如 NetWare SNS 使用了特制的，操作系统特性的设备名，就不具有移植性。
参考 附录 C 获取更多操作系统特定的设备名称格式。
销售商为了特殊需求也可以在他们的软件中选择自定义设备名称。
然而，必须指出的是当销售商使用特定的设备名时当这个卷不能重定向时，
使用另一个应用恢复的时候，用户必须知道这个源设备的名称。
在所有的情况下，卷属性的比特（BIT2 - BIT5）需要设置来定义
设备名称使用的格式。

## 卷名 {4 bytes}

卷名 是另一个使用 4 字节的 MTF_TAPE_ADDRESS 结构的字段，它指定了
指示卷名的字符串的偏移以及长度。
这个字段引用的字符串只能用于显示目的。
它用来保存如 卷标 和 网络分享注释 等东西。

## 机器名 {4 bytes}

机器名字段是另一个 4 字节的 MTF_TAPE_ADDRESS 结构，用于指示包含这个卷所属的机器名的大小和偏移（比如：PENTIUM\_1， ENG\_SERV，等。）。

## 媒介写日期 {5 bytes}

媒介写日期是一个 5 字节的 MTF_DATE_TIME 结构，包含了本媒介的日期和时间。
