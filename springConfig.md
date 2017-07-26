# 介绍Spring，mybatis项目的配置过程

## 1. 首先生成maven的web项目
## 2. 在pom.xml中配置依赖项和插件
## 3. 建立项目结构，如src/main/java用来存放源代码 src/test/java存放测试代码
## resources包存放所有的配置信息
## 4. 组织各层次的包目录，如：dao层，service层，entity层，web层
## 5. 首先配置web.xml
        > 将Spring容器的存放地点写入:<context-param> </context-param>
        > 配置容器监听器，用来启动Spring容器：<listener></listener>
        > 配置DispatcherServlet和servlet mapping
        > 其他等需要在配置：如欢迎界面，错误码，过滤器等
## 6. 然后配置SpringContext
        >在resources下面创建springconfig包存放全部spring配置
        > webcontext下面配置web相关的组件，比如：视图解析器
        > applicationcontext配置其他所有的组件，也可将其分为几个部分，分别配置dao层，servcie层组件
## 7. 写jdbc.properties文件
        > 该文件主要写数据库类型，数据库url地址，用户名和密码
## 8. 配置 mybatis-config.xml
        > 配置mybatis数据库操作的相关属性，如：是否使用自增键，是否使用缓存等
## 9. 数据库建表，添加相应的测试数据
## 10. 写相应的实体类位于entity包下
## 11. 写相关实体类的dao操作，只写接口，不必写具体的操作
## 12. 在resources文件下创建mybatisMapper包，存放实际的数据库操作
## 13. 写service层
## 14. 写web层，主要为控制器
## 15. 根据控制层的逻辑写相应的jsp