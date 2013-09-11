## Interceptor(拦截器)与Filter的区别整理

* 使用范围不同： Filter是Servlet规范规定的，只能用于Web程序中。Interceptor既可以Web程序又可以Application,Swing程序。
* 容器不同：Filter是Servlet规范中定义的，由Servlet容器支持，Interceptor由Spring容器支持。
* 使用的资源不同： Interceptor是Spring的一个组件，配置在Spring文件中，可以使用其内的任何资源，对象，数据源，事务管理等。
* 深度不同: Filter只能在Servlet前后起作用，Interceptor能够深入到方法前后，异常抛出前后等。

>优先使用Interceptor.