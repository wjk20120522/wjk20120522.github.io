title: 深入理解EventBus
date: 2015-07-12 09:00:10
tags: Android
---

由于目前的项目中用到了EventBus，想深入了解下，在网上查找了相关的资料，结合了自己的一点理解，整理了一下。如有错误，还请指出。

#### 概述
1. EventBus的项目主页[https://github.com/greenrobot/EventBus](https://github.com/greenrobot/EventBus)
2. EventBus是一款针对Android优化的发布/订阅事件总线，采用了观察者模式。主要功能是替代Intent，Handler，Broadcast在Fragment，Activity，Service，线程之间传递消息。优点是开销小，代码更优雅，发送者和接受者**解耦**。
3. 整体的[框架图](https://github.com/greenrobot/EventBus/blob/master/EventBus-Publish-Subscribe.png)

-----
#### 如何使用
1. Android Studio中添加依赖，在自己的Module下的build.gradle文件中添加依赖：
```
compile 'de.greenrobot:eventbus:2.4.0'
```
2. 定义基本的Events。观察者可以订阅某些Event，当发布者发布了对应的Event时，系统变会通知订阅了这个Event的观察者，以便执行相应的动作。Event没有特别的继承要求，如：
``` Java
public class MessageEvent {
	public final String message;

	public MessageEvent(String message) {
		this.message = message;
	}
}
```
3. 创建订阅者。订阅者需要实现对应的处理函数onEvent来处理接受到的不同类型的Event。当然，前提是他们在EventBus订阅过，当不需要订阅时，记得要取消订阅。观察者订阅的例子如：
``` Java
@Override
public void onStart() {
    super.onStart();
    EventBus.getDefault().register(this);
}

@Override
protected void onStop() {
    EventBus.getDefault().unregister(this);
    super.onStop();
}

// This methd will be called when a MessageEvent is posted
public void onEvent(MessageEvent event) {
    Toast.makeText(this, event.message, Toast.LENGTH_LONG).show();
}

// This method will be called when a SomeOtherEvent is posted
public void onEvent(SomeOtherEvent event) {
    doSomethingWith(event);
}
```
4. 事件发布者。在代码的任何部分都可以post一个Event。所以订阅了这个类型事件的订阅者都可以接收到。
``` Java
EventBus.getDefault().post(new MessageEvent("hello"));
```
[Read More:](https://github.com/greenrobot/EventBus/blob/master/HOWTO.md)
大致的使用方法就是这样，但是处理事件究竟在哪个线程执行呢？UI线程还是新的子线程？EventBus提供了四种不同的处理函数，这四个函数指定了该函数执行时需要在的线程：
* onEvent. 事件在哪个线程post出来的，onEvent处理函数便会在这个线程中运行
* onEventMainThread. 不论是在哪个线程中post出Event，处理函数都会在UI线程中执行。
* onEventBackgroundThread. 如果事件是UI发布出来的，则在子线程中执行，如果事件在子线程中发布出来的，则直接在该子线程中执行
* onEventAsyncThread. 处理函数只在新创建的子线程中执行。

-----
#### 源码分析
我们针对上面的使用过程结合源码看看EventBus究竟是如何工作的：
1. EventBus入口
```
public void register(Object subscriber) {
    register(subscriber, false, 0);
}
```
入口函数又调用了重载函数，它的三个参数分别代表：
* subscriber: 订阅者
* sticky: false, 是否有粘性，默认是false. 调用registerSticky函数可以设置。sticky事件不同之处在于，当事件发布后，再有订阅者开始订阅该类型事件，依然能收到该类型事件最近一个Sticky事件。
* priority: 事件的优先级，默认是0. 高优先级的订阅者可以取消事件继续向低优先级的订阅者分发。(有点像Android中的有序广播)
PS：旧版本的EventBus还有一个DEFAULT_METHOD_NAME参数(onEvent)，允许自定义事件响应函数名称，新版已经去除。

2. 真正调用的register
```
private synchronized void register(Object subscriber, boolean sticky, int priority) {
    List<SubscriberMethod> subscriberMethods = subscriberMethodFinder.findSubscriberMethods(subscriber.getClass());
    for (SubscriberMethod subscriberMethod : subscriberMethods) {
        subscribe(subscriber, subscriberMethod, sticky, priority);
    }
}
```
通过subscriberMethodFinder方法找到一个订阅者的所有订阅方法。具体的查找过程如下：

```
List<SubscriberMethod> findSubscriberMethods(Class<?> subscriberClass) {
    String key = subscriberClass.getName();
    List<SubscriberMethod> subscriberMethods;
    synchronized (methodCache) {
    	// 判断是否有缓存，有缓存就直接返回缓存的内容
        subscriberMethods = methodCache.get(key);
    }
    if (subscriberMethods != null) {
        return subscriberMethods;
    }
    subscriberMethods = new ArrayList<SubscriberMethod>();
    Class<?> clazz = subscriberClass;
    HashSet<String> eventTypesFound = new HashSet<String>();
    StringBuilder methodKeyBuilder = new StringBuilder();
    while (clazz != null) {
        String name = clazz.getName();
        if (name.startsWith("java.") || name.startsWith("javax.") || name.startsWith("android.")) {
            // Skip system classes, this just degrades performance
            break;
        }

        // Starting with EventBus 2.2 we enforced methods to be public (might change with annotations again)
        // 通过反射，获取到订阅者的所有方法
        Method[] methods = clazz.getDeclaredMethods();
        for (Method method : methods) {
            String methodName = method.getName();
            if (methodName.startsWith(ON_EVENT_METHOD_NAME)) {
                int modifiers = method.getModifiers();
                if ((modifiers & Modifier.PUBLIC) != 0 && (modifiers & MODIFIERS_IGNORE) == 0) {
                    Class<?>[] parameterTypes = method.getParameterTypes();
                    // 订阅函数参数只能为1个
                    if (parameterTypes.length == 1) {
                        String modifierString = methodName.substring(ON_EVENT_METHOD_NAME.length());
                        ThreadMode threadMode;
                        // 根据订阅函数的名称设置订阅函数的threadMode
                        if (modifierString.length() == 0) {
                            threadMode = ThreadMode.PostThread;
                        } else if (modifierString.equals("MainThread")) {
                            threadMode = ThreadMode.MainThread;
                        } else if (modifierString.equals("BackgroundThread")) {
                            threadMode = ThreadMode.BackgroundThread;
                        } else if (modifierString.equals("Async")) {
                            threadMode = ThreadMode.Async;
                        } else {
                            if (skipMethodVerificationForClasses.containsKey(clazz)) {
                                continue;
                            } else {
                                throw new EventBusException("Illegal onEvent method, check for typos: " + method);
                            }
                        }
                        Class<?> eventType = parameterTypes[0];
                        methodKeyBuilder.setLength(0);
                        methodKeyBuilder.append(methodName);
                        methodKeyBuilder.append('>').append(eventType.getName());
                        String methodKey = methodKeyBuilder.toString();
                        if (eventTypesFound.add(methodKey)) {
                            // Only add if not already found in a sub class
                            // 一个订阅方法对象包含Method对象，threadMode对象，eventType对象
                            subscriberMethods.add(new SubscriberMethod(method, threadMode, eventType));
                        }
                    }
                } else if (!skipMethodVerificationForClasses.containsKey(clazz)) {
                    Log.d(EventBus.TAG, "Skipping method (not public, static or abstract): " + clazz + "."
                            + methodName);
                }
            }
        }
        // 遍历父类的订阅函数
        clazz = clazz.getSuperclass();
    }
    if (subscriberMethods.isEmpty()) {
        throw new EventBusException("Subscriber " + subscriberClass + " has no public methods called "
                + ON_EVENT_METHOD_NAME);
    } else {
    	// 将该订阅者的所有订阅方法放入缓存，第二次直接从缓存取
        synchronized (methodCache) {
            methodCache.put(key, subscriberMethods);
        }
        return subscriberMethods;
    }
}
```
回到register方法，在获得了一个订阅者的所有订阅方法后，对于每一个订阅方法，调用subscribe方法。具体的调用过程为：

``` Java
// Must be called in synchronized block
private void subscribe(Object subscriber, SubscriberMethod subscriberMethod, boolean sticky, int priority) {
    Class<?> eventType = subscriberMethod.eventType;
    // 通过订阅事件类型，查找所有的订阅Subscription,每个Subscription包含订阅者，订阅方法
    CopyOnWriteArrayList<Subscription> subscriptions = subscriptionsByEventType.get(eventType);
    Subscription newSubscription = new Subscription(subscriber, subscriberMethod, priority);
    if (subscriptions == null) {
    	// 该事件目前没有订阅列表，新建一个
        subscriptions = new CopyOnWriteArrayList<Subscription>();
        subscriptionsByEventType.put(eventType, subscriptions);
    } else {
        if (subscriptions.contains(newSubscription)) {
            throw new EventBusException("Subscriber " + subscriber.getClass() + " already registered to event "
                    + eventType);
        }
    }

    // Starting with EventBus 2.2 we enforced methods to be public (might change with annotations again)
    // subscriberMethod.method.setAccessible(true);

    int size = subscriptions.size();
    for (int i = 0; i <= size; i++) {
    	// 将订阅按照优先级插入到订阅列表中
        if (i == size || newSubscription.priority > subscriptions.get(i).priority) {
            subscriptions.add(i, newSubscription);
            break;
        }
    }
    // 将订阅事件添加到订阅事件列表中
    List<Class<?>> subscribedEvents = typesBySubscriber.get(subscriber);
    if (subscribedEvents == null) {
        subscribedEvents = new ArrayList<Class<?>>();
        typesBySubscriber.put(subscriber, subscribedEvents);
    }
    subscribedEvents.add(eventType);

    if (sticky) {
        Object stickyEvent;
        synchronized (stickyEvents) {
            stickyEvent = stickyEvents.get(eventType);
        }
        if (stickyEvent != null) {
            // If the subscriber is trying to abort the event, it will fail (event is not tracked in posting state)
            // --> Strange corner case, which we don't take care of here.
            postToSubscription(newSubscription, stickyEvent, Looper.getMainLooper() == Looper.myLooper());
        }
    }
}
```
这个函数中有两个数据结构需要关注：
* subscriptionsByEventType. 根据事件类型得到所有的订阅信息，每个订阅包含订阅者，订阅函数以及优先级。
* typesBySubscriber. 根据订阅者查找事件类型
所以，任何一个订阅者，我们可以得到它的订阅事件类型列表。通过这个订阅事件类型，可以找到所有的订阅信息。这样在post的时候便可以针对这些所有的订阅信息，去依次执行对应的订阅函数。

最后便是post函数了。
```
/** Posts the given event to the event bus. */
public void post(Object event) {
    PostingThreadState postingState = currentPostingThreadState.get();
    List<Object> eventQueue = postingState.eventQueue;
    eventQueue.add(event);

    if (!postingState.isPosting) {
        postingState.isMainThread = Looper.getMainLooper() == Looper.myLooper();
        postingState.isPosting = true;
        if (postingState.canceled) {
            throw new EventBusException("Internal error. Abort state was not reset");
        }
        try {
            while (!eventQueue.isEmpty()) {
            	// 分发事件
                postSingleEvent(eventQueue.remove(0), postingState);
            }
        } finally {
            postingState.isPosting = false;
            postingState.isMainThread = false;
        }
    }
}
```
我们来查看具体的分发事件的逻辑：
```
private void postSingleEvent(Object event, PostingThreadState postingState) throws Error {
    Class<?> eventClass = event.getClass();
    boolean subscriptionFound = false;
    if (eventInheritance) {
    	// 查找eventClass以及父类和接口所对应动所有事件类型
        List<Class<?>> eventTypes = lookupAllEventTypes(eventClass);
        int countTypes = eventTypes.size();
        for (int h = 0; h < countTypes; h++) {
            Class<?> clazz = eventTypes.get(h);
            subscriptionFound |= postSingleEventForEventType(event, postingState, clazz);
        }
    } else {
        subscriptionFound = postSingleEventForEventType(event, postingState, eventClass);
    }
    // 如果没有订阅发现，那么会Post一个NoSubsribeEvent事件
    if (!subscriptionFound) {
        if (logNoSubscriberMessages) {
            Log.d(TAG, "No subscribers registered for event " + eventClass);
        }
        if (sendNoSubscriberEvent && eventClass != NoSubscriberEvent.class &&
                eventClass != SubscriberExceptionEvent.class) {
            post(new NoSubscriberEvent(this, event));
        }
    }
}
```
postSingleEventForEventType将每个事件类型post出去，的代码如下：
```
private boolean postSingleEventForEventType(Object event, PostingThreadState postingState, Class<?> eventClass) {
    CopyOnWriteArrayList<Subscription> subscriptions;
    synchronized (this) {
    	// 根据订阅事件查找对应的订阅(参看之前的两个数据结构)
        subscriptions = subscriptionsByEventType.get(eventClass);
    }
    if (subscriptions != null && !subscriptions.isEmpty()) {
        for (Subscription subscription : subscriptions) {
            postingState.event = event;
            postingState.subscription = subscription;
            boolean aborted = false;
            try {
            	// 对每个订阅调用此方法，postingState.isMainThread表示当前线程是否是主线程
                postToSubscription(subscription, event, postingState.isMainThread);
                aborted = postingState.canceled;
            } finally {
                postingState.event = null;
                postingState.subscription = null;
                postingState.canceled = false;
            }
            // 如果某个高优先级的订阅者选择取消进一步传播，则aborted为true
            if (aborted) {
                break;
            }
        }
        return true;
    }
    return false;
}
```
postToSubscription便是具体的某个订阅需要执行的函数了。算是一个**核心**方法吧。
```
private void postToSubscription(Subscription subscription, Object event, boolean isMainThread {
    switch (subscription.subscriberMethod.threadMode) {
        case PostThread:
        	// 直接当前线程调用此订阅函数
            invokeSubscriber(subscription, event);
            break;
        case MainThread:
            if (isMainThread) {
            	// 如果当前线程是主线程，则直接在当前线程调用
                invokeSubscriber(subscription, event);
            } else {
            	// 否则通过Handler将事件放入主线程队列中执行
                mainThreadPoster.enqueue(subscription, event);
            }
            break;
        case BackgroundThread:
            if (isMainThread) {
            	// 如果是主线程，创建一个runnable丢入线程池中
                backgroundPoster.enqueue(subscription, event);
            } else {
            	// 在当前线程执行
                invokeSubscriber(subscription, event);
            }
            break;
        case Async:
        	// 在子线程中执行
            asyncPoster.enqueue(subscription, event);
            break;
        default:
            throw new IllegalStateException("Unknown thread mode: " + subscription.subscriberMethod.threadMode);
    }
}
```
mainThreadPoster，backgroundPoster， asyncPoster代码分析就不具体分析了。具体的可以参考：[快速Android开发系列通信篇之EventBus](http://www.cnblogs.com/angeldevil/p/3715934.html)
invokeSubscriber函数直接在当前线程执行.
```
void invokeSubscriber(Subscription subscription, Object event) {
    try {
    	// 通过反射，调用subscription.subscriber的method，参数为event
        subscription.subscriberMethod.method.invoke(subscription.subscriber, event);
    } catch (InvocationTargetException e) {
        handleSubscriberException(subscription, event, e.getCause());
    } catch (IllegalAccessException e) {
        throw new IllegalStateException("Unexpected exception", e);
    }
}
```
总结一下post的流程，从currentPostingThreadState得到当前线程的Post信息PostingThreadState，其中包含事件队列。将当前事件添加到线程的事件队列中。循环事件队列中每个事件，如果事件可以继承，获得父类及接口的事件。对每个事件，查找其所有订阅者。循环每个订阅者，根据ThreadMode，在不同的线程中调用订阅者的事件响应函数。   

哈呼......


----
#### 参考文献
1. [https://github.com/greenrobot/EventBus](https://github.com/greenrobot/EventBus)
2. [http://blog.csdn.net/yuanzeyao/article/details/38174537](http://blog.csdn.net/yuanzeyao/article/details/38174537)
3. [http://www.codekk.com/open-source-project-analysis/detail/Android/Trinea/EventBus%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90](http://www.codekk.com/open-source-project-analysis/detail/Android/Trinea/EventBus%20%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90)
4. [快速Android开发系列通信篇之EventBus](http://www.cnblogs.com/angeldevil/p/3715934.html)
