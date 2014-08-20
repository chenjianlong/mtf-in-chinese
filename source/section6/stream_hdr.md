# 流头（MTF\_STREAM\_HDR）

每个数据流的开头都有一个 流头（MTF\_STREAM\_HDR） 结构。
和给定的 DBLK 关联的第一个流头由 DBLK 的开头的偏移定位。
这个偏移保存在 DBLK 的 MTF\_DB\_HDR 部分的 Offset To First Event 字段。
所有的 流头 都是从 4 字节的边界开始的。

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>流 ID</td><td>UINT32</td><td>4 字节</td>
  </tr>
  <tr>
    <td>&nbsp;4 &nbsp;4h</td><td>流文件系统属性</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;6 &nbsp;6h</td><td>流媒介格式属性</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>&nbsp;8 &nbsp;8h</td><td>流长度</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>16 10h</td><td>数据加密算法</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>18 12h</td><td>数据压缩算法</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <tr>
    <td>20 14h</td><td>校验</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <caption>**结构 15. 流头（MTF\_STREAM\_HDR）**</caption>
</table>

## 流 ID {4 bytes}

流 ID 是一个 4 字节的用于标识数据流的类型的字段。
如下表所示的 4 字节的 ASCII 值用于特定的流 ID。
可以添加额外的 4 字节 ASCII 值。

## 流文件系统属性 {2 bytes}

流文件系统属性 是一个 2 字节的一共 16 比特的字段。
目前只定义了比特 0 - 2，其余的预留。
这些属性比特提供了关于流中的数据的质量的有用信息。
定义如下所示。

<table>
  <caption>**表 17. 流文件系统属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>STREAM\_MODIFIED\_BY\_READ</td><td>流中的数据在读取后改变了，不要尝试验证操作。</td><td>BIT0</td>
  </tr>
  <tr>
    <td>STREAM\_CONTAINS\_SECURITY</td><td>这个流中包含有安全信息。</td><td>BIT1</td>
  </tr>
  <tr>
    <td>STREAM\_IS\_NON\_PORTABLE</td><td>这个数据只能恢复到和源系统相同的系统中。</td><td>BIT2</td>
  </tr>
  <tr>
    <td>STREAM\_IS\_SPARSE</td><td>流数据是稀疏的（查看下面）</td><td>BIT3</td>
  </tr>
  <tr>
    <td></td><td>预留</td><td>BIT4 - BIT15</td>
  </tr>
</table>

**STREAM\_IS\_SPARSE**

STREAM\_IS\_SPARSE 比特表示接下来的数据是稀疏的，并且封装为 'SPAR' 数据流。
初始的流头在 流 ID 字段指定 类型为稀疏数据（比如：STANDARD\_DATA\_STREAM），在 流文件系统属性 字段设置 STREAM\_IS\_SPARSE 比特以及将流长度设为零。
紧跟在初始的流头后面的是一个或多个的封装成稀疏数据的 'SPAR' 数据流。

## 流媒介格式属性 {2 bytes}

流媒介格式属性 是一个 2 字节的提供了流的涉及格式的物理特性信息的字段。
它们的定义如下。

<table>
  <caption>**表 18. 流媒介格式属性**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>STREAM\_CONTINUE</td><td>这是一个跨越流。</td><td>BIT0</td>
  </tr>
  <tr>
    <td>STREAM\_VARIABLE</td><td>这个流的数据大小是可变的。</td><td>BIT1</td>
  </tr>
  <tr>
    <td>STREAM\_VAR\_END</td><td>变长数据的最后一块。</td><td>BIT2</td>
  </tr>
  <tr>
    <td>STREAM\_ENCRYPTED</td><td>这个流是加密的。</td><td>BIT3</td>
  </tr>
  <tr>
    <td>STREAM\_COMPRESSED</td><td>这个流是压缩的。</td><td>BIT4</td>
  </tr>
  <tr>
    <td>STREAM\_CHECKSUMED</td><td>这个流会跟着一个校验流。</td><td>BIT5</td>
  </tr>
  <tr>
    <td>STREAM\_EMBEDDED\_LENGTH</td><td>这个流的长度嵌在数据中。</td><td>BIT6</td>
  </tr>
  <tr>
    <td></td><td>预留</td><td>BIT7 - BIT15</td>
  </tr>
</table>

**STREAM\_EMBEDDED\_LENGTH**

STREAM\_EMBEDDED\_LENGTH 比特已经废弃，在这里还提供的主要原因是向后兼容。
当启用压缩时这个比特用于将流长度内嵌，并且将流数据分为变长流。
这个功能现在由 压缩帧头 提供。

<table>
  <tr>
    <th>偏移</th><th>内容</th><th>类型</th><th>大小</th>
  </tr>
  <tr>
    <td>&nbsp;0 &nbsp;0h</td><td>流长度</td><td>UINT64</td><td>8 字节</td>
  </tr>
  <tr>
    <td>&nbsp;8 &nbsp;8h</td><td>校验</td><td>UINT16</td><td>2 字节</td>
  </tr>
  <caption>**结构 16. STREAM\_EMBEDDED\_LENGTH**</caption>
</table>

## 流长度 {8 bytes}

流长度 字段使用 8 字节的 UINT64 结构来指定当前流的长度。
流长度不包括 MTF_STREAM_HDR 的大小或者用于对齐的填充数据。

## 数据加密算法

数据加密算法 是使用 2 字节的包含了当前用来加密数据的加密算法注册 ID 的字段。
只有当流媒介格式属性设置了 STREAM\_ENCRYPTED 比特后这个字段才有用。

## 数据压缩算法

数据压缩算法 是一个当前用来压缩数据的算法注册 ID 的字段。
只有当流媒介格式属性设置了 STREAM\_COMPRESSED 比特后这个字段才有用。

## 校验

校验 包含了对所有字段进行按字进行 XOR 操作的总和。
不包括 2 字节的校验字段本身。
这个字段用于在读操作时验证这是一个合法的流说明。

需要注意的，这个字段不用于文件内容的校验。
它只用于验证流头（MTF\_STREAM\_HDR）的信息。
