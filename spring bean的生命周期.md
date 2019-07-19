# 				spring bean的生命周期

## 一.Java类的加载机制

类从被加载到虚拟机内存中开始，到卸载出内存为止，它的整个生命周期包括：加载、验证、准备、解析、初始化、使用和卸载7个阶段。

​	![img](http://incdn1.b0.upaiyun.com/2017/06/2fb054008ca2898e0a17f7d79ce525a1.png) 

### 1.加载

“加载”是了“类加载”过程中的一个阶段，在加载阶段，虚拟机需要完成以下3件事情。

- 1 )通过一个类的全限定名来获取定义此类的二进制字节流。
- 2 ) 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。
- 3 ) 在内存中生成一个代表这个类的java.lang.Class对象 ,作为方法区这个类的各种数的访问入口。

加载阶段完成后,虚拟机外部的二进制字节流就按照虚拟机所需的格式存储在方法区之中 ,方法区中的数据存储格式由虚拟机实现自行定义,虚拟机规范未规定此区域的具体数据结构。然后在内存中实例化一个java.lang.Class类的对象(并没有明确规定是在Java堆中,对 于HotSpot虚拟机而言,Class对象比较特殊,它虽然是对象,但是存放在方法区里面),这个对象将作为程序访问方法区中的这些类型数据的外部接口。

**对于类加载过程的其他阶段，一个非数组的加载阶段是开发人员控制性最强的，因为加载阶段既可以使用系统提供的引导加载器来完成，也可以由用户自定义的类加载器去完成。** 

### 2.验证

“验证”是连接阶段的第一步，这一阶段是为了确保 Class 文件内的字节流格式符合Java虚拟机要求，不会危害虚拟机安全。 

验证总体上分为4个阶段： 文件格式验证、元数据验证、字节码验证、符号引用验证。 



### 3.准备

准备阶段是正式为类变量分配内存并设置类变量初始值的阶段，这些内存都将在方法区中进行分配。 
注：


- 1）这时候进行内存分配的仅包括类变量（static），而不包括实例变量，实例变量会在对象实例化时随着对象一块分配在Java堆中。 
- 2）这里所设置的初始值通常情况下是数据类型默认的零值（如0、0L、null、false等），而不是被在Java代码中被显式地赋予的值。


### 4.解析

”解析“阶段是虚拟机将常量池内的符号引用替换为直接引用的过程，符号引用以 CONSTANT_Class_info、CONSTANT_Fieldref_info、CONSTANT_Mehodref_info 等类型的常量出现（即 类名称，变量名称，方法名称等信息）。那么符号引用与直接引用有什么关联呢？

- 符号引用（Symbolic References）：即用一组符号来描述所引用的目标。它与虚拟机的内存布局无关，引用的目标不一定已经加载到内存中。
- 直接引用（Direct References）：直接引用可以是指向目标的指针、相对偏移量或是一个能简介定位到目标的句柄。它是和虚拟机内存布局相关的。

解析动作主要针对 类或接口、字段、类方法、接口方法、方法类型、方法句柄 和 调用限定符 7类符号引用进行。

- **符号引用**:符号引用以一组符号来描述所引用的目标，符号可以是任何形式的字面量，只要使用时能无歧义地定位到目标即可。符号引用与虚拟机实现的内存布局无关，引用 的目标并不一定已经加载到内存中。
- **直接引用**:直接引用可以是直接指向目标的指针、相对偏移量或是一个能间接定位到目标的句柄。直接引用是与虚拟机实现的内存布局相关的，如果有了直接引用，那么引用的目标必定已经在内存中存在。

### 5.初始化

类的初始化阶段是类加载过程的最后一步，该阶段才真正开始执行类中定义的Java程序代码（或者说是字节码）。 

对于初始化阶段，虚拟机规范有且仅有5种情况必须立即对类进行初始化：    

​	 ①.遇到new（使用new关键字实例化对象）、getstatic（获取一个类的静态字段，final修饰符修饰的静态字段除外）、putstatic（设置一个类的静态字段，final修饰符修饰的静态字段除外）和invokestatic（调用一个类的静态方法）这4条字节码指令时，如果类还没有初始化，则必须首先对其初始化    

​	 ②.使用java.lang.reflect包中的方法对类进行反射调用时，如果类还没有初始化，则必须首先对其初始化     		

​	③.当初始化一个类时，如果其父类还没有初始化，则必须首先初始化其父类     

​	④.当虚拟机启动时，需要指定一个主类（main方法所在的类），虚拟机会首选初始化这个主类    

​	 ⑤.当使用JDK1.7的动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化。   

这5种行为称为对一个类进行主动引用，除此之外所有引用类的方式，都不会触发初始化，称为被动引用。

初始化完成之后，bean就可以被使用，使用之后将会被虚拟机回收。

**Java中任何的bean都会完成这些工作，包括spring加载bean的过程，那么spring中的bean又和我们平时通过new关键字拿到的实例有什么区别呢？下面我们一起来看spring是如何加载的。**

## 二.spring bean的生命周期

  

| spring bean的加载过程                                        |
| ------------------------------------------------------------ |
| ![1534814631443](C:\Users\wangzhan\AppData\Local\Temp\1534814631443.png) |
| ![1534814692557](C:\Users\wangzhan\AppData\Local\Temp\1534814692557.png) |

![1534822733880](C:\Users\wangzhan\AppData\Local\Temp\1534822733880.png)

 

### 1.获取资源

无论是由xml配置的bean还是由配置类配置的bean，把配置加载到spring中 一定是spring开始加载bean的第一步。加载并将配置完成的类载入之后，就开始进行下一步的解析工作。

```java
//XmlBeanFactory方式加载资源
Resource resource =new ClassPathResource("AplicationContext.xml");
//解析
 BeanFactory beanFactory=new XmlBeanFactory(resource);
```

```java
//ClassPathXmlApplicationContext加载，同时还会解析
ClassPathXmlApplicationContext bf=new ClassPathXmlApplicationContext("AplicationContext.xml");
```

### 2.解析与注册

加载完资源之后，spring会对不同的配置进行解析。解析的标签有两种，一种是spring的默认标签，还有就是自定义标签。

```java
//解析bean默认标签
private void parseDefaultElement(Element ele, BeanDefinitionParserDelegate delegate) {
   if (delegate.nodeNameEquals(ele, IMPORT_ELEMENT)) {
      //对import标签的处理
       importBeanDefinitionResource(ele);
   }
   else if (delegate.nodeNameEquals(ele, ALIAS_ELEMENT)) {
      //对alias标签的处理
       processAliasRegistration(ele);
   }
   else if (delegate.nodeNameEquals(ele, BEAN_ELEMENT)) {
      //对bean标签的处理
       processBeanDefinition(ele, delegate);
   }
   else if (delegate.nodeNameEquals(ele, NESTED_BEANS_ELEMENT)) {
      // 对beans标签的处理
      doRegisterBeanDefinitions(ele);
   }
}
```

解析完成之后，将会注册到容器的注册表，到此spring对bean的解释就完成啦。

### 3.获取实例、注入和增强

spring获取到bean的所有定义之后，通过类加载器，把class文件加载到内存中，然后通过反射机制初始化，然后将需要注入的属性注入进去。

![1534814565253](C:\Users\wangzhan\AppData\Local\Temp\1534814565253.png)

通过打印出来的bean的加载过程，我们可以清晰的看到在实例化过程的顺序。

###4.销毁

随着容器的销毁，容器内的对象也将随之销毁。







