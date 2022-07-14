# Foundational Spring
使用MVC的方式，构建Web

## Getting started with Spring
spring container（也叫做spring application context）创建并且管理很多component（也就做bean），并通过DI(dependency injection)：注入给其它component。

DI方式：
* configuration注解：通过带bean注解的方法提供component；
* autowiring注解：spring自动将其注入依赖该component的其它类；
* component scanning：spring通过classpath发现componet，并自动创建；
* autoconfiguration：spring boot猜测需要注入的component，例如自动引入其它包；

Spring工程目录：
* mvnw，mvnw.cmd：mavan脚本；
* pom.xml：maven依赖文件；
* src/main/java：代码；
* src/main/resources：资源，例如static包括静态web资源，templates包括web模版，application.properties包括配置；
* src/test/java：测试内容，参考main；
* src/test/resources：测试内容，参考main；

可以使用插件spring-boot-maven-plugin，将Spring打包成如下格式：
* war: 用于打包老的web程序，包含内容多，需要运行在server中;
* jar：用于打包Java库/插件/应用程序，有可能直接运行，大部分云平台支持，建议使用;

Spring包括的库：
* core spring framework：提供container、DI和其它功能，例如spring MVC，REST API，persistence，reactive-style programming；
* spring boot：autoconfiguration，startar依赖，actuator（提供运行时信息），环境属性，测试支持；
* spring data：persistence支持；
* spring security：安全支持；
* spring integration and spring batch：和其它application/componet的整合，spring integration用于real time，batch用于批处理；
* spring cloud：microservice支持；

## Developing web applicaitons
SpringMVC框架：
* controller和view是解藕的，可以通过如下方式传递数据：controller里面model拷贝到request；view使用reques里面的数据；
* view传递给web application的参数，可以通过Validation API（例如Hibernate）验证；

thymeleaf的说明：
* 在默认情况下，只解析一次templates，对应结果会被缓存，以供后边使用。如果不想缓存，需要修改配置（thymeleaf是spring.thymeleaf.cache）；

## Working with data
Spring可以访问数据库的方式包括：JDBCTemplate（已经过时）和JPA（新项目使用）

JPA的使用：
1. 构造entity：对应数据库记录；
1. 构造repository：对应数据库记录的操作；
1. 默认repository方法：按照repository传统，声明方法；
1. 定制repository方法：按照SQL语句，实现方法；

## Securing Spring
配置用户信息的保存地址：
* in-memory：用户信息保存在内存，适用于测试环境；
* jdbc-based：用户信息保存在关系数据库，；
* ldap-backed：用户信息保存在ldap服务器；
* custom user details service：定义数据库记录User，并实现接口UserDetails；定义UserRepository，实现findByUsername；定义UserRepositoryUserDetailsService，实现UserDetailsService；

配置认证信息（WebSecurityConfigurerAdapter.configure）：
* 哪些页面需要认证；
* 指定登录页面；
* 指定登出操作；
* 阻止CSRF(cross-site request forgery)；

获取用户信息：
* 注入principal对象；
* 注入authentication对象；
* 利用注解方法authenticationprincipal：和注入authentication对象相比，不需要强制转化；
* 从securitycontextholder获取security context：任何地方都可以使用这种方法；

## Working with configuration properties
配置的作用：
* Bean配置（Bean wiring）: declares application components to be created as beans and how they should be injected into each other；
* 属性配置（Property injection）: sets values on beans；

spring environment聚合多个来源的数据，提供统一的属性配置：
* JVM系统属性；
* 操作系统环境变量，例如SERVER_PORT=9090；
* 命令行参数，例如--server.port=9090；
* 配置文件，例如src/main/resources/application.properties，src/main/resources/application.yml；

属性配置的访问：注解configurationproperties读取属性；

属性配置元数据（方便IDE查找），可以存放到如下文件：src/main/resources/META-INF/additional-spring-configuration-metadata.json指定可配置的属性；

配置profile-specific属性：
* 放在不同的配置文件中：application-{profile name}.yml；application-{profile name}.properties；
* 对于yml配置，使用---分隔，并且指定不同的spring.profiles；
* 放到程序中不同的profile注解下：不同profile使用不同的bean：@Profile({"dev", "qa"})；

配置激活profile的方式：
* 配置属性：spring.profiles.active；
* 环境变量：export SPRING_PROFILES_ACTIVE=prod,audit,ha；
* 命令行参数：--spring.profiles.active=prod；

# Integrated Spring

## creating REST services
CRUD模式：
* create(post): /；
* read(get): /, /{id}；
* update(put-replace all attributes; patch-replace some attributes): /{id}；
* delete(delete): /{id}；

内置REST支持：
* HATEOAS(hypermedia as the engine of application state)：创建自描述的API；
* data-backed REST：引入依赖spring-boot-starter-data-rest；

## Consuming REST services
spring通过几种方法调用REST API：
* RestTemplate：同步的REST客户端；
* Traverson：同步的REST客户端，用于HATEOAS；
* WebClient：异步的REST客户端，用于reactive编程；

## Sending messages asynchronously
spring支持的异步通信：
JMS（Java Message Service）:such as ActiveMQ；
* receive modes: pull, push;

AMQP（Advanced Message Queueing Protocol）: such as RabbitMQ 
* sent to exchanges; 
* mq routes message from exchanges to queues with routing kyes；
* consumed from queues;
* receive modes: pull, push;

kafka:
* kafka cluster has many topics;
* kafka topic has many partition;
* messages in a partition are sequencial;
* only support push mode;

## Integrating Spring
整合的使用：
* 实现自定义的组件：例如创建网关；
* 配置组件之间的关系，可以使用一下配置方式：XML、Java、DSL；

整合的组件：
* channels：仅用于传递消息，不处理消息；
* filters：过滤消息；
* transformers：转换消息；
* routers：消息路由；
* splitters：拆分消息；
* aggregators：聚合消息；
* service activators：一般用于从input channel接收消息后调用服务处理，也可以将处理后的消息再发送到output channel；
* gateways：和外部对接的接口，可以接收外部的输入，或者将结果输出到外部；
* channel adapters：和外部对接的适配器，可以接收外部的输入，或者将结果输出到外部，spring已经提供很多channel adapters（endpoint modules）；

# Reactive Spring
ignore

# Cloud-native Spring

## Discovering services
spring cloud提供服务注册/发现组件eureka：
* 服务启动的时候，将自己注册到eureka；
* 其它服务通过eureka，查找服务实例；
* 其它服务通过客户端负载均衡器ribbon，决定使用哪个服务实例；

## Managing configuration
spring cloud提供配置服务器config server：
* config server支持从下列地方拉取数据：git（建议保存非敏感数据），hashicorp vault（建议保存敏感数据）；
* config server通过http接口提供配置；
* config server可以提供application/profile专用的属性；
* config server可以提供动态更新数据：手动（调用服务actuator接口），自动（git通过webhook通知config server，config server通过message broker通知应用程序；应用程序拉取最新配置）；

## Handling failure and latency
spring cloud提供circuit breaker服务：
* hystrix提供circuit breaker服务；
* hystrix dashboard使用hystrix stream的数据，可视化显示circuit breaker数据；
* turbine聚合多个服务circuit breaker数据；

# Deployed Spring

## Working with Spring Boot Actuator
spring提供actuator，用来监控程序

actuator默认导出的路径：
* /auditevents；
* /beans；
* /conditions: autoconfiguration conditiongs；
* /configprops；
* /env；
* /env/{toMatch}；
* /health；
* /heapdump；
* /httptrace；
* /info；
* /loggers；
* /loggers/{name}；
* /mappings：report of all http mappings and their handler methdos；
* /metrics；
* /metrics/{name}；
* /scheduledtasks；
* /threaddump；

actuator的定制：
* 修改默认导出路径的值；
* 添加导出路径；
* 添加对路径的认证；

## Administering Spring
spring提供admin，用来可视化actuator的输出：
* 发现actuator的机制：手工、自动（eureka）；
* admin可以添加login；
* admin可以通过认证方式连接actuator；

## Monitoring Spring with JMX
spring提供mbeans方式访问actuator：
* 通过jconsole可以发现actuator mbeans路径"org.springframework.boot"；
* 用户可以自行定义mbeans路径；
* 用户可以定制mbeans发送的通知；

## Deploying Spring
spring运行方式：
* IDE中运行：适用于开发；
* 命令行运行，例如maven goal sprint-boot:run；
* jar：建议的生产运行方式，可以放到Docker中运行；
* war：老的生产运行方式，不建议；


# Notes

## annotation
* componentscan：spring启动component scanning，将带有component，controller，repository，service等注解的类，注册为component；
* configuration：这是一个配置类；该类给container提供bean（通过带bean注释的方法）；
* configurationproperties：属性配置；
* conponent：用于控件的component scan；
* controller：用于http接口的component scan；
* crossorigin：用于跨域访问(cross-origin resource sharing)；
* data：提供一套方法，例如构造函数，getter，setter，equals, hashCode, toString等等；
* enableautoconfiguration：spring启动auto configuration；
* enableconfigserver：spring启动config server；
* enablehystrix：开启circuit breaker；
* enablewebsecurity：开启web认证；
* endpoint：自定义actuator；
* entity：数据库记录；
* feignclient：feign客户端，自动产生实现，类似repository；
* generatedvalue：自动产生的值；
* header: 文件名应该在消息头中;
* hystrixcommand: fallback调用的方法;
* id：记录的uuid；
* javax.validation.constraints：提供http接口参数验证；
* jmslistener：push模式的jms客户端；
* kafkalistener: push模式的kafka客户端;
* loadbalanced：使用负载均衡，例如ribbon；
* managedresource：JMX MBean；
* manytomany：多对多的数据关系；
* manytoone：多对一的数据关系；
* messaginggateway: spring integration为网关产生一个实现，类似repository;
* prepersist：保存数据之前的hookx；
* noargsconstructor：不带参数的构造函数；
* rabbitlistener: push模式的rabbitmq客户端;
* repository：用于数据库访问；
* repositoryrestcontroller：自定义数据库的REST接口；
* requestingmapping：和其它xxxmapping，一起处理http请求；
* requiredargsconstructor：带参数的构造函数；
* responsestatus: http响应状态；
* restcontroller：用于rest接口的controller，返回数据直接写到http响应里面；
* runwith：指示JUnit运行测试，可以带参数SpringRunner.class（spring使用的test runner，提供spring application context）；
* service：用于服务的component scan；
* slf4j：打印日志；
* springbootapplication：包括三个注解（springbootconfiguration, enableautoconfiguration, componentscan），参考对应的解释；
* springbootconfiguration：将该类作为configuration类，可以看成特殊的configuration注解；
* springboottest：指示JUnit跑测试用例的时候，使用spring boot能力，类似运行SpringApplication.run()；
* table：指定表名字；
* webmvctest：测试MVC程序；

