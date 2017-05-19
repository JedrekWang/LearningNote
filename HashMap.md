# HashMap小结

## HashMap的简介
    HashMap提供了常数级别的数据get和put操作，但是遍历顺序不保证有序
    HashMap实现了Map接口，继承于AbstractMap
    HashMap允许null的键和null的值
    HashMap不保证线程安全，需要线程安全可以使用HashTable,Collections.synchronizedMap()和ConcurrentHashMap(推荐)
    HashMap是用链表数组实现的，Java 8在此基础上加入了红黑树进行了优化

## HashMap的源码分析
````
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
````
HashMap的初始大小为16,1<<4也很好的说明了容量必须为2^n.
````
static final int MAXIMUM_CAPACITY = 1 << 30;
````
HashMap的最大容量为2^30.
````
static final float DEFAULT_LOAD_FACTOR = 0.75f;
````
初始的装载因子为0.75
````
static final int TREEIFY_THRESHOLD = 8;
````
当一个桶的节点超过8时，会将链表优化为红黑树
````
transient Node<K,V>[] table;
````
这个就是HashMap的链表数组的结构,每一个Node对应着一个桶(bucket)
````
transient int modCount;
````
这个数是来来实现HashMap的fail-fast机制，即当一个线程在迭代HashMap时，如果另外一个线程
进行put和remove操作时，会出现ConcurrentModificationException

### HashMap的结点类
````
static class Node<K,V> implements Map.Entry<K,V> {   //结点的类
        final int hash;                                  //散列值
        final K key;
        V value;
        Node<K,V> next;                 //指向下一个元素的结点

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
//略...........
}
````
这个Node实现了Entry结构,本质上就是一个键值对,hash值对应着相应的桶号,next则是指向下一个元素的指针

### HashMap的hash策略
首先，HashMap的长度必须为2^n,目的是在求hash值和resize()方法中做一些优化
````
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
````
这是HashMap的hash方法，可以看出先求出key的hashCode的值，然后与该值的高16位与低16位进行亦或操作，最后
用(h&(length-1))进行操作
原因是：进行取模运算的消耗比较大，而位运算的消耗较小,通过h&(length-1)在length一直为2^n的基础上等价于取模,
但效率更高

### HashMap的resize策略
resize即重新改变长度,当底层的数组无法容纳更多的元素时，就会进行此操作。HashMap的初始装载因子为0.75,即当元素
超过75%时resize，这个值很好的权衡了时间和空间的消耗，装载因子过小会导致额外的空间消耗，而过大会频繁碰撞，导致
时间上的消耗
````
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        int oldThr = threshold;
        int newCap, newThr = 0;
        if (oldCap > 0) {
            if (oldCap >= MAXIMUM_CAPACITY) {  //超过HashMap允许的最大值，不进行resize操作
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold 在原来的基础上加倍
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults 初始容量大小为0则设为默认的16
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
````
