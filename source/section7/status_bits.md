# 状态 Bits

在某些 DBLKs 中的 MTF\_DB\_HDR 的属性字段中使用状态位来表示写 FDD 和 Set Map 是否成功。
下表描述了使用的比特和它们的含义。
请参考 MTF\_DB\_HDR 来获取更多关于属性字段的使用信息。

<table>
  <caption>**表 25. 基于媒介的 Catalog 状态 Bits**</caption>
  <tr>
    <th>比特名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>MTF\_FDD\_ABORTED</td><td>在第二个 MTF\_ESET 设置表示由于某些错误 FDD 没有写。</td><td>BIT16</td>
  </tr>
  <tr>
    <td>MTF\_END\_OF\_FAMILY</td><td>在第二个 MTF\_ESET 设置表示由于某些错误没有写 Set Map 流。注意这个比特同时隐含本媒体簇不会有其他的数据集附加过来。</td><td>BIT17</td>
  </tr>
</table>
