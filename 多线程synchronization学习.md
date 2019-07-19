1.s*ynchronization* 关键字是对对象上锁

2.如果一个对象有多个s*ynchronization* 方法，当一个线程进入到s*ynchronization* 方法，那么在这个方法执行完之前，其他方法不能访问该对象的任何s*ynchronization* 方法。

3.s*ynchronization* 代码块是细粒度的同步锁，代码块要同步的之后代码块内的内容。其他依然可以并发执行。

4.对静态方法上的s*ynchronization* 同步，是将锁加在了这个类对象上。