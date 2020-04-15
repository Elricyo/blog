---
title: SpringBoot动态注册Bean
date: 2020-03-31 16:18:14
tags: SpringBoot
---
# SpringBoot动态注册Bean
## 1.注册的方式
### 1.1 @Import
&nbsp;&nbsp;利用@Import来像SpringBoot注册Bean是最简单的，只要往注解中添加自己的一个类，那么这个类就会被依赖注入。
```java
//新建一个DemoBean1的类，并且默认在构造器里输出标志
public class DemoBean1 {
    public DemoBean1(){
        System.out.println("DemoBean1被实例化!!!");
    }
}


//然后在启动类使用@Import引入DemoBean1来注入这个对象
@SpringBootApplication
@Import(DemoBean1.class)
public class SpringBootAnnotationApplication {

	public static void main(String[] args) {
		ConfigurableApplicationContext applicationContext =SpringApplication.run(SpringBootAnnotationApplication.class, args);
	}

}

//测试依赖注入成不成功
@RestController
public class DemoController {
    //依赖注入刚刚的Bean
    @Autowired
    DemoBean1 demoBean1;


    @RequestMapping("/demo")
    public String demo(){
        demoBean1.print();
        return "SUCCESS";
    }
}
```
启动项目，然后调用一下接口``` curl http://localhost:8080/demo```
![](https://tva1.sinaimg.cn/large/00831rSTgy1gdd4nb0uslj321o0jkn5a.jpg)
动态注册Bean成功。
## 1.2 BeanDefinitionRegistryPostProcessor 注册Bean
&nbsp;&nbsp;使用BeanDefinitionRegistryPostProcessor来注册Bean，一般可以通过@Import启动自己的启动自定义的BeanDefinitionRegistryPostProcessor。
```java
//首先需要一个引子的类，来启动加载我们自定义动态注册的类
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(StartDemoProcessor.class)
public @interface StartDemo {
}

//这个注解@Import了我们的类，来动态注册
public class StartDemoProcessor implements BeanDefinitionRegistryPostProcessor {
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry beanDefinitionRegistry) throws BeansException {
        BeanDefinitionBuilder beanDefinitionBuilder = BeanDefinitionBuilder.genericBeanDefinition(DemoBean2.class);
        BeanDefinition beanDefinition = beanDefinitionBuilder.getRawBeanDefinition();
        //动态注册DemoBean2
        beanDefinitionRegistry.registerBeanDefinition(DemoBean2.class.getName(),beanDefinition);
    }

    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {

    }
}

//截下来使用@Autowired来测试
@RestController
public class DemoController {
    @Autowired
    DemoBean2 demoBean2;

    @RequestMapping("/demo2")
    public String dmeo2(){
        demoBean2.print();
        return "SUCCESS";
    }
}
```
启动项目，然后调用一下接口``` curl http://localhost:8080/demo2```
![](https://tva1.sinaimg.cn/large/00831rSTgy1gdd59kk151j31r40piqbw.jpg)
动态注入成功。
## 1.3ApplicationContextAware注册Bean
使用ApplicationContextAware也是跟BeanDefinitionRegistryPostProcessor一样，来注册Bean，这里就不展开。