### Redis是如何进行过期处理的

---
1. 定时删除(主动删除)<br>&nbsp;&nbsp;&nbsp;&nbsp;在设置键的过期时间的时候创建一个定时器，当过期时间到的时候立马执行删除操作。不过这种处理方式是即时的，不管这个时间内有多少过期键，不管服务器现在的运行状况，都会立马执行，所以对CPU不是很友好。
2. 惰性删除(被动删除)<br>&nbsp;&nbsp;&nbsp;&nbsp;惰性删除策略不会在键过期的时候立马删除，而是当外部指令获取这个键的时候才会主动删除。处理过程为：接收get执行、判断是否过期（这里按过期判断）、执行删除操作、返回nil（空）。
3. 定期删除(主动删除)<br>&nbsp;&nbsp;&nbsp;&nbsp;定期删除是设置一个时间间隔，每个时间段都会检测是否有过期键，如果有执行删除操作。这个概念应该很好理解。通过TTL命令用于获取键到期的剩余时间(秒)。 判断大于0还是其他，这样可以实现定期删除。

---

#### 分析

- 1 是实时执行的，对CPU不是很友好，但是这在最大程度上释放了内存，所以这种方式算是一种<font color = #FF2941>内存优先优化策略</font>。
- 2、3为被动删除，所以过期键应该会存在一定的时间，这样就使得过期键不会被立马删除，仍然占用着内存。但是惰性删除的时候一般是单个删除，相对来说<font color = #FF2941>对CPU是友好的</font>。 
- 定期删除设定时间过于频繁可能会演变成为定时删除，执行过少则可能会导致内存占用过大