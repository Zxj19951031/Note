# Java Virtual Machine
## 什么是JVM？
JAVA虚拟机是一个执行JAVA字节码文件（*.class）的进程。
## 类加载过程
**加载** -> 验证 -> **准备** -> 解析 -> **初始化** -> 使用 -> 卸载
### 加载
- 通常在第一次用到某类时就会进行**加载**操作
- 带有main方法的类在进程启动后就会**加载**操作
### 验证
- JVM会根据规范验证字节码文件
### 准备
- JVM计算类的大小分配对应的内存空间
- 为静态成员变量分配内存空间，并设置初始值
### 解析
- JVM将符号引用解析为直接引用
### 初始化
- 为静态成员变量赋初始化值
- 执行静态代码块
- 执行构造方法
```java
public class Main {
    public static int a = 1;

    static {
        System.out.println("step 1 a = " + a);
        a++;
        System.out.println("step 2 a = " + a);
    }

    public Main() {
        System.out.println("step 3 a = " + a);
        Main.a++;
        System.out.println("step 4 a = " + a);
    }

    public static void main(String[] args) {
        // write your code here
        new Main();
        /*
        output:
        step 1 a = 1
        step 2 a = 2
        step 3 a = 2
        step 4 a = 3
         */
    }
}
```
## 类加载器
- Bootstap ClassLoader 根类加载器 加载<JAVA_HOME>/lib目录中的核心类 由C++实现
- Extension ClassLoader 扩展类类加载器（ExtClassLoader） 加载<JAVA_HOME>/lib/ext目录中的类或环境变量java.ext.dirs对应的路径下的类
- Application ClassLoader 应用程序类加载器
## 双亲委派机制
类加载器（除根类加载器）在加载类时，会尝试让`parent`的`loadClass`方法进行加载类，若父类无法加载该类则下放权力给当前类加载器进行加载类
```java
java.lang.ClassLoader#loadClass(java.lang.String, boolean)
```
```java
if (c == null) {
    long t0 = System.nanoTime();
    try {
        if (parent != null) {
            c = parent.loadClass(name, false);
        } else {
            c = findBootstrapClassOrNull(name);
        }
    } catch (ClassNotFoundException e) {
        // ClassNotFoundException thrown if class not found
        // from the non-null parent class loader
    }

    if (c == null) {
        // If still not found, then invoke findClass in order
        // to find the class.
        long t1 = System.nanoTime();
        c = findClass(name);

        // this is the defining class loader; record the stats
        sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
        sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
        sun.misc.PerfCounter.getFindClasses().increment();
    }
}
```
## 内存模型

