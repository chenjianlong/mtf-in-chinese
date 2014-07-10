# 数据集结束描述块（MTF\_ESET）

数据集结束描述块（MTF\_ESET）联同一个卷标，表示一个数据集的末尾。
MTF\_ESET DBLK 重复了本数据集的 MTF\_SSET DBLK 的数据集号和媒介写日期。
另外，数据流可能需要使用来支持基于媒介的 Catalog。

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h</td><td>数据集结束描述块属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>56 38h</td><td>损坏文件的个数</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>60 3Ch</td><td>为 MBC 预留</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>68 44h</td><td>为 MBC 预留</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>76 4Ch</td><td>FDD 媒介序号</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>78 4Eh</td><td>数据集号</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>80 50h</td><td>媒介写日期</td><td>MTF\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <caption>**结构 12. 数据集结束描述块（MTF\_ESET）**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 结构的下列成员必须设为定义的值。

* MTF\_DB\_HDR 的 DBLK 类型字段设置为 'ESET'。
* 格式化的逻辑地址字段设为 0。
* 控制块 ID 字段从数据集继续。MBC 中所有的 MTF\_ESET DBLKs 共享同一个控制块 ID。

## 数据集结束描述块属性 {4 bytes}

数据集结束描述块属性是一个由 4 个字节组成的 32 比特字段。
目前只有比特 0 - 5 有定义。
比特 1 - 5 用于指定在本媒介中，用了什么备份操作来创建的这个数据集。
可能的操作包括，复制，普通备份，差异备份，增量备份和每日备份。
在给定的数据集中这 5 个比特只能设置其中一个。
如下所，“修改”标（说明一个文件是否被创建或修改）。
另一个名称是“归档“标识。
这个字段的比特 6 - 23 作预留。

<table>
  <caption>**表 16. 数据集结束描述块属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>ESET 传输比特</td><td>当数据关联擦着是”传输”时，设置这个比特。它表示当操作完成后数据集中文件从源媒介删除。</td><td>BIT0</td>
  </tr>
  <tr>
    <td>ESET 复制比特</td><td>这个比特表示操作是“复制”。复制方法会复制主存储器所有选择的文件。文件的“修改”标识不会被改变。</td><td>BIT1</td>
  </tr>
  <tr>
    <td>ESET 普通比特</td><td>这个比特表示备份类型是“普通”。普通备份方法会备份所有选择的文件。之后会重置文件的“修改”标识。</td><td>BIT2</td>
  </tr>
  <tr>
    <td>ESET 差异比特</td><td>这个比特表示备份类型是“差异”。差异备份只备份选择的文件设置了“修改”标识的文件。之后“修改”标识不会被重置。</td><td>BIT3</td>
  </tr>
  <tr>
    <td>ESET 增量比特</td><td>这个比特表示备份类型是“增量”。增量备份方法只会备份选择的文件中设置了“修改”标识的文件。之后会重置文件的“修改”标识。</td><td>BIT4</td>
  </tr>
  <tr>
    <td>ESET 每日比特</td><td>这个比特表示备份类型是“每日”。每日备份方法只会备份那些选择的文件中文件创建或修改日期为今天的文件。之后文件的“修改”标识不会被重置。</td><td>BIT5</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT6 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>销售商特定</td><td>BIT24 - BIT31</td>
  </tr>
</table>

## 文件/目录 细节 PBA {8 bytes}

文件/目录 详细 PBA 字段使用 8 字节的 UINT64 结构来指定
MBC 文件/目录 细节流的物理块地址。
FDD 流是一个和数据流关联的标识这个MTF\_ESET DBLK 的结尾。

## FDD 媒介序号 {2 bytes}

FDD 媒介序号 是一个 2 字节的指示和本数据集关的 文件/目录 细节的媒介序号的字段。

## 数据集号 {2 bytes}

数据集号是一个包了和本数据集关联的 ID 号的两字节的字段。
应该和 MTF\_SSET DBLK 的数据集号相同。
媒介中的第一个数据集号从 1（0x01）开始，每有一个新的数据集附加到这个媒介会增加 1。
参考 MTF\_SSET DBLK 字段来获取更多信息。

## 媒介写日期 {5 bytes}

媒介写日期 使用 5 字节的 MTF\_DATE\_TIME 结构来指示本数据集创建的确日期和时间。
