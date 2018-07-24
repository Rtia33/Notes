

# 【Android LruCache源码分析】
转自：[Android LruCache源码分析](https://www.jianshu.com/p/ef22974673ea)


### 一、LruCache概要

LRU (Least Recently Used) 即最近最少使用算法。在Android开发中，LruCache是基于LRU算法实现的。当缓存空间使用完的情况下，最久没被使用的对象会被清除出缓存。

LruCache常用的场景是做图片内存缓存，电商类APP经常会用到图片，当我们对图片资源做了内存缓存，不仅可以增强用户体验，而且可以减少图片网络请求，减少用户流量耗费。

LruCache是一个内存层面的缓存，如果想要进行本地磁盘缓存，推荐使用DiskLruCache，虽然没包含在官方API中，但是官方推荐我们使用。

### 二、使用方法

LruCache的使用方法如下：

```java
public class BitmapLruCache extends LruCache<String, Bitmap> {
    //设置缓存大小，建议当前应用可用最大内存的八分之一 即(int) (Runtime.getRuntime().maxMemory() / 1024 / 8)
    public BitmapLruCache(int size) {
        super(size);
    }

    //计算当前节点的内存大小 这个方法需要重写 不然返回1
    @Override
    protected int sizeOf(String key, Bitmap value) {
        return value.getByteCount() / 1024;
    }

    //当节点移除时该方法会回调，可根据需求来决定是否重写该方法
    @Override
    protected void entryRemoved(boolean evicted, String key, Bitmap oldValue, Bitmap
            newValue) {
        super.entryRemoved(evicted, key, oldValue, newValue);
    }
}

```

往缓存中放一张图片：

```java
mLruCache.put(name, bitmap);

```

获取一张图片：

```java
mLruCache.get(name);

```

删除一张图片：

```java
mLruCache.remove(name);

```

### 三、源码分析

1.  首先先看一下LruCache构造方法

```java
    public LruCache(int maxSize) {
        if (maxSize <= 0) {
            throw new IllegalArgumentException("maxSize <= 0");
        }
        this.maxSize = maxSize;
       //我们发现实现用的是LinkedHashMap   注意最后的true 表示LinkedHashMap 中accessOrder设置为true
        this.map = new LinkedHashMap<K, V>(0, 0.75f, true);
    }

```

2.  put方法

```java
  /**
     * Caches {@code value} for {@code key}. The value is moved to the head of
     * the queue.
     *
     * @return the previous value mapped by {@code key}.
     */
    public final V put(K key, V value) {
        if (key == null || value == null) {//不允许key 和 value 为 null
            throw new NullPointerException("key == null || value == null");
        }

        V previous;
        synchronized (this) {//多线程安全
            putCount++;
            //size 表示当期使用的缓存大小  safeSizeOf 会掉用sizeOf方法 用于计算当前节点的大小
            size += safeSizeOf(key, value);
            // 将新节点放入LinkedHashMap 如果有返回值，表示map集合中存在旧值
            previous = map.put(key, value);
            if (previous != null) {//存在旧值 在移除旧值后 更新缓存大小
                size -= safeSizeOf(key, previous);
            }
        }
        //有旧值移除 回调entryRemoved
        if (previous != null) {
            entryRemoved(false, key, previous, value);
        }

        trimToSize(maxSize);//整理每个节点 主要判断当前size是否超过maxSize 
        return previous;
    }

 public void trimToSize(int maxSize) {
        while (true) {
            K key;
            V value;
            synchronized (this) {
                if (size < 0 || (map.isEmpty() && size != 0)) {
                    throw new IllegalStateException(getClass().getName()
                            + ".sizeOf() is reporting inconsistent results!");
                }
               //size <= maxSize 停止遍历列表，不然继续遍历列表修剪节点。 
                if (size <= maxSize || map.isEmpty()) {
                    break;
                }

                Map.Entry<K, V> toEvict = map.entrySet().iterator().next();
                key = toEvict.getKey();
                value = toEvict.getValue();
                map.remove(key);
                size -= safeSizeOf(key, value);
                evictionCount++;
            }

            entryRemoved(true, key, value, null);
        }
    }

```

我们看下safeSizeOf 和 entryRemoved方法

```java
  private int safeSizeOf(K key, V value) {
        int result = sizeOf(key, value);//调用了sizeOf方法
        if (result < 0) {
            throw new IllegalStateException("Negative size: " + key + "=" + value);
        }
        return result;
    }

  protected int sizeOf(K key, V value) {//sizeOf的默认实现
        return 1;
    }

protected void entryRemoved(boolean evicted, K key, V oldValue, V newValue) {}//entryRemoved的默认实现

```

3.  get方法

```java
 /**
     * Returns the value for {@code key} if it exists in the cache or can be
     * created by {@code #create}. If a value was returned, it is moved to the
     * head of the queue. This returns null if a value is not cached and cannot
     * be created.
     */
    public final V get(K key) {
        if (key == null) {//key不能为 null
            throw new NullPointerException("key == null");
        }

        V mapValue;
        synchronized (this) {//线程同步
            mapValue = map.get(key);//调用LinkedHashMap 的get方法
            if (mapValue != null) {
                hitCount++;
                return mapValue;//找到返回
            }
            missCount++;
        }

        /*
         * Attempt to create a value. This may take a long time, and the map
         * may be different when create() returns. If a conflicting value was
         * added to the map while create() was working, we leave that value in
         * the map and release the created value.
         */

        V createdValue = create(key);//缓存没有情况下走创建流程
        if (createdValue == null) {
            return null;
        }

        synchronized (this) {
            createCount++;
            mapValue = map.put(key, createdValue);

            if (mapValue != null) {
                // There was a conflict so undo that last put
                map.put(key, mapValue);
            } else {
                size += safeSizeOf(key, createdValue);
            }
        }

        if (mapValue != null) {
            entryRemoved(false, key, createdValue, mapValue);
            return mapValue;
        } else {
            trimToSize(maxSize);
            return createdValue;
        }
    }

 protected V create(K key) {//创建方法默认空实现 可根据需求决定是否重写该方法
        return null;
    }

```

4.  remove方法

```java
/**
    * Removes the entry for {@code key} if it exists.
    *
    * @return the previous value mapped by {@code key}.
    */
   public final V remove(K key) {
       if (key == null) {
           throw new NullPointerException("key == null");
       }

       V previous;
       synchronized (this) {//线程同步
           previous = map.remove(key);//获得移除的节点
           if (previous != null) {//当节点不为空 更新缓存大小
               size -= safeSizeOf(key, previous);
           }
       }

       if (previous != null) {
          //当节点移除时回调entryRemoved方法
           entryRemoved(false, key, previous, null);
       }

       return previous;
   }

```

通过上述几个方法代码，我们知道LruCache如何控制及更新缓存的大小的，主要是在线程同步块里对size字段进行更新,然后根据size字段和maxSize字段的大小关系来修剪节点。但如何做到最近最少使用呢？ 没错，LinkedHashMap 帮我们做到最近最少使用的排序。

让我们看下LinkedHashMap 如何实现的，在此过程我们不分析HashMap的实现，只关心LinkedHashMap 的一些实现，HashMap的实现有机会给大家分享。

```java
//继承了HashMap 说明LinkedHashMap 的查找效率依然是O(1)
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V>
{
   //重新定义了节点 用于实现链表
    private transient LinkedHashMapEntry<K,V> header;

 private static class LinkedHashMapEntry<K,V> extends HashMapEntry<K,V> {
        LinkedHashMapEntry<K,V> before, after;
        //此处省略几个字
}
    //此处省略几个字
}

```

LinkedHashMap类并没有重写put方法，当我们调用put方法时，调用的依然是HashMap的put方法。我们看下HashMap的put方法：

```java
public V put(K key, V value) {
        if (table == EMPTY_TABLE) {
            inflateTable(threshold);
        }
        if (key == null)
            //HashMap的特点 可以放key为null的值
            return putForNullKey(value);
        int hash = sun.misc.Hashing.singleWordWangJenkinsHash(key);
        int i = indexFor(hash, table.length);
        for (HashMapEntry<K,V> e = table[i]; e != null; e = e.next) {
            Object k;
            if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                V oldValue = e.value;
                e.value = value;
                e.recordAccess(this);
                return oldValue;//存在旧值 就把旧值移除掉 并返回旧值  由此可知 当我们更换缓存中已存在的值时，并不会影响它在链表中位置
            }
        }

        modCount++;
        addEntry(hash, key, value, i);//LinkedHashMap 重写了该方法
        return null;
    }

 //LinkedHashMap中实现 
 /**
     * This override alters behavior of superclass put method. It causes newly
     * allocated entry to get inserted at the end of the linked list and
     * removes the eldest entry if appropriate.
     */
//将节点放在链表的末尾
 void addEntry(int hash, K key, V value, int bucketIndex) {
        // Previous Android releases called removeEldestEntry() before actually
        // inserting a value but after increasing the size.
        // The RI is documented to call it afterwards.
        // **** THIS CHANGE WILL BE REVERTED IN A FUTURE ANDROID RELEASE ****

        // Remove eldest entry if instructed
        LinkedHashMapEntry<K,V> eldest = header.after;
        if (eldest != header) {
            boolean removeEldest;
            size++;
            try {
                removeEldest = removeEldestEntry(eldest);//hook  默认为false 让用户重写removeEldestEntry 来决定是否移除eldest节点
            } finally {
                size--;
            }
            if (removeEldest) {
                removeEntryForKey(eldest.key);
            }
        }

        super.addEntry(hash, key, value, bucketIndex);//往下瞅 有惊喜
    }

  protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return false;
    }

   //HashMap中addEntry：
   void addEntry(int hash, K key, V value, int bucketIndex) {
        if ((size >= threshold) && (null != table[bucketIndex])) {
            resize(2 * table.length);
            hash = (null != key) ? sun.misc.Hashing.singleWordWangJenkinsHash(key) : 0;
            bucketIndex = indexFor(hash, table.length);
        }
         //LinkedHashMap重写了该方法
        createEntry(hash, key, value, bucketIndex);
    }

    //LinkedHashMap中
    void createEntry(int hash, K key, V value, int bucketIndex) {
        HashMapEntry<K,V> old = table[bucketIndex];
        LinkedHashMapEntry<K,V> e = new LinkedHashMapEntry<>(hash, key, value, old);
        table[bucketIndex] = e;
       //把该节点插入到链表头部 最近使用访问到的在最前边原则
        e.addBefore(header); 
        size++;
    }

```

LinkedHashMap中重写了get方法，实现如下;

```java
    public V get(Object key) {
        //使用HashMap的getEntry方法，验证了我们所说的查询效率为O(1)
        LinkedHashMapEntry<K,V> e = (LinkedHashMapEntry<K,V>)getEntry(key);
        if (e == null)
            return null;
        e.recordAccess(this);//在找到节点后调用节点recordAccess方法 往下看
        return e.value;
    }

        //LinkedHashMapEntry
        void recordAccess(HashMap<K,V> m) {
            LinkedHashMap<K,V> lm = (LinkedHashMap<K,V>)m;
            if (lm.accessOrder) {//accessOrder该值默认为false 但是  在LruCache中设置为true
                lm.modCount++;
                remove();//该方法将本身节点移除链表
                addBefore(lm.header);//将自己添加到节点尾部 保证最近使用的节点位于链表末尾
            }
        }

```

LinkedHashMap的并没有重写HashMap的remove方法，依然是调用HashMap的remove方法，代码如下：

```java
    public V remove(Object key) {
        Entry<K,V> e = removeEntryForKey(key);
        return (e == null ? null : e.getValue());
    }

    final Entry<K,V> removeEntryForKey(Object key) {
        if (size == 0) {
            return null;
        }
        int hash = (key == null) ? 0 : sun.misc.Hashing.singleWordWangJenkinsHash(key);
        int i = indexFor(hash, table.length);
        HashMapEntry<K,V> prev = table[i];
        HashMapEntry<K,V> e = prev;

        while (e != null) {
            HashMapEntry<K,V> next = e.next;
            Object k;
            if (e.hash == hash &&
                ((k = e.key) == key || (key != null && key.equals(k)))) {
                modCount++;
                size--;
                if (prev == e)
                    table[i] = next;
                else
                    prev.next = next;
                //注意这一行 开头我们说过  LinkedHashMap用的节点是自己定义的LinkedHashMapEntry ，继承自HashMapEntry
                e.recordRemoval(this);
                return e;
            }
            prev = e;
            e = next;
        }

        return e;
    }

       //LinkedHashMapEntry 中recordRemoval
       void recordRemoval(HashMap<K,V> m) {
            remove();
        }
        private void remove() {//更改指针来移除自身
            before.after = after;
            after.before = before;
        }

```

### 三、总结

读完源码我们可以总结出：

1.  LruCache是继承自HashMap，它的查找效率依然是O(1);
2.  LruCache内部又维护了一个双向链表结构，当我们有访问操作时候，被访问节点会移到链表结尾；
3.  在Lrucache的put、get和remove方法中，对集合操作时使用了synchronized关键字，来保证线程安全；

### 四、Demo验证

1.首先创建LRUCache，我们设置其大小为4，因为默认每个Item大小为1，当我们执行放入操作时，最后放入的数据会在链表最后边，打印结果如下：

```java
    private void test() {
        LruCache<String,String> lruCache = new LruCache<>(4);
        lruCache.put("a","这是第1个放进去的值");
        lruCache.put("b","这是第2个放进去的值");
        lruCache.put("c","这是第3个放进去的值");
        lruCache.put("d","这是第4个放进去的值");
        print(lruCache);
    }

   private void print(LruCache<String,String> lruCache){
        Map map =lruCache.snapshot();
        Iterator<String> iterator = map.keySet().iterator();
        String key;
        while (iterator.hasNext()){
             key = iterator.next();
            Log.i("LruCacheItem","key ="+key+"   value ="+map.get(key));
        }
    }

```

测试截图如下：

![1.png](https://upload-images.jianshu.io/upload_images/9028834-370e401bf0e17620.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.当我们访问链表中一个元素时候，该元素会移到队列末尾，测试如下;

```java
    private void test() {
       LruCache<String,String> lruCache = new LruCache<>(4);
        lruCache.put("a","这是第1个放进去的值");
        lruCache.put("b","这是第2个放进去的值");
        lruCache.put("c","这是第3个放进去的值");
        lruCache.put("d","这是第4个放进去的值");
        lruCache.get("c");
        print(lruCache);
    }

```

测试截图如下：

![2.png](https://upload-images.jianshu.io/upload_images/9028834-97df9df52bafd26a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们发现第一个元素被移除。

我们会发现key为c的item移到了队列末尾。

3.当我们放入元素超过总的大小时，队列首部元素会被移除：

```java
    private void test() {
        LruCache<String,String> lruCache = new LruCache<>(4);
        lruCache.put("a","这是第1个放进去的值");
        lruCache.put("b","这是第2个放进去的值");
        lruCache.put("c","这是第3个放进去的值");
        lruCache.put("d","这是第4个放进去的值");
        lruCache.get("c");
        lruCache.put("e","这是第5个放进去的值");
        print(lruCache);

    }

```

测试截图如下：

![3.png](https://upload-images.jianshu.io/upload_images/9028834-78710376adf7537a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们发现超出总大小后，队列首部元素被移除。

4.移除相对简单，不会改变队列里元素的相对顺序，只是该元素出队列而已，测试如下：

```java
    private void test() {
        LruCache<String,String> lruCache = new LruCache<>(4);
        lruCache.put("a","这是第1个放进去的值");
        lruCache.put("b","这是第2个放进去的值");
        lruCache.put("c","这是第3个放进去的值");
        lruCache.put("d","这是第4个放进去的值");
        lruCache.get("c");
        lruCache.put("e","这是第5个放进去的值");
        lruCache.remove("c");
        print(lruCache);
    }

```

key为c的元素被移除链表，结果如下：
![4.png](https://upload-images.jianshu.io/upload_images/9028834-ca6a62196a4fa014.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)





