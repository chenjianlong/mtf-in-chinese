# 控制 Bits

在特定 DBLKs 的 MTF\_DB\_HDR 的属性字段用于对于给定数据集和媒体簇
是否尝试写 FDD 和 Set Map。
下表说明了比特的作用以及含义。
请参考 MTF\_DB\_HDR 的说明来获取属性字段的更多信息。

<table>
  <caption>**表 24. 基于媒介的 Catalog 控制 Bits**</caption>
  <tr>
    <th>Bit</th><th>Name</th><th>Description</th><th>Value</th>
  </tr>
  <tr>
    <td>MTF\_SET\_MAP\_EXISTS</td><td>这个比特在 MTF\_TAPE DBLK 表示媒体簇中的每个数据集都后面都必须写 Set Map 流。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_ALLOWED</td><td>这个比特在 MTF\_TAPE DBLK 表示媒体簇中的每个数据集都后面或许需要写 FDD 流。这个比特并不强制每个数据集都写 FDD。</td><td>BIT17</td>
  </tr>
  <tr>
    <td>MTF\_FDD\_EXISTS</td><td>这个比特在 MTF\_SSET DBLK 表示 FDD 流会写到这个数据集。如果不写 FDD，那么在 MTF\_ESET 中必须设置 MTF\_FDD\_ABORTED。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_NO\_ESET\_PBA</td><td>如果这个媒介没有数据集，因而，也不会有 Set Map 于 MTF\_ESET 与之关联，那么需要设置这个比特在 MTF\_EOTM。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_INVALID\_ESET\_PBA</td><td>如果 MTF\_ESET 的 PBA 由于设备不支持而导致不合法，那么需要在 MTF\_EOTM 设置这个比特</td><td>BIT17</td>
  </tr>
</table>
