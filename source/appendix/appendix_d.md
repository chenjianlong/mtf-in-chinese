# 附录 D 实现问题

## 字段大小与对齐

由于大多数 32 位处理器的天性，所有的 32 位元素都被对齐到 4 字节的边界，而所有的 16 位元素对齐到 2 字节的边界。
除开这个条件，结构的真实大小依赖于处理器以及编译器的类型。
因此为了保证兼容性所有的结构需要封装为 1 字节边界。

## 软件压缩算法

在 MTF\_SSET DBLK 的 Data Encryption Algorithm 已经改为 Software Compression Algorithm。
MTF Version 1.00a 限制了一个数据集只能有一种软件压缩算法。
Software Compression Algorithm 的增加允许软件决定数据集是否有兼容的软件压缩算法。
Data Encryption 已经没有定义而且这个改变应该不影响现产品。

## Block Alignment Pad

在 Version 1.0 的 MTF 中，Block Alignment Pad 用于填充在一个 DBLK 的结尾以及下一个 DBLK 的开头。
根据填充到下一个 FLB 需要的字节数，Block Alignment Pad 可以只有几个字节也可以有上百个字节。
block alignment pad 没有头，它只是简单的 NULL 数据（二进制 0）。
Block Alignment Pads 只会用于没跟有数据流部分的 DBLK。
MTF Version 1.00a 使用 stream pad（SPAD）来取代 Block Alignment Pad。

## Offset To First Event

在公共头中的 Offset To First Event 通常用于指示与本 DBLK 关联的 stream 的头。
MTF 1.0 specification 之前的草案不需要 SPAD 数据流。
下面的方法是用确定 Offset To First Event 字段是指向一个 Stream Header 还是 DBLK 的推荐方法：

1. 检查是否是一个已知的 DBLK 或者 Stream 类型，然后使用 checksum 来验证数据的有效性。
2. 如果第一步失败了使用 checksum 来确定是否是未知的流头。
3. 如果第一步失败了使用 checksum 来确定是否是未知的 DBLK。

## Device Specific Physical Block Addressing

有两种类型的定位方法：绝对的（物理）和逻辑的。
并不是所有的驱动都支持所有的定位方法：驱动的特征比特指示了支持的类型。
同时，在某些驱动中，媒介（磁带）可以影响到是否支持定位；比如：一个驱动的特征比特可以根据驱动中的媒介以及驱动中媒介的类型来改变。

某些 Windows NT 磁带（class）在驱动中实现了逻辑磁带定位的软件模拟；
比如：它们实现了伪逻辑磁带定位。
这为那些本质上只支持绝对磁带定位的媒介在 MTF 上进行读/写 的方法。

在所有的驱动中，定位支持的目的是在写磁带的时候提供“获取位置”然后可以在写完后通过进行“设置位置”从而磁带的相同位置开始进行读取。
如果任意的媒介和驱动支持任意的定位，这个技术总是“可行”的；比如：它永远可以先“获取位置”然后“设置位置”到绝对位置或者逻辑位置。
“设置位置”到一个“合成”位置（比如：任意不是通过“获取位置”得到的地址）不一定会行得通。
在磁带中“任意访问”位置并不是目的所在；实际上，很多驱动都不支持。

然而，通常可以“设置位置”到一个“获取位置”得到的地址相对（比如：从某个偏移开始）的逻辑地址（伪地址或者真实地址）。
所有支持逻辑定位的媒介和驱动，这种相对（偏移）类型的“设置位置”可以至少做到：

* 前进方向（正的偏移），
* 从“获取位置”得到的逻辑位置，
* 从任意相对，计算出来的位置（从“获取位置”得到的逻辑地址的偏移），
* within and throughout the/any region of data that is/was consecutively and contiguously written immediately following
the logical position obtained by means of a "get position" (i.e., a data "zone" produced by consecutive and contiguous
"writes" and nothing else -- no other intermediate tape mark writes, "get positions", or whatever).

The foregoing is true both on media and drives that intrinsically support true (SCSI-2) logical positioning and on media and
drives where (driver implemented) pseudo-logical positioning is supported.

In addition to the foregoing, on media and drives that intrinsically support true logical positioning it is possible to do both
forward and reverse relative "set positions" (negative or positive offset) and relative "set positions" that cross tape mark
divisions (filemarks and/or setmarks).

In the set of tape media and tape drives handled by the current set of Windows NT tape drivers (4mmdat.sys, archqic.sys,
exabyte1.sys, exabyte2.sys, tandqic.sys, wangqic.sys, and qic117.sys), the media and drives that intrinsically support true
(SCSI-2) logical positioning are all DAT media and drives (4mmdat.sys), the 1.35 gigabyte (9135) and 2.1 gigabyte (9210)
QIC media and Archive Anaconda (model 2750 and model 2800) QIC drives (archqic.sys), and 5+ gigabyte 8mm media and
SCSI-2 8mm drives (exabyte2.sys -- Exabyte 8500 series and compatible).
In all other cases where logical positioning support
is indicated in the drive features bits, it is pseudo-logical tape positioning support; i.e., it is done by software simulation of
logical tape positioning in the driver.

Thus, although it is sometimes possible to "set position" to a position relative to (i.e., at an offset from) an absolute position
obtained by means of a "get position", it is unnecessary to do so: pseudo-logical tape positioning is implemented in the
Windows NT tape drivers where it is possible to do so.
Doing so is very media/drive unique and hence requires very
media/drive specific knowledge.
An understanding of how this is accomplished can be acquired by studying the technical
standards that govern the physical format of recorded information on the specific tape media, the tape drive technical manual(s)
and the Windows NT tape driver source code.
Translation between absolute position and pseudo-logical position and vice versa
is accomplished by module "physlogi" in the Windows NT tape drivers. The source code for this module (physlogi.c) is
included in the Windows NT DDK.
