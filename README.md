<h2>spring-boot-absolute-config</h2>

<h3>前言</h3>

该工程是为解决应用部署应用时指定配置文件存放位置的问题.

SpringBoot项目默认加载以下位置的配置文件:

    classpath:
    file:./
    classpath:config/
    file:./config/:

想要指定外部的配置文件, 一种方法就是通过启动脚本来控制:

    在启动脚本中添加:
    -Dspring.config.location=文件绝对路径
    
但有时候有些项目需要兼容之前的老项目,就会遇到使用外部绝对路径的来指定配置文件了,每次都在启动脚本中添加,显然不是很合适.因此诞生了该工程.

<h3>实现方式</h3>

通过实现 **EnvironmentPostProcessor** 接口, 自定义实现方法:

    public void postProcessEnvironment(ConfigurableEnvironment environment, SpringApplication application)

来实现加载自定义配置文件.

<h3>使用说明</h3>

1. 引入pom文件

        <dependency>
            <groupId>com.github.springboot</groupId>
          	<artifactId>absolute-config</artifactId>
          	<version>1.0.0-RELEASE</version>
        </dependency>
        
2. 在classpath下的配置文件中增加参数
        
        如,在application.yml中添加
        config.file.absolute.path: /opt/app/config/**/**/application.yml 
        
3. 重启项目
    
    重启项目时,会自动加载指定位置的配置文件;
    
    
<h3>注意事项</h3>

1. 支持配置文件的格式
    
        1) classpath下SpringBoot默认加载application.properties、application.yml或application.yaml;
        2) 外置配置文件可以是以.properties、.yml或.yaml结尾（注意配置内容的格式）；

2. 外部加载的配置文件,不能使用原始配置文件的key

        如: server.port: 8090
        此参数只在classpath下的配置文件中生效,在外部加载的配置文件中不生效.

    此类key主要是在 ConfigFileApplicationListener 中进行加载.
    
3. 引入了配置文件,但没配置config.file.absolute.path

    此时不会报错,只会在启动时打印提醒的语句.
    
4. 配置了错误的config.file.absolute.path

    此时在项目启动时会打印出错误的异常栈,但不影响程序的正常启动.
    
    但是,如果你的项目中依赖了外置配置文件中的内容,可能会报错.