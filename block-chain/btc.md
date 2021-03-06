## 比特币原理笔记

### 1 数字

1.1 一个大于1的自然数，除了1和它自身外，不能被其他自然数整除的数叫做质数；否则称为合数。质数如2、3、5、7

1.2 每一个合数都可以以唯一形式被写成质数的乘积，即分解质因数。

### 2 大数

2.1 宇宙所有的原子数大约在10的60次方到80次方之间

2.2 2的256次方结果为1.1579208923731619542357098500869 × 10的77次方

2.3 长度256位的数，逐个猜测也不可能，即便每猜一次消耗一个原子的能量，根据2.1宇宙中的原子能量全消耗了也不一定能猜出来

### 3 加密解密

3.1 根据1，设计一种算法，将明文通过数字x加密，可以通过数字y进行解密，其中y是通过x计算出来的。此为RSA加密算法

3.2 可以将y公诸于众，拥有x的A用户加密的数据，其他人可以通过y确认确实是A发送的

3.3 以上x叫做私钥，y叫做公钥

3.4 设定私钥的长度为256个0或1，则为2的256次方，根据2.2可得重复的概率小到忽略不计

3.5 根据私钥x计算出来的y也是256个0或1，同3.4。无法根据公钥找到私钥（如果能反推，金融互联网等行业要重构）

3.6 对任意数据以512个0或1进行分组，最后不足512在后面补1000（N个0），最后64位为数据长度，每组512位进行与或加大质数等赋值到8个32位整数，再对512位数字加大质数和结果赋值到8个32位整数里并循环64次并每次循环使用不用的质数。此为哈希256算法

3.7 哈希256算法不管多长的数据都能变成256位的结果，原数据不可还原；相似的内容经过哈希算法后千差万别；根据2.2，哈希算法后得到一样的结果的概率忽略不计

### 4 交易

4.1 公钥y1有50个币，向公钥y2送20个币，将这条（记录r1） + （公钥y1） + （私钥x1加密记录之后的值z1）公诸于众。此为交易

4.2 其他人用公钥y1解密z1得到记录r1，确认是私钥x1的主人操作，知道现在y1还有30个币，y2有20个币。此为交易验证

4.3 现在公钥y2可以向其他人发送币最多20个币了

4.4 每笔交易对应一个交易地址，由交易数据哈希生成256位不重复

4.5 每笔交易的数据包含 币的来源地址即4.4的地址（可以为多个） + 交易对象公钥地址和数量 + 自己剩下的数量和公钥地址（全转了就没有） + 手续费（矿工费）

### 5 区块

5.1 区块存储交易，每个区块不超过1M，平均能存储2500个交易记录

5.2 谁创建区块能获得50个币，大约10分钟1个，每出现210000个区块（约四年）减半，到2040年挖完，总数约2100万个，以后只得到矿工费

5.3 每个区块会链接到前一个区块，一直到创始区块（无前一个）

### 6 挖矿

6.1 对现有的临时交易校验通过后，对（现有交易数据 + 历史区块值）找到某个数N通过哈希算法计算后小于某个大数S，则可以创建区块，此为挖矿

6.2 创建区块后可以获得5.2中的币，以及4.5中的矿工费

6.3 创建后公诸于众，其他人下载该区块数据，根据4.2校验交易有效，根据6.1计算区块有效

6.4 大约10分钟一个区块，其他人拿到新的区块后马上根据（新的区块值和历史区块值 + 最近10分钟交易数据）继续找某个数N通过哈希函数计算后小于某个大数S

6.5 每2160个区块计算一次S，理论上是15天才能挖完，如果小于15天，则将S的值变小提高难度

6.6 大数S为256位，如果前面32位为0，根据3.6的随机，平均需要尝试2的32次方（约42亿次）次才能得到这种数，6.5增加难度33位为0，则需要尝试2的33次方（约84亿次）

6.7 根据5.1和6.4，理论上每笔交易最小大小为225字节，如果都是最小的，加上头尾则10分钟为4300笔交易，大约每秒7.3笔，实际值会低于这个数值

### 7 分布式

7.1 根据6.2，才会有更多的人加入挖矿行为，为了获取新区块的币和矿工费，同时有动力保存历史区块信息

7.2 每个节点会存储已知的节点，并在别人询问是将自己知道的节点公布出去，新节点只要连接任意一个节点，就能获取它知道的节点，再次询问其他节点可以增加已知节点

7.3 根据5.1和7.1，区块大小会无限制增长（目前约150G），同步和计算延迟很大，某些节点存储全部信息，挖矿节点只需要下载区块地址摘要信息即可挖矿

7.4 根据7.3数据太大，计算用户有多少币需要查询所有区块所有交易，为此维护一张未花费交易表，每个交易用户交易时候到这个表里查询用户是否有这么多钱，交易确认后更新这个表（删除用户花费掉的交易，加入用户零钱和交易对象的币）

### 8 安全

8.1 空区块作弊，不要矿工费，节约校验交易时间提前一点点时间挖，有一定概率先挖到

8.2 两个节点同时挖到，各自扩散到周围的节点，新的节点根据他所获取的再挖，最终谁长谁胜出，短的再扩散时会被其他长节点拒绝，每个节点知道长的会胜出，会在他所知道的最长的节点基础上挖 

8.3 A发起一笔交易100给B，已经挖到新的区块确认之后，B说已经收到并发货。A假装不知道新的区块又把这100给C，以此算新的区块，如果A控制了全网算力的51%，则有可能在给C的交易上挖并且长度有超过B的可能行，前提A控制了全网算力的51%

### 附录

- [RSA公钥私钥加密算法](rsa.md)
- [哈希算法与MD5、SHA](hash.md)
- [拜占庭将军FBFT和Pow公示](hash.md)