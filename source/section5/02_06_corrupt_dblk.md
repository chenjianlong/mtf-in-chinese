# 损坏对象描述块（MTF\_CFIL）

经常会出现 DBLK 已写但是由于硬盘损坏，网络失败等原因导致不是所有关联的数据都成功读取的情况。
当出现这种情况时，不能正确读的流需要填充到正确的流大小。

一个 **损坏对象描述块**（MTF\_CFIL）随后会写入来指明上一个 DBLK 关联的数据损坏了。
MTF\_CFIL DBLK 包含了指示损坏的位置和长度。
它不用于任何类型的媒介错误恢复。

如果需要的话，对于一个特定对象只需一个 MTF\_CFIL DBLK 写到媒介。
任何因为损坏而无法读取的部分被替换为 0。

> 注意：在流的头中指定的字节必须写到媒介中。

对象数据必须被填充的原因是大多数设备不允许回退到流的头来重写长度值。
如果流是变长的，如果没有合法的数据需要写，那么没必要完成超过本段的流。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h<td>损坏对象属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>56 3Ch</td><td>预留</td><td>- - -</td><td>8 字节</td>
  </tr>
  <tr>
    <td>64 40h</td><td>流偏移</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>72 48h</td><td>损坏的流号</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <caption>**结构 10. 损坏对象描述块（MTF\_CFIL）**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 的 DBLK 类型字段将被设置为 'CFIL'。
MTF\_DB\_HDR 包含了对所有的 DBLKs 来说都相同的字段以及一个
指向操作系特定数据区域的偏移。
这个区域包含了一个特定操作系的目录相关信息。

## 损坏对象属性 {4 bytes}

损坏对象属性 是一个 4 个字节组成的 32 比特的字段。
这个字段指定了这个 MTF\_CFIL DBLK 的损坏数据的属性。
目前只有比特 16 - 18 有定义。
下表描述了比特的含义。

<table>
  <caption>**表 15. 损坏对象属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>CFIL\_LENGTH\_CHANGE\_BIT</td><td>本比特表示只从因为写操作打开文件以来文件的大小被修改了。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>CFIL\_UNREADABLE\_BLK\_BIT</td><td>本比特表读取源媒介发生硬错误（硬盘）。这通常表示媒介已经损坏（比如，坏扇区）。</td><td>BIT17</td>
  </tr>
  <tr>
    <td>CFIL\_DEADLOCK\_BIT</td><td>本比特表示文件被死锁。（比如：在支持文件锁的系统中，在一段时间内无法解锁文件区域）。</td><td>BIT18</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT0 - BIT15
BIT19 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>销售商特定</td><td>BIT24 - BIT31</td>
  </tr>
</table>

## 流偏移 {8 bytes}

流偏移字段使用 UINT64 结构来指定数据流中损坏填充开始的偏移。
如果数据流是压缩的，偏移是当数据解压时损坏数据的偏移。

## 损坏的流号 {2 bytes}

损坏的流数量是一个用于指示上一个 MTF\_FILE DBLK 的数据流中哪个流包含损坏数据填充的两个字节的字。
需要记住跟在 MTF\_FILE DBLK 后面的数据流部分可以包含若干个数据流每个都有一个流的头。
第一个数据流中的流为 1，第二个为 2，依次类。
