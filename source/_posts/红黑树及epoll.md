---
title: 红黑树及epoll
date: 2020-03-16 10:16:57
tags:"红黑树","epoll"
---

推荐一个在线数据结构算法生成网站： https://www.cs.usfca.edu/~galles/visualization/Algorithms.html 

参考博客： https://blog.csdn.net/daaikuaichuan/article/details/83862311 

红黑树时一种还有红黑节点并能自平衡的二叉查找树，满足以下性质：

- 每个节点要么是红色要么是黑色
- 根节点是黑色
- 叶子节点是黑色
- 每个红色节点的两个子节点一定都是黑色
- 任意节点到每个叶子结点的路径包含数量相同的黑节点
  - 如果一节点存在黑色子节点，那么此节点必然有两个子节点

红黑树可以通过左旋，右旋在插入删除的时候实现自平衡。具体操作在博客 https://www.jianshu.com/p/e136ec79235c ，最麻烦的是红黑树的删除操作。红黑树查找最坏情况下$O(log_2N)$。

而epoll底层就是红黑树。所以相比于select和poll线性查找快很多。 它在Linux内核中申请了一个简易的文件系统，把原先的一个select或poll调用分成了3部分： 

epoll在sys/epoll.h里面，有三个典型函数

1. epoll_create函数创立一个epoll对象
2. 调用epoll_ctl向epoll对象中添加套接字； 
3.  调用epoll_wait收集发生事件的连接。 

epoll 结构体是

```c++
struct eventpoll {
　　...
　　/*红黑树的根节点，这棵树中存储着所有添加到epoll中的事件，
　　也就是这个epoll监控的事件*/
　　struct rb_root rbr;
　　/*双向链表rdllist保存着将要通过epoll_wait返回给用户的、满足条件的事件*/
　　struct list_head rdllist;
　　...
};

```

epoll_create内核帮助我们建立size个节点，在内核cache里建立红黑树用于存储以后epoll_ctl传来的socket,还会在建立一个双向链表。用于存储准备就绪的事件。调用epoll_wait,观察这个rdlist里面有无数据就可以。有数据返回，无数据就sleep。等待超时。

	epoll里面有两种触发模式，LT和ET
	- LT是水平触发只要这个文件描述符还有数据可读，每次 epoll_wait都会返回它的事件，提醒用户程序去操作。
	- ET是边缘触发，在它检测到有 I/O 事件时，通过 epoll_wait 调用会得到有事件通知的文件描述符，对于每一个被通知的文件描述符，如可读，则必须将该文件描述符一直读到空，让 errno 返回 EAGAIN 为止，否则下次的 epoll_wait 不会返回余下的数据，会丢掉事件。如果ET模式不是非阻塞的，那这个一直读或一直写势必会在最后一次阻塞
如果采用EPOLLLT模式的话，系统中一旦有大量你不需要读写的就绪文件描述符，它们每次调用epoll_wait都会返回，这样会大大降低处理程序检索自己关心的就绪文件描述符的效率.的ET模式是如果数据没有全部写完，下次调用的时候不会通知，只会通知一次。直到该文件描述符出现第二次可读写时间。





