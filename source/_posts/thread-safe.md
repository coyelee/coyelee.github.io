---
title: thread-safe
date: 2019-12-19 16:36:07
tags:
categories:
- iOS
---

###	线程安全一个小case

> 代码示例

```
- (void)moreThreadSafe {
    pthread_mutex_init(&_mutex, NULL);
    self.threadStr = @"thread safe after";
    [self performSelector:@selector(doSomething) withObject:nil afterDelay:0.5];

    /*
     * 示例代码一
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{
        NSLog(@"before sleep %@",self.threadStr);
        sleep(3);
        NSLog(@"after sleep %@",self.threadStr);
    });*/
    
    
     // 示例代码二
     __weak typeof(self.threadStr)weakString = self.threadStr;
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{
        __strong __typeof(weakString) strongString = weakString;
        NSLog(@"before sleep %@",strongString);
        sleep(3);
        NSLog(@"after sleep %@",strongString);
    });
    
    /*
     * 示例代码三
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{
        pthread_mutex_lock(&self->_mutex);
        NSLog(@"before sleep %@",self.threadStr);
        sleep(3);
        NSLog(@"after sleep %@",self.threadStr);
        pthread_mutex_unlock(&self->_mutex);
    });*/
}

- (void)doSomething {
    pthread_mutex_lock(&_mutex);
    self.threadStr = @"thread safe  changed";
    pthread_mutex_unlock(&_mutex);
}

```


> 说明
	
为了保证异步子线程对资源（例如示例代码中的 self.threadStr ）的独占，防止资源竞争，需要保证子线程的不被改变，因此类似示例一代码的写法，就不能保证这个问题，示例二代码通过改变数据储存指针的方式保证了不被更改，同时也保证了野指针的问题，示例三通过加锁的方式也保证了这种问题的出现，但加入此资源使用地方较多，不建议这种方式。