# <center>spring源码阅读总结</center> 
&#160; &#160; &#160; &#160;spring是java web的一套开源框架，可以理解为一个轻量级容器。它的核心有二分别是：IoC，AOP。  
&#160; &#160; &#160; &#160;IoC（Inversion of Control）:控制反转，之前很不理解“控制反转”的意思，现在我给它个通俗的解释，其实就是将我们的bean对象交给spring去管理了，这也就是开头我为什么会说spring是个容器了。bean翻译成汉语就是“豆子”，那么spring就类似于一个碗来盛我们的bean，并且我们在开发种各种服务之间的调用不需要我们去手动new对象来进行调用，spring会在需要的时候来实例化对象提供给我们使用。谈到IoC不得不说它之中的一个核心 DI（依赖注入）。  

	步骤：A：启动Spring容器；B：从Spring容器中把对象取出来；C：最后，对象调用实际的业务方法。 

	A：启动Spring容器：
	在类路径下寻找配置文件来实例化容器：ApplicationContext context = new ClassPathXmlApplicationContext("配置文件类路径");

	通过这种方式加载。需要将spring配置文件放到当前项目的classpath路径下。classpath路径指的是当前项目的src目录，该目录是java源文件的存放位置。

 

	在文件系统路径下寻找配置文件来实例化容器：ApplicationContext context = new FileSystemXmlApplicationContext("配置文件系统路径"); 

	

	注：经常采用第一种方式启动Spring容器。

	B:从Spring容器中把对象取出来：

	context.getBean("配置文件中bean的id"); 如：HelloWorld helloWorld = (HelloWorld)context.getBean("helloWorld");

	C:对象调用实际的业务方法:

	helloWorld.hello(); 

 
并且spring种获取的getBean()均是单例模式的。
   <em>注意：可以看下单例模式：懒汉，恶汉的区别，并且能够自己写出来</em>  

			synchronized (this.mergedBeanDefinitions) {
			RootBeanDefinition mbd = null;

			// Check with full lock now in order to enforce the same merged instance.
			if (containingBd == null) {
				mbd = this.mergedBeanDefinitions.get(beanName);
			}

			if (mbd == null) {
				if (bd.getParentName() == null) {
					// Use copy of given root bean definition.
					if (bd instanceof RootBeanDefinition) {
						mbd = ((RootBeanDefinition) bd).cloneBeanDefinition();
					}
					else {
						mbd = new RootBeanDefinition(bd);
					}
				}
				else {
					// Child bean definition: needs to be merged with parent.
					BeanDefinition pbd;
					try {
						String parentBeanName = transformedBeanName(bd.getParentName());
						if (!beanName.equals(parentBeanName)) {
							pbd = getMergedBeanDefinition(parentBeanName);
						}
						else {
							if (getParentBeanFactory() instanceof ConfigurableBeanFactory) {
								pbd = ((ConfigurableBeanFactory) getParentBeanFactory()).getMergedBeanDefinition(parentBeanName);
							}
							else {
								throw new NoSuchBeanDefinitionException(bd.getParentName(),
										"Parent name '" + bd.getParentName() + "' is equal to bean name '" + beanName +
										"': cannot be resolved without an AbstractBeanFactory parent");
							}
						}
					}
					catch (NoSuchBeanDefinitionException ex) {
						throw new BeanDefinitionStoreException(bd.getResourceDescription(), beanName,
								"Could not resolve parent bean definition '" + bd.getParentName() + "'", ex);
					}
					// Deep copy with overridden values.
					mbd = new RootBeanDefinition(pbd);
					mbd.overrideFrom(bd);
				}

				// Set default singleton scope, if not configured before.
				if (!StringUtils.hasLength(mbd.getScope())) {
					mbd.setScope(RootBeanDefinition.SCOPE_SINGLETON);
				}

				// A bean contained in a non-singleton bean cannot be a singleton itself.
				// Let's correct this on the fly here, since this might be the result of
				// parent-child merging for the outer bean, in which case the original inner bean
				// definition will not have inherited the merged outer bean's singleton status.
				if (containingBd != null && !containingBd.isSingleton() && mbd.isSingleton()) {
					mbd.setScope(containingBd.getScope());
				}

				// Only cache the merged bean definition if we're already about to create an
				// instance of the bean, or at least have already created an instance before.
				if (containingBd == null && isCacheBeanMetadata()) {
					this.mergedBeanDefinitions.put(beanName, mbd);
				}
			}

			return mbd;
		}
  
&#160; &#160; &#160; &#160;DI（Dependency Injection）依赖注入，IoC的核心，IoC就是基于DI的。可以参考这csdn这篇文章
[依赖注入的理解](https://blog.csdn.net/z3881006/article/details/53932779)，一般知道它的两种注入方式：通过注解，xml配置即可。上边链接中提到的BeanFactory 和 ApplicationContext补充一点区别就是：<em>BeanFactory会懒加载，需要什么bean实例化什么bean，而ApplicationContext会实例化所有的bean</em>。  
&#160; &#160; &#160; &#160;AOP(Aspect Oriented Programming)面向切面编程，属于一种编程范式跟我们提到的：面向对象编程，面向接口编程，面向过程编程，函数式编程一样同属于一种编程范式即就是定义了一种编程规范，每个编程范式都存在其优缺点，并且使用的场景不一样。<b>我们实际开发过程中一个 controller,service,dao属于一个模块，所有模块累加起来构成了一个项目。所有的模块可以看成是横向划分的（好多个豆腐块摞在了一起）AOP的概念呢就是将这些个横着的豆腐块竖着切一刀。比如我们的事物控制层常常放在service层，就是从service层竖着切了一刀。</b> 再比如果某几个方法需要判断一下当前用户的权限。最常用的是写一个公共方法然后再在那几个方法中进行调用即可。<b>利用spring中的AOP则不需要在业务方法中添加任何代码，只需要进行方法切面加入权限相当于在方法前进行拦截。</b>  
springMVC流程工作流程：  
1、用户发送请求至前端控制器DispatcherServlet   
2、DispatcherServlet收到请求调用HandlerMapping处理器映射器。   
3、处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(二者组成HandlerExecutionChain),并将其一并返回给  DispatcherServlet。   
4、DispatcherServlet通过HandlerAdapter处理器适配器调用处理器     
5、执行处理器(Controller，也叫后端控制器)。   
6、Controller执行完成返回ModelAndView   
7、HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet   
8、DispatcherServlet将ModelAndView传给ViewReslover视图解析器   
9、ViewReslover解析后返回具体View   
10、DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。   
11、DispatcherServlet对用户进行响应  
DispatcherServlet是springMVC的核心，它的源码应该好好看下，包括它是什么地方spring与tomcat容器的上下文进行绑定的  HandlerMapping是两种处理请求和cotroller是如何映射的：xml，依赖注入。
[spring源码解析](http://www.cnblogs.com/fangjian0423/p/springMVC-dispatcherServlet.html#preface)

 

 

