---
title: Controller单元测试
date: 2016-11-19 14:30:23
categories: Java
---
近期发现可以使用Mock对Controller层进行测试，相比平时不断地重启Tomcat，简单实用很多。<!-- more -->

先编写一个需被测试的Controller接口类。
1. 在IDEA中创建maven web项目，并且在pom.xml中加入如下必要依赖。
    注意：Spring4与servlet2.5存在不兼容问题，应选择2.5以上版本。
    ![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/1.png)
2. 编写Controller类。
	![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/2.png)
3. 配置web.xml，添加Spring MVC的DispatcherServlet。
	![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/3.png)
4. 编写Spring MVC的配置文件，开启相应的注解驱动、静态资源访问和组件扫描功能等。
	![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/4.png)

接口编写就到此为止。下面开始使用junit和Mock对这个Controller进行测试，校验其正确性。

常规[junit单元测试](http://baike.baidu.com/link?url=XzAsp4SE4-J0TAhwe6H2aXsUKBPUq0VVJK5gwjJKcWAp2dnM4a7jKqaGMQ8JkgYwHLeoDIyFmetpxXk9UEK7Ka)时，可编写该类的单元测试类，并使用`@Test`注解标注单元测试方法，最终使用Assert断言执行结果，判断方法有没有被正确执行。

而在Spring框架下，单元测试中往往需要获取IoC容器的bean。虽然可以在`@Before`注解下，读取Spring配置文件，再获取ApplicationContext，再获取容器中的bean。但这样过于麻烦，较方便的做法是使用整合了junit的Spring Test，步骤如下：
1. 在单元测试类上标注`@RunWith(SpringJUnit4ClassRunner.class)`，表示用以Spring提供的SpringJUnit4ClassRunner加载本类，执行单元测试。
2. 在单元测试类上标注`@ContextConfiguration({“…….xml”,”…….xml”})`，定义当前单元测试使用的配置文件，可以为多个。
3. 在类中正常注入IoC容器中的Bean，开始执行测试。

使用Spring Test进行单元测试方便许多，不过依旧无法对Controller层进行单元测试。因为调度Controller接口的是浏览器请求，Spring Test无法做到模拟浏览器请求。Mock可以模拟浏览器请求，并直接在后端代码中调度Controller，观察Controller的具体响应。

在Spring Test单元测试基础上使用Mock，模拟http请求，对Controller层编写单元测试。步骤如下：
1. 添加`@WebAppConfiguration`注解，表示当前的测试类的测试环境由默认的ApplicationContext转换为WebApplicationContext，也就是web应用的环境测试当前类。
2. 注入WebApplicationContext对象。构造MockMvc对象时需要Spring容器作为参数。
3. 执行MockMvc对象的方法，模拟http请求，对Controller进行测试。

对上述say接口的单元测试类如下。
![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/5.png)

关于MockMvc对象，可以在源码中看到MockMvc类的描述。MockMvc为Spring MVC提供测试支持，并列举了使用demo。.perform()方法是设置请求的参数，.adnExcept()方法相当于Assert断言……
![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/6.png)

如果接口对数据库操作可能产生异常的话，也可以对测试类加上事务控制注解@Transactional，并对测试方法加上事务回滚注解@Rollback。到此即可简单地对Spring MVC编写单元测试类并执行，可以看到执行结果。
![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/7.png)

![image](http://ogvr8n3tg.bkt.clouddn.com/Controller%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95/8.png)

MockMvc对象提供的测试接口还有很多，例如对Controller类的验证、对Controller类执行方法的验证，在源码中均可看到API，就不一一列举。