# 数据集起始描述块（MTF\_SSET）

**数据集起始描述块**（MTF\_SSET）包含了描述数据集的信息。
MTF\_SSET DBLK 放在数据集的开头。
这个结构包含了标识和描述数据集所有重要方面的信息，比如：
物理块地址（PBA），用户名称和地址，数据管理软件的版本，
使用的加密和压缩算法，数据集号，备份属性，媒介写入时间，
时区，使用的 MTF 副版本号，等。

MTF\_SSET 由 MTF\_DB\_HDR 结构以及跟着一系列的字段和字符串存储区域组成。
字符串存储区域用于存储类似数据集的名称，用户名等字符串。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>名称</th><th>类型</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h</td><td>SSET 属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>56 38h</td><td>密码加密算法</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>58 3Ah</td><td>软件压缩算法</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>60 3Ch</td><td>软件销售商 ID</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>62 3Eh</td><td>数据集号</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>64 40h</td><td>数据集名称</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>68 44h</td><td>数据集说明</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>72 48h</td><td>数据集密码</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>76 4Ch</td><td>用户名</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>80 50h</td><td>物理块地址（PBA）</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>88 58h</td><td>媒介写日期</td><td>MTF_DATE_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>93 5Dh</td><td>软件主版本</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>94 5Eh</td><td>软件副版本</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>95 5Fh</td><td>时区</td><td>INT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>96 60h</td><td>MTF 副版本号</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <tr>
    <td>97 61h</td><td>媒介 Catalog 版本</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <caption>**结构 6. 数据集起始描述块（MTF\_SSET）**</caption>
</table>

## 公共的块头

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 的 DBLK 类型字段需要设置为 'SSET'。

## SSET 属性 {4 bytes}

SSET 属性字段是一个由 4 个字节组成的 32 位字。
目前只定义了比特 0 - 5。
比特 1 - 5 用于指定媒介中跟在 MTF\_SSET DBLK 后的数据集所使用的备份操作。
可能的操作包括，复制，普通备份，差异备份，增量备份和每日备份。
在一个数据集中这 5 个比特中只能设置其中 1 个。
在下面的说明中，提及了 “修改” 标识（说明一个文件是否被创建修改）。
另外也称为 ”归档“ 标识。
比特 6 - 23 预留为将来使用。

<table>
  <caption>**表 9. SSET 属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>SSET\_TRANSFER\_BIT</td><td>这个比特表示数据管理操作是”传输“。它表示在此数据集的数据会在传输完毕后从源媒介删除。</td><td>BIT0</td>
  </tr>
  <tr>
    <td>SSET\_COPY\_BIT</td><td>这个比特表示操作是”复制“。复制方法会将所有选择的文件从主存储器复到媒介。文件的 ”修改“ 标识 **不会** 重置。</td><td>BIT1</td>
  </tr>
  <tr>
    <td>SSET\_NORMAL\_BIT</td><td>这个比特表示备份类型是”普通“。普通备份方法是备份所有选择的文件。文件的”修改“标识会重置。</td><td>BIT2</td>
  </tr>
  <tr>
    <td>SSET\_DIFFERENTIAL\_BIT</td><td>这个比特表示备份类型是”差异“。差异备份类型只备份那些选择的文件中设置了“修改”标识的文件。备份完成后”修改“标识不会重置。</td><td>BIT3</td>
  </tr>
  <tr>
    <td>SSET\_INCREMENTAL\_BIT</td><td>这个比特表示备份类型是”增量“。增量备份类型只备份那些选择的文件中设置了“修改”标识的文件。备份完成后重置”修改“标识。</td><td>BIT4</td>
  </tr>
  <tr>
    <td>SSET\_DAILY\_BIT</td><td>这个比特表示备份类型是“每日”。每日备份只会备份那些创建或者修改日期是今天的文件。备份完成后”修改“标识不会重置。</td><td>BIT5</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT6 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>销售商指定</td><td>BIT24 - BIT31</td>
  </tr>
</table>

## 密码加密算法 {2 bytes}

密码加密算法 字段是一个用于指定用来加密数据集密码字段的两个字节的算法 ID。
如果密码存在而加密算法未知，应用程序不应该访问此媒介。
这个值必须是 “已注册” 的算法号。

## 软件压缩算法 {2 bytes}

软件压缩算法 字段是一个指定在数据集中与 DBLK 关联的数据流所使用的压缩算法的 ID。
这个两个字节的值必须是一个 “已注册” 压算号

> 注意：如果 MTF\_COMPRESSION 比特在 公共的块头的块属性字段设置了并且 软件压算法 的值为 0，
除非遇到第一个 压缩流头，不能确定 软件压缩算法。

## 软件销售商 ID {2 bytes}

_软件销售商 ID_ 字段指明是哪个销售商的软件写了这个数据集。
这个值必须是一个 “已注册” 的 软件销售商 ID。

## 数据集号 {2 bytes}

数据集号 字段是一个两个字节的包含了与数据集关联的 ID 号。
媒体簇中的第一个数据集的数据集号从 “1”（0x01）开始，
并且这个媒体簇中以后每个新的数据集的数据集号都加 1。
当一个数据集跨越到一个新的媒介时，MTF\_SSET DBLK 的 MTF\_DB\_HDR 的
块属性 字段的 MTF\_CONTINUATION 比特会设，但是数据集号会保持一致。

## 数据集名称 {4 bytes}

数据集名称 字段使用一个 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定
字符串的位置和大小。
这个字符串存储在字符串存储区域，包含了赋给数据集的名称。
如果没有与数据集关联的名称，MTF\_TAPE\_ADDRESS 的 Size 字段为 0。

## 数据集说明 {4 bytes}

数据集说明 字段使用一个 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定
包含了数据集说明的字符串的位置和大小。
如果没有与数据集关联的说明，MTF\_TAPE\_ADDRESS 的 Size 字段为 0。

## 数据集密码 {4 bytes}

数据集密码 字段使用一个 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定
包含了数据集密码的字符串的位置和大小。
字符串关联的数据会使用 _密码加密算法_ 字段指定的算法来加。
如果没有与数据集关联的密码，MTF\_TAPE\_ADDRESS 的 Size 字段为 0。

## 用户名 {4 bytes}

用户名 字段使用一个 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定
包含了创建此数据集的用户名的字符串的位置和大小。
如果此数据集没有关联的用户名， MTF\_TAPE\_ADDRESS 的 Size 字段为 0。

## 物理块地址 （PBA） {8 bytes}

物理块地址 （PBA） 字段使用 UINT64 结构来指定这个 MTF\_SSET 的物理块地址。
PBAs 从设备获取。
对于所有的 软件 和/或 设备来说使用相同的规则来生成 PBAs 非常重要。
查看 3.6 节来查看 PBAs 的描述，而 附录 L 查看 设备相关的细节。

## 媒介写日期 {5 bytes}

媒介写日期 使用 5 个字节的 MTF\_DATE\_TIME 结构来指定数据集创建的确切时间和日期。

## 软件主版本 {1 byte}

软件主版本 是一个用于指示创建了这个数据集的应用的主版本的单字节字段。
例如，如果数据集是由 Ultimate Enterprise Backup Ver. 3.1 创建的，这个字段的值为 3。

## 软件副版本 {1 byte}

软件主版本 是一个用于指示创建了这个数据集的应用的副版本的另一个单字节字段。
这个值应该为 1，如果是上面的例子话。
主和副版本号都是销售商指定的并且必须取值为 0 到 255 之间。

## 时区 {1 byte}

时区 字段是一个指定 UTC 和本地时间差异的单字节字段。
这个差异是按每个值相差 15 分钟。
因此，这个值必须在 -48 到 48 之间。
（如果 EST 是 -20 表示它是比 UTC 晚 5 个小时）。
如果时间不是以 UTC 作为坐标，这个字段必须为 127。

<table>
  <caption>**表 10. 时区**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>LOCAL\_TZ</td><td>指明本地时间不是以 UTC 为坐标的。</td><td>127</td>
  </tr>
</table>

## MTF 副版本号 {1 byte}

MTF 副版本 是一个单字节的指明 MTF 的副版本的字段。
比如，MTF Version 2.3 表示 副版本号为 “3”。
数据集中的副版本号可以有所不同，因此在 MTF\_SSET DBLK
指定，然而主版本号在整个媒介中必须保持一致因此指定在 MTF_TAPE DBLK。

如果附加的字段添加到了 DBLK，那么 MTF 副版本号需要增加。
在后来的副版本中不能删除任何字段。
因此，如果这个值大于等于你的软件所能理解的最大值，
你的程序可以认为所有的字段都正确的初始化了。

对每个主版本的 MTF 来说，副版本从 “0” 开始。
这个字段允许 255 个副版本。

Table 11. Minor Version Numbers
Name
Value
MTF Minor Version for Major Version “1”
0
<table>
  <caption>**表 11. 副版本号**</caption>
  <tr>
    <th>名称</th><th>值</th>
  </tr>
  <tr>
    <td>MTF Minor Version for Major Version “1”</td><td>0</td>
  </tr>
</table>

参考 MTF_TAPE DBLK 结构中的 MTF 主版本号如何定义。

## 媒介 Catalog 版本 {1 byte}

媒介 Catalog 版本 字段是一个指明写到本媒介的 基于媒介的 Catalog 的单字节版本。
基于媒介的 Catalog 由一个 “类型“ 和一个 ”版本“ 。
这和 MTF 格式的主版本号和副版本号类似。
版本号可以在基于数据集不同而有所不同因此放在 MTF_SSET DBLK。
例如，不同版本的 基于媒介的 Catalog 会有不同的 文件/目录 细节。
在媒体簇中 MBC 的类型不能改变，因此放在 MTF_TAPE DBLK 中。
媒介 Catalog 版本指定基于媒介的 Catalog 的定义。

