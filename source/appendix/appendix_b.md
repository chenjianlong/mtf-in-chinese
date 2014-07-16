# 附录 B 密码加密算法

MTF currently defines a single password encryption algorithm based on the Message Digest 5 (MD5) algorithm as described
in RFC 1321. Password encryption can be done on the Media Password field of the MTF_TAPE DBLK and on the Data Set
Password field of the MTF_SSET DBLK.

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

The MD5 algorithm takes as input a message of arbitrary length and produces as output a 128-bit “fingerprint” or “message
digest” of the input. It is conjectured that it is computationally infeasible to produce two messages having the same message
digest, or to produce any message having a given prespecified target message digest. A copy of RFC 1321 can be obtained
from the world wide web or via email to “mtf@smg.seagate.com”.

## Media Password

The Media Password field of the MTF_TAPE DBLK can be encrypted. To encrypt, the Password Encryption Algorithm field
of the MTF_TAPE DBLK is set to a value of 5 (MD5 encryption algorithm). The unencrypted password is given as input to
the MD5 algorithm which produces as output a 128-bit “message digest” of the password. The MD5 128-bit output is stored
in the Media Password field. The Password Encryption Algorithm field is set to a value of 0 if no password encryption is
used.

> Note: If the Password Encryption Algorithm is unknown, no access to the media is allowed by software.

## Data Set Password

The Data Set Password field of the MTF_SSET DBLK can be encrypted. To encrypt, the Password Encryption Algorithm
field of the MTF_SSET DBLK is set to a value of 5 (MD5 encryption algorithm). The unencrypted password is given as input
to the MD5 algorithm which produces as output a 128-bit “message digest” of the password. The MD5 128-bit output is
stored in the Data Set Password field. The Password Encryption Algorithm field is set to a value of 0 if no password
encryption is used.

> Note: If the Password Encryption Algorithm is unknown, no access to the data set is allowed by software.
