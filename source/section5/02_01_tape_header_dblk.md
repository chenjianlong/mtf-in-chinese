# 磁带头描述块（MTF\_TAPE）

磁带头描述包含了当前媒介的通用信息。
MTF\_TAPE DBLK 是媒介中的第一个块并包含了对于媒体簇来说非常重的信息，
比如 媒介序列，ID，名称，描述，等。
MTF\_TAPE DBLK 中的其他字段记录了媒介必须保持不变的行为。
这些行包括 MTF 的主版本号，基于媒介的 Catalog 的类型，
以及媒介中所有 DBLKs 指定字节对齐的 格式化的块大小。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h</td><td>媒体簇 ID</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>56 38h</td><td>磁带属性</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>60 3Ch</td><td>媒介序号</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>62 3Eh</td><td>密码加密算法</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>64 40h</td><td>软卷标块大小</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>66 42h</td><td>基于媒介的 Catalog 类型</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>68 44h</td><td>媒介名称</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>72 48h</td><td>媒介描述/媒介标签</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>76 4Ch</td><td>媒介密码</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>80 50h</td><td>软件名称</td><td>MTF\_TAPE\_ADDRESS</td><td>4 字节</td>
  </tr>
  <tr>
    <td>84 54h</td><td>格式化逻辑块大小</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>86 56h</td><td>软件销售商 ID</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>88 58h</td><td>媒介日期</td><td>MFT\_DATE\_TIME</td><td>5 字节</td>
  </tr>
  <tr>
    <td>93 50h</td><td>MTF 主版本号</td><td>UINT8</td><td>1 字节</td>
  </tr>
  <caption>**结构5. 磁带头描述**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头都有的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 结构的下列字段必须设置为特定的值。

* DBLK 类型字段设为 'TAPE'
* 格式化的逻辑地址字段设置为 0
* Control Block ID 字段设置为 0

## 媒体簇 ID {4 bytes}

媒体簇 ID 字段是一个用于标识媒体簇属于哪个媒介的 4 字节数字。
后续的媒介必须保持这个字段不变。
MTF Version 1.00a 没有提供算法来生成唯一的 媒体簇 ID。

## 磁带属性 {4 bytes}

磁带属性 字段用于指定这个媒介涉及到的数据内容的属性的 4 个字节的字段。
比特 0 - 1 的定义如下。
比特 2 - 23 为将来使用而预留，最高的 8 比特（BIT 24 - 31）为销售商指定属性而预留。

<table>
  <caption>**表 5. 磁带属性**</caption>
  <tr>
    <th>名称</th><th>描述</th><th>值</th>
  </tr>
  <tr>
    <td>TAPE\_SOFT\_FILEMARK\_BIT</td><td>这个比特如果设置的话表示使用 软卷标</td><td>BIT0</td>
  </tr>
  <tr>
    <td>TAPE\_MEDIA\_LABEL\_BIT</td><td>这个比特如果设表示媒介描述/媒介标签 字段包含 媒介标签</td><td>BIT1</td>
  </tr>
  <tr>
    <td></td><td>预留（设为 0）</td><td>BIT2 - BIT23</td>
  </tr>
  <tr>
    <td></td><td>销售商特定属性</td><td>BIT24 - BIT31</td>
  </tr>
</table>

## 媒介序号 {2 bytes}

媒介序号 字段在第一个媒介中从“1”开始，
并且每处理媒体簇中的一个新的媒介的时候这个值加 1。

## 密码加密算法 {2 bytes}

密码加密算法 字段指定 媒介密码 字段使用的加密算法。
当密码存在而加密算法未知时，应用程序应访问此媒介。
这个字段的值必须是一个“注册”的算法号。

## 软卷标块大小 {2 bytes}

软卷标块大小 字段表示 软卷标（MTF\_SFMB）DBLK 包含了多少个 512 字节大小（比如：值为 2 表示 MTF\_SFMB DBLK 的大小为 1024 字节）。
软卷标块大小 通过 设备驱动返回的物理块大小 计算出来。
只有 软卷标仿真器 才会需要 软卷标块大小。

## 基于媒介的 Catalog 类型 {2 bytes}

基于媒介的 Catalog 类型 字段表示使用哪种格式的 基于媒介的 Catalog，附录 B 详细讲述了 基于媒介的 Catalog 的格式。
基于媒介的 Catalog “类型” 必须在整个媒体簇中保持一致，
因此在 MTF\_TAPE\_DBLK 中指定。

在 MTF\_SSET DBLK 中有一个MBC ”版本“字段指示了特定
MBC 类型的副版本号。

<table>
  <caption>表6. 基于媒介的 Catalog 类型</caption>
  <tr>
    <th>名称</th><th>值</th>
  </tr>
  <tr>
    <td>不使用 MBC</td><td>0</td>
  </tr>
  <tr>
    <td>第一类 MBC</td><td>1</td>
  </tr>
  <tr>
    <td>第二类 MBC</td><td>2</td>
  </tr>
</table>

## 媒介名称 {4 bytes}

媒介名称字段使用 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定
为用户标识媒介的字符串的大小和位置。
如果没有名称和媒介关联，那么 MTF\_TAPE\_ADDRESS 和 Size 字段为 0。

## 媒介描述/媒介标签 {4 bytes}

媒介描述/媒介标签 字段也是使用 4 字节的 MTF\_TAPE\_ADDRESS 结构来指定
媒介描述字符串或者软件生成媒介标签。
如果是 磁带属性 中的 TAPE\_MEDIA\_LABEL\_BIT 比特设置了，那么就是使用媒介标签。
如果没有 描述/标签 与媒介关联那么 MTF_TAPE_ADDRESS 的 Size 字段为 0。

### 媒介描述定义

媒介描述定义用于使用人类可读的格式描述媒介的内容。

### 媒介标签定义

媒介标签是当 MTF 或者 非 MTF 媒介引进时应用生成的一个
唯一的标识。
应用程序使用 媒介标签 来管理媒介。
当唯一的媒介标签生成后，每次媒介重写的时候应用必须保留这个媒介标签。
媒介标签是由 标签，版本，销售商，销售商 ID，创建时间戳，匣标号，
面，媒介 ID，媒介域 ID，和 销售商特定字段。
在媒介标签中所有的字段都由 '|' 字符分割，并且都是字母数字类型并包下面的字符
‘+’, ‘-‘, ‘_’, ‘:’, ‘/’, ‘.’, ‘{‘, and ‘}’ 。

<table>
  <caption>**表7. 媒介标签**</caption>
  <tr>
    <th>名称</th><th>描述</th>
  </tr>
  <tr>
    <td>版本</td><td>媒介标签版本号。设置为三个字母 '1.0'。</td>
  </tr>
  <tr>
    <td>销售商</td><td>创建媒介标签的销售的名称</td>
  </tr>
  <tr>
    <td>销售商产品 ID</td><td>用于唯一标识创建这个媒介标签的产品。本字段由销售商决定。本字段是可选的。如果不使用本字段为空。</td>
  </tr>
  <tr>
    <td>创建时间戳</td><td>创建媒介标签的日期和时间。创建时间戳的格式是 YYYY/MM/DD.HH:MM:SS 。</td>
  </tr>
  <tr>
    <td>匣标号</td><td>印在盒子的标识，或者是条形码。
  </tr>
  <tr>
    <td>面</td><td>面 字段包含了媒介的当前面。对于单面的媒介来说，这个字段永远是字符 '1'。对于双面媒介来说，在主面时这个字段设为 '1'，而在另一面设为 '2'。
**注意：** 在双面的媒介中 标签，版本，销售商，销售商 ID，创建时间戳，匣标号，媒介 ID，媒介域 ID 必须在两面都完全一样。
    </td>
  </tr>
  <tr>
    <td>媒介 ID</td><td>媒介 ID 是一个 128 位的全局唯一标识。这个全局唯一 ID 被 Open Software Foundation 定义为 UUID（universally unique ID）。如果这个 UUID 不是用指定的 UUID 算法生的话，不能以 '{' 开头。</td>
  </tr>
  <tr>
    <td>媒介域 ID</td><td>媒介域 ID 同样是一个 UUID。它用来标识媒介是哪个媒介域的。一个域是销售指定的一组资源（比如：备份服务器）。如果这个 UUID 不是用指定的 UUID 算法生的话，不能以 '{' 开头。这个字段是可选的。如果不使用可以设为空。</td>
  </tr>
  <tr>
    <td>销售商指定</td><td>媒介标签的扩展。本字段是可选的。所有的销售商指定扩展以 'VS:' 三个字符开头。</td>
  </tr>
</table>

媒介标签的例子：

MTF Media Label|1.0|Seagate|PVL|1996/03/29.18:36:10|AB1234|1|{9EAA3460-89BA-11cf-8A04-
0000C0D9CA0D}|{7E43CEA0-89BA-11cf-8A04-0000C0D9CA 0D}| VS:First Vendor Specific Parameter

## 媒介密码 {4 bytes}

_媒介密码_ 字段使用 MTF\_TAPE\_ADDRESS 来指定包含本媒介密码的字符串的位置与大小。
关联的数据会用 _密码加密算法_ 字段指定的算法来进行加密。
如果没有关联的密码，MTF\_TAPE\_ADDRESS 的 Size 字段为 0。

## 软件名称 {4 bytes}

软件名称 字段使用 MTF\_TAPE\_ADDRESS 来指定包含创建本媒介的应用程序
的名称。MTF\_TAPE\_ADDRESS 结构的 Size 字段不能为 0。

## 格式化逻辑块大小 {2 bytes}

格式化逻辑块大小 是一个用于指定被媒介所有 DBLKs 对齐的两字节字段。
格式化逻辑块大小 的合法值为 512 和 1024 字节。

## 软件销售商 ID {2 bytes}

软件销售商 ID 是一个用于标识写入本媒介的软件的 销售商 ID 的两字节字段。这个值必须是一个 “注册” 的 软件销售商 ID。

## 媒介日期 {5 bytes}

媒介日期 使用一个 5 字节的 MTF\_DATE\_TIME 结构来指示本媒介首次创建的具体日期和时间。
精度为秒。

## MTF 主版本号 {1 byte}

MTF 主版本号 是一个单字节的用于标创建本媒介的 Microsoft Tape Format 的主版本号。
在 MTF Version 1.00a 中版本号从 “1” 开始并且每个主版本增加 1。
这个字段允许有 255 个主版本。
写到这个媒体簇的所有集合都必须使用同一个主版本号。
MTF 的副版本号在 MTF\_SSET DBLK 中标识。
MTF Version 1.00a 的副版本号为 “0”。
同一个 MTF 主版本的不同副版本可以在 DBLk 中有不同的结构但必须保持
向后兼容（比如：字段不能删除只能添加）。
请查看 **数据集起始描述块** 来获取更多信息。

<table>
  <caption>**表 8. 主版本号**</caption>
  <tr>
    <th>名称</th><th>值</th>
  </tr>
  <tr>
    <td>MTF 主版本号</td><td>1</td>
  </tr>
</table>
