## 确保对象的唯一性——单例模式 （四）  

5 一种更好的单例实现方法  

饿汉式单例类不能实现延迟加载，不管将来用不用始终占据内存；懒汉式单例类线程安全控制烦琐，而且性能受影响。可见，无论是饿汉式单例还是懒汉式单例都存在这样那样的问题，有没有一种方法，能够将两种单例的缺点都克服，而将两者的优点合二为一呢？答案是：Yes！下面我们来学习这种更好的被称之为 Initialization Demand Holder （IoDH）的技术。  

在 IoDH 中，我们在单例类中增加一个静态（static）内部类，在该内部类中创建单例对象，再将该单例对象通过 getInstance() 方法返回给外部使用，实现代码如下所示：

```
//Initialization on Demand Holder
class Singleton {
	private Singleton() {
	}
	
	private static class HolderClass {
            private final static Singleton instance = new Singleton();
	}
	
	public static Singleton getInstance() {
	    return HolderClass.instance;
	}
	
	public static void main(String args[]) {
	    Singleton s1, s2; 
            s1 = Singleton.getInstance();
	    s2 = Singleton.getInstance();
	    System.out.println(s1==s2);
	}
}
```

编译并运行上述代码，运行结果为：true，即创建的单例对象 s1 和 s2 为同一对象。由于静态单例对象没有作为 Singleton 的成员变量直接实例化，因此类加载时不会实例化 Singleton，第一次调用 getInstance() 时将加载内部类 HolderClass，在该内部类中定义了一个 static 类型的变量 instance，此时会首先初始化这个成员变量，由 Java 虚拟机来保证其线程安全性，确保该成员变量只能初始化一次。由于 getInstance() 方法没有任何线程锁定，因此其性能不会造成任何影响。  

**通过使用 IoDH，我们既可以实现延迟加载，又可以保证线程安全，不影响系统性能，不失为一种最好的 Java 语言单例模式实现方式**（其缺点是与编程语言本身的特性相关，很多面向对象语言不支持IoDH）。  
 
**练习**  
分别使用饿汉式单例、带双重检查锁定机制的懒汉式单例以及 IoDH 技术实现负载均衡器 LoadBalancer。  

至此，三种单例类的实现方式我们均已学习完毕，它们分别是饿汉式单例、懒汉式单例以及 IoDH。