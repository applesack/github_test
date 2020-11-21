# **源码分析**

***源码版本***
> org.springframework.boot  
> 2.1.7.RELEASE

---
## **如何启动?**
启动Springboot，只需要在main方法中构造一个**SpringApplication**对象，然后调用这个对象的**run**方法，即可启动Springboot应用。
- 一般把整个项目的入口类、该类上有`@SpringbootApplication`注解的称为**启动类**

## **启动的步骤？**
大体上分为两步
1. `SpringApplication application = new SpringApplication(SourcecodeApplication.class);`  
这个步骤中，主要完成几个事情  
    1. 将启动类这个域指向构造方法参数中的类`SourcecodeApplication.class`
    2. [todo] primarySources
    3. *确定当前应用的类型，(方式是尝试加载某个类，比如classpath下含有servlet相关类，则确定当前是WEB环境)。
    4. *通过工厂类获取初始化器和监听器的实现，并注入到容器中来。
    5. 获取启动类(获取调用栈，找到方法名为"main"的方法所在的类)
2. `application.run(args);`
    其他主要的功能都在这个run方法内完成，包括bean的装配，初始化器的调用，web容器初始化等。

## **SpringFactoriesLoader**
用于spring容器获取配置文件中需要的各个接口的实现类。  
这些配置文件定义在`classpath:META-INF/spring.factories`中。  
配置文件的格式是`key=impl1,impl2...`，等于号左边是要实现的接口，右边是具体实现类的完整类名，当一个接口有多个实现的时候，每个实现之间用逗号分隔开。  

这个工厂类加载的入口在SpringFactoriesLoder#loadFactoryNames中  
方法签名：
- `loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader): List<String>`   

这个方法会先获取`factoryClass`的类名(字符串)，然后根据这个类名获取具体的实现，具体过程是：
1. 首先查找缓存中是否有这个接口实现的信息，假如有就直接返回，只有当缓存中没有才继续寻找，减少时间复杂度。
2. 通过`ClassLoader.getSystemResources(name)`这个方法，返回classpath下所有jar包中包含这个路径的集合。
3. 遍历这个集合，将对应位置的配置文件解析成键值对，遍历键值对将值用逗号分割后存入缓存。
4. 在遍历的过程中将符合条件的接口实现保存，遍历结束返回。

SpringFactoriesLoader中表示接口和实现类都是使用字符串格式，并没有做实例化，这是一个慢加载，只有在某个地方需要的时候才从工厂中获取对应的接口的实现类，自行做实例化处理，工厂本身不实例化实现类。

## **ApplicationContextInitializer (系统初始化器)**
spring容器中较为常用的一个扩展点，功能是在容器启动的时候执行一些代码  
实现这个功能有**3**种方式：
1. 在启动类中，在SpringApplication对象上调用`addInitializers()`方法，将自定义初始化器的实现注入进去
2. 在resource目录下的`META-INF`目录的下`spring.factories`文件中填写接口实现类，key值为`org.springframework.context.ApplicationContextInitializer`
3. 在resource目录下的`application.properties`配置文件中填写系统初始化类的接口实现信息，key值为`context.initializer.classes`

具体调用流程：  
1. 首先在构建SpringApplication的实例的过程中，调用了`setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class))`这个方法，这个方法从SpringFactoriesLoader中获取的初始化器接口的实现，并实例化了实现类，然后根据这些实现类的@Order注解或者Ordered接口进行**排序**，将这些实现类以`ArrayList`的形式保存在容器中。
2. 然后在`run()`方法内，准备上下文的过程中(`prepareContext()`)，调用`applyInitializers(context)`，初始化器被调用。
3. 在这个方法中，会遍历所有配置文件中的初始化器实现的实例，对于一个实例，首先会调用spring的工具类来检查这个实例的泛型类型，只有当泛型类型是`ApplicationContextInitializer`的子类的时候才会继续，否则会抛出异常，这里就是为什么实现初始化器的接口泛型要指定为`ApplicationContextInitializer`，假如不这样初始化器就不会被调用。~~*这里调用的是方式1定义的初始化器*~~
4. 在初始化器中，其中有一个名为`DelegatingApplicationContextInitializer`的初始化器，它的Order值为0，这意味着在排序的时候它将会被排在最前面，最先被调用，这个初始化器在执行的时候会检查环境变量中的`context.initializer.classes`属性，将实现类实例化，并排序调用。~~*这里调用的是第3种方式注册的初始化器*~~，因为是在Delegating中被调用，所以这种方式定义的初始化器没有参与和方式1一起的排序，它的排序是在Delegating中完成的。

**TIPS:**  
- 在配置文件中，假如有多个实现类，在多个实现类之间用逗号分隔，`key=class1,class2,class2...`
- 3种实现方式都需要实现`ApplicationContextInitializer`接口
- 可以用`@Order`注解决定各个初始化器的优先级，数字越小优先级越高，但`application.properties`中定义的优先于其他方式

## **Spring系统监听器**
在spring启动过程中，会产生多种事件(Event)，这些事件都继承自**ApplicationEvent**，而对于这些事件，会有多种监听器对事件进行监听，在spring运行的某些节点，spring会发布这些事件，交给对应的监听器处理，而spring的一些功能就是在这些监听器中实现的。

**入口:**  
SpringApplication的构造器中，调用`setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class))`将监听器的实现注入到spring容器中。