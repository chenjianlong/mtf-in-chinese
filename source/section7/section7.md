# 基于媒介的 Catalog

本部分提供了关于基于媒介的 Catalog（MBC ）的详细信息。
MBC 由两部分组成。
第一部分是包含媒体簇中每个数据集的累加信息的 Set Map。
第二部分是某个备份集的 文件/目录 细节。
Set Map 和 文件/目录 细节存储为与 MTF\_ESET DBLK 关联的
数据流。
MTF Version 1.00a 定义了 MBC 的两种不同实现。
首先是 “第一类”，其是 “第二类”。
在媒体簇中 catalog 的类型必须保持一致。
必须在 MTF\_TAPE DBLK 指明使用哪种 MBC。
MBC 的版本可以在 MTF\_SSET DBLK 的版本字段指明。
