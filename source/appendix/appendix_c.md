# 附录 C 数据压缩算法

MTF currently defines a single data compression algorithm based on the Stac Technologies LZS221 compression libraries. The
definition of the LZS221 compression algorithm is intended to provide cross product tape interchange of software compressed
streams. It is assumed that a working knowledge of the LZS221 compression libraries is known.

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

All Common Block Headers in the Data Set are set to indicate possibility of compressed streams. The MTF_COMPRESSION
bit is set in the Block Attributes field and the Software Compression Algorithm field is set to the value of 0x0ABE.
Note: Compression cannot be used on End of Set (MTF_ESET) Data Streams.

## Stream Header

To indicate the stream is compressed in the Stream Header, set the STREAM_COMPRESSED bit in the Stream Tape Format
Attributes field and set the Data Compression Algorithm field to the value of 0x0ABE. If the compressed stream is variable
length (STREAM_VARIABLE), all Stream Headers used to make up the variable length stream are set to indicate
compression is active. Once compression is active, all stream data must be encapsulated by Compression Frame Headers.

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

## Compress

The LZS221 compression library specifies a Compress API. Uncompressed data is passed in the src buffer and compressed
data is returned in the dst buffer.
The following prototype is from the LZS221-86 compression library.

```cpp
extern void OS2_API Compress(char **src,
                             char **dst,
                             unsigned long *srcCnt,
                             unsigned long *dstCnt,
                             char *scratchRAM);
```

## Decompress

The LZS221 compression library specifies a Decompress API. Compressed data is passed in the src buffer and uncompressed
data is returned in the dst buffer.
The following prototype is from the LZS221-86 compression library.

```cpp
extern int OS2_API Decompress(char **src,
                              char **dst,
                              unsigned long *srcCnt,
                              unsigned long *dstCnt,
                              char *scratchRAM);
```

## Compress and Decompress Pseudo Code

The following compress and decompress pseudo code integrates into the LZS221-86 compression library and is provided to assist in development.

**代码略**






