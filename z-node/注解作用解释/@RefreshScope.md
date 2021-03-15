##### 文档链接
    1.https://www.jianshu.com/p/188013dd3d02

##### RefreshScope作用
    (org.springframework.cloud.context.scope.refresh)是spring cloud提供的一种特殊的scope实现，用来实现配置、实例热加载。
      
##### Scope与ApplicationContext生命周期
    1.Singleton和Prototype是硬编码的，并不是Scope子类。 Scope实际上是自定义扩展的接口
    2.Scope Bean实例交由Scope自己创建，例如SessionScope是从Session中获取实例的，ThreadScope是从ThreadLocal中获取的，而RefreshScope是在内建缓存中获取的。

#### RefreshScope实现配置和实例刷新
##### RefreshScope 刷新过程
    1.提取标准参数(SYSTEM,JNDI,SERVLET)之外所有参数变量
    2.把原来的Environment里的参数放到一个新建的Spring Context容器下重新加载，完事之后关闭新容器
    3.提起更新过的参数(排除标准参数)
    4.比较出变更项
    5.发布环境变更事件,接收：EnvironmentChangeListener／LoggingRebinder
    6.RefreshScope用新的环境参数重新生成Bean
    重新生成的过程很简单，清除refreshscope缓存幷销毁Bean，下次就会重新从BeanFactory获取一个新的实例（该实例使用新的配置）
    
##### Spring Cloud Bus 如何触发 Refresh
    1.BusAutoConfiguration#BusRefreshConfiguration 发布一个RefreshBusEndpoint
    2.RefreshBusEndpoint 会从http端口触发广播RefreshRemoteApplicationEvent事件
    3.BusAutoConfiguration#refreshListener 负责接收事件(所有配置bus的节点)
    4.RefreshListener#onApplicationEvent 触发 ContextRefresher