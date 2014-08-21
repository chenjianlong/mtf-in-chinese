# 附录 C 数据压缩算法

MTF 当前定义了一种基于 Stac Technologies LZS221 compression libraries 数据压缩算法。
LZS221压缩算法的定义是为了提供支持生产环境的磁带切换的软件压缩
流。
它假设了你已经知道如何使用 LZS221 compression libraries 进行工作。

<table>
  <caption>**Table 31. Data Compression Algorithm Table**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  <tr>
    <td>MTF_LZS221</td><td>Stac Technologies LZS221</td><td>0x0ABE</td>
  </tr>
</table>

## Common Block Header

数据集中的 Common Block Headers 需要设置来表明会出现压缩流。
需要设置 Block Attributes 字段的 MTF_COMPRESSION 比特以及 Software Compression Algorithm 需要设为 0x0ABE。

> 注意：压缩不能用于  End of Set（MTF\_ESET）的数据流。

## Stream Header

在 Stream Header 的 Stream Tape Format Attributes 字段设置 STREAM\_COMPRESSED 比特以及设置 Data Compression Algorith 字段为 0x0ABE 来表示这个流是压缩的。
如果压缩流是变长的（STREAM\_VARIABLE）所有组成变长流的 Stream Headers 都需要指定使用压缩。
当启用压缩，所有的流数据都必须被 Compression Frame Headers 封装。

## LZS221 Buffer Sizes

The size of the buffers used by the Compress and Decompress routines are provided in the table below.

<table>
  <tr>
    <th>名称</th><th>说明</th><th>大小</th>
  </tr>
  <tr>
    <td>src</td><td>Source buffer</td><td>62 * 1024</td>
  </tr>
  <tr>
    <td>dst</td><td>Destination buffer</td><td>(62 * 1024) + 32</td>
  </tr>
  <tr>
    <td>scratchRAM</td><td>Scratch buffer used by LZS221.
This is defined in the LZS221 header file.</td><td>LZS_HISTORY_SIZE</td>
  </tr>
</table>

## 压缩

LZS221 compression library 定义了压缩 API。
未压缩的数据通过 src buffer 传进，而压缩后数据通过 dst buffer 返回。
以下的原型来自于 LZS221-86 compression library。

```cpp
extern void OS2_API Compress(char **src,
                             char **dst,
                             unsigned long *srcCnt,
                             unsigned long *dstCnt,
                             char *scratchRAM);
```

## 解压

LZS221 compression library 定义了解压 API。
压缩后的数据通过 src buffer 传进，而解缩后数据通过 dst buffer 返回。
以下的原型来自于 LZS221-86 compression library。

```cpp
extern int OS2_API Decompress(char **src,
                              char **dst,
                              unsigned long *srcCnt,
                              unsigned long *dstCnt,
                              char *scratchRAM);
```

## 压缩和解压伪代码

The following compress and decompress pseudo code integrates into the LZS221-86 compression library and is provided to assist in development.

**代码略**






