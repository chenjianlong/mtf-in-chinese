# 附录 B 密码加密算法

MTF 目前定义了一种密码加密算法，它是基于 RFC 1321 描述的 MD5 算法。
密码加密算法可以用在 MTF\_TAPE DBLK 的 Media Password 字段以及 MTF\_SSET DBLK 的  Data Set Password 字段。

<table>
  <caption>**Table 30. Password Encryption Algorithm Table**</caption>
  <tr>
    <th>名称</th><th>说明</th><th>值</th>
  </tr>
  </tr>
    <td>MTF\_MD5</td><td>Message Digest 5</td><td>5</td>
  </tr>
</table>

## Message Digest 5

MD5 算法是输入任意长度的信息然后输出 128-比特 的输入信息的 “指纹” 或者说 “信息摘要” 。
可以认为两条不同的消息是不大可能产生相同的消息摘要的，或者得到生成特定消息摘要的消息。
RFC 1321 的副本可从万维网或通过电子邮件 “mtf@smg.seagate.com” 获得。

## Media Password

MTF\_TAPE DBLK 中的 Media Password 字段可以加密。
为了加密需要将 MTF\_TAPE DBLK 的 Password Encryption Algorithm 字段设为 5（MD5 加密算法）。
未加密的密码作为 MD5 算法的输入然后输出 128-比特的 “消息摘要” 。
MD5 的 128-比特输出保存在 Media Password 字段。
如果不使用密码加密算法可以将 Password Encryption Algorithm 字段设为 0。

> 注意：如果 Password Encryption Algorithm 字段是未知的，那么软件不允许访问。

## Data Set Password

MTF\_SSET DBLK 中的 Data Set Password 字段可以加密。
为了加密需要将 MTF\_SSET DBLK 的 Password Encryption Algorithm 字段设为 5（MD5 加密算法）。
未加密的密码作为 MD5 算法的输入然后输出 128-比特的 “消息摘要” 。
MD5 的 128-比特输出保存在 Data Set Password 字段。
如果不使用密码加密算法可以将 Password Encryption Algorithm 字段设为 0。

> 注意：如果 Password Encryption Algorithm 字段是未知的，那么软件不允许访问。
