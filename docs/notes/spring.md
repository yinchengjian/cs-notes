## IOC

1.xml配置的bean会通过xml解析到beanDefinition对象中，

![1548653168705](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1548653168705.png)

2.xml解析

是通过BEanDefinitionReader的实现类XmlBeanDefinitionReader去做的：

- 将xml文件加载到内存
- 获取根标签下的所有标签
- 遍历标签列表，读取id，class属性
- 创建BeanDefinition对象，将读取到的属性值保存到对象中。
- 然后将<id,BeanDefinition>键值对缓存到Map中



![1550129224432](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1550129224432.png)



## AOP