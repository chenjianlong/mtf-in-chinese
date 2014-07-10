# 磁带标记结束描述块（MTF_EOTM）

**磁带标记结束描述块**（MTF_EOTM）用于指示遇到媒介的末尾，当写媒介的时候媒体簇跨越到另一个媒介。
当遇到 EOM 时，写操作由可能遇到一系列不同的情况。
每种情况可以以唯一的方式处理。
参考 结束媒体处理 章节。

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <tr>
    <td>52 34h><td>最后的 ESET PBA</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <caption>**结构 13. 磁带标记结束描述块**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 结构的下列成员必须设为定义的值。

* MTF\_DB\_HDR 的 DBLK 类型字段设置为 'EOTM'。
* 格式化的逻辑地址字段设为 0。
* 控制块 ID 字段设置为 0。

## 最后的 ESET PBA {8 bytes}

最后的 ESET PBA	字节使用 8 字节的 UINT64 结构来指定最后一个写到媒介的 MTF\_ESET 的物理块地址。
