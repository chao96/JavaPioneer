# Zookeeper基于Curator实现分布式锁

## 一、算法流程

1.每个客户端创建临时有序节点。

2.客户端获取节点列表，客户端获取节点列表，判断自己是否列表中的第一个节点，如果是就获得锁，如果不是就监听自己前面的节点，等待前面节点被删除。

3.获取到锁就进行正常的业务流程，执行完释放锁。

**注意：**

> 只监听前一个节点的原理：若监听所有子节点，则任意一个节点状态改变，则所有子节点都会收到通知（羊群效应）。实际上只需要它的后一个节点接收到通知。

## 二、Zookeeper 实现分布式锁示意图

![](F:\learn\docs\zookeeper\2分布式锁示意图.png)

## 二、代码实现

采用Curator的`acquire`和`release`两个方法就可以方便的实现分布式锁

```java
public class CuratorLock {

    public static void main(String[] args) throws Exception {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        CuratorFramework client = CuratorFrameworkFactory.newClient("127.0.0.1:2181",retryPolicy);
        client.start();
        CuratorFramework client2 = CuratorFrameworkFactory.newClient("127.0.0.1:2181",retryPolicy);
        client2.start();
        //创建分布式锁, 锁空间的根节点路径为/curator/lock
        InterProcessMutex mutex  = new InterProcessMutex(client,"/curator/lock");
        final InterProcessMutex mutex2  = new InterProcessMutex(client2,"/curator/lock");

        mutex.acquire();
        //获得了锁, 进行业务流程
        System.out.println("clent Enter mutex");

        Thread client2Th = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    mutex2.acquire();
                    System.out.println("client2 Enter mutex");
                    mutex2.release();
                    System.out.println("client2 release lock");

                }catch (Exception e){
                    e.printStackTrace();
                }

            }
        });
        client2Th.start();
        //完成业务流程, 释放锁
        Thread.sleep(5000);
        mutex.release();
        System.out.println("client release lock");
        client2Th.join();

        //关闭客户端
        client.close();
    }
}
```

**运行结果：**

![](F:\learn\docs\zookeeper\1控制台日志输出.png)