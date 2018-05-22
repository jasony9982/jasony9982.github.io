---  
layout: post  
title: atomic 和 nonatomic 的区别
categories: tech
---  
> 今天面试小伙伴的时候问到了 atomic 和 nonatomic 的区别，感觉回答的不太完善，这里是比较详细的回答

# 问题：下面三种写法有什么区别
```
@property(nonatomic, strong) UITextField *textField;
@property(atomic, strong) UITextField *textField;
@property(strong) UITextField *textField;
```


# 解答
1.  解释
    后两行是一样的，不写的话默认就是atomic。

    atomic 和 nonatomic 的区别在于，系统自动生成的 getter/setter 方法不一样。

    对于atomic的属性，系统生成的 getter/setter 会保证 get、set 操作的完整性，不受其他线程影响。比如，线程 A 的 getter 方法运行到一半，线程 B 调用了 setter：那么线程 A 的 getter 还是能得到一个完好无损的对象。

    而nonatomic就没有这个保证了。所以，nonatomic的速度要比atomic快。

    不过atomic可并不能保证线程安全。如果线程 A 调了 getter，与此同时线程 B 、线程 C 都调了 setter——那最后线程 A get 到的值，3种都有可能：可能是 B、C set 之前原始的值，也可能是 B set 的值，也可能是 C set 的值。同时，最终这个属性的值，可能是 B set 的值，也有可能是 C set 的值。

2. [苹果的官方文档](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Chapters/ocProperties.html) 有解释了，下面我们举例子解释一下背后的原理。

    ```
    //@property(atomic, strong) UITextField *textField;
    - (UITextField *) textField {
        return _textField;
    }

    - (void) setTextField:(UITextField *)textField {
        [textField retain];
        [_textField release];
        _textField = textField;
    }

    ```
    ```
    //@property(atomic, strong) UITextField *textField;
    //系统生成的代码如下：

    - (UITextField *) textField {
        UITextField *retval = nil;
        @synchronized(self) {
            retval = [[_textField retain] autorelease];
        }
        return retval;
    }

    - (void) setTextField:(UITextField *)textField {
        @synchronized(self) {
        [_textField release];
        _textField = [textField retain];
        }
    }
    ```

    简单来说，就是 atomic 会加一个锁来保障线程安全，并且引用计数会 +1，来向调用者保证这个对象会一直存在。假如不这样做，如有另一个线程调 setter，可能会出现线程竞态，导致引用计数降到0，原来那个对象就释放掉了。

3. 《realm》对atomic和nonatomic的解释

    atomic (default)

    Atomic is the default: if you don’t type anything, your property is atomic. An atomic property is guaranteed that if you try to read from it, you will get back a valid value. It does not make any guarantees about what that value might be, but you will get back good data, not just junk memory. What this allows you to do is if you have multiple threads or multiple processes pointing at a single variable, one thread can read and another thread can write. If they hit at the same time, the reader thread is guaranteed to get one of the two values: either before the change or after the change. What atomic does not give you is any sort of guarantee about which of those values you might get. Atomic is really commonly confused with being thread-safe, and that is not correct. You need to guarantee your thread safety other ways. However, atomic will guarantee that if you try to read, you get back some kind of value.

    nonatomic

    On the flip side, non-atomic, as you can probably guess, just means, “don’t do that atomic stuff.” What you lose is that guarantee that you always get back something. If you try to read in the middle of a write, you could get back garbage data. But, on the other hand, you go a little bit faster. Because atomic properties have to do some magic to guarantee that you will get back a value, they are a bit slower. If it is a property that you are accessing a lot, you may want to drop down to nonatomic to make sure that you are not incurring that speed penalty.

    其中Atomic is really commonly confused with being thread-safe, and that is not correct. You need to guarantee your thread safety other ways.这句话明确说明Atomic不能保证对象多线程的安全。所以Atomic 不能保证对象多线程的安全。它只是能保证你访问的时候给你返回一个完好无损的Value而已。举个例子：

    如果线程 A 调了 getter，与此同时线程 B 、线程 C 都调了 setter——那最后线程 A get 到的值，有3种可能：可能是 B、C set 之前原始的值，也可能是 B set 的值，也可能是 C set 的值。同时，最终这个属性的值，可能是 B set 的值，也有可能是 C set 的值。所以atomic可并不能保证对象的线程安全。

# atomic和nonatomic的对比

1. atomic和nonatomic用来决定编译器生成的getter和setter是否为原子操作。

2. atomic：系统生成的 getter/setter 会保证 get、set 操作的完整性，不受其他线程影响。getter 还是能得到一个完好无损的对象（可以保证数据的完整性），但这个对象在多线程的情况下是不能确定的，比如上面的例子。

    也就是说：如果有多个线程同时调用setter的话，不会出现某一个线程执行完setter全部语句之前，另一个线程开始执行setter情况，相当于函数头尾加了锁一样，每次只能有一个线程调用对象的setter方法，所以可以保证数据的完整性。

    atomic所说的线程安全只是保证了getter和setter存取方法的线程安全，并不能保证整个对象是线程安全的。

3. nonatomic：就没有这个保证了，nonatomic返回你的对象可能就不是完整的value。因此，在多线程的环境下原子操作是非常必要的，否则有可能会引起错误的结果。但仅仅使用atomic并不会使得对象线程安全，我们还要为对象线程添加lock来确保线程的安全。

4. nonatomic的速度要比atomic的快。

5. atomic与nonatomic的本质区别其实也就是在setter方法上的操作不同

