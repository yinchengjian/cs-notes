mybatis是这样的。
首先他是会读取配置文件，然后通过该reader得到SqlSessionFactory，通过该工厂得到sqlsession，然后sqlsession.getmapper()获取mapper接口，就可以完成后续的数据库操作。
1.在得到factory时，就已经将该接口对象和代理工厂(class,mapperproxyfactory)添加到了一个map中
2.getmapper时就会通过该map获取到对应的代理工厂，然后创建一个代理类，jdk代理类即使实现了invocationholder接口，实现了invoke方法，并且传递了一个mapperproxy 对象（就是代理实例的处理程序），用来完成对应方法的实现。
3.当getmapper获得一个接口实例后，会通过调用对应方法来实现数据操作，还是会通过mapperproxy对象的invoke方法来完成，所获得的method是通过反射完成的，参数都一样传递的，

![1550061010788](C:\Users\yinchengjian\AppData\Roaming\Typora\typora-user-images\1550061010788.png)

