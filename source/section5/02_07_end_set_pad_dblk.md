# 填充结束描述块（MTF\_ESPB）

填充结束描述块（MTF\_ESPB）只在当物理比 MTF\_TAPE DBLK 的格式化的逻辑块大时使用。
当出现这种情况时，有可能会出现数据集不是结束在物理块边界。
这种情况出现在当数据集的最后一块 DBLK 填充到格式化的逻辑边界。
当卷标作为数据集的结尾写在 MTF\_ESET DBLK 后面时，MTF\_ESPB DBLK 需要在这种情况下填充 0 到下一个物理块边界。

> **注意：** 通过扩展数据集中最后一个 SPAD 到物理块边界也可以达到同样的效果，如果你知道这是数据集中最后一个 DBLK 的话。

<table>
  <tr>
    <th>偏移</th><th>字段名</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>公共的块头</td><td>MTF\_DB\_HDR</td><td>52 字节</td>
  </tr>
  <caption>**结构 11. 填充结束描述块（MTF\_ESPB）**</caption>
</table>

## 公共的块头 {52 bytes}

公共的块头 字段是一个在每个 DBLK 开头的 52 字节的 MTF\_DB\_HDR 结构。
MTF\_DB\_HDR 的 DBLK 类型字段将被设置为 'ESPB'。
