# 变长流

变长流用于分段的流数据。
流数据中的每个段由一个流头封装。
每个用于组成变长流的流头需要设置流媒介格式属性的 STREAM\_VARIABLE 比特。
最后一个流头需要在设置流媒介格式属性的 STREAM\_VAR\_END 比特。

![](images/variable_length_streams.png)
