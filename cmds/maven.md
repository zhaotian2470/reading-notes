# Lifecycle
maven支持如下lifecycle：default（build），clean，site，其中default包括如下phase：
* validate: validate the project is correct and all necessary information is available；
* compile: compile the source code；
* test: unit test；
* package: package compiled code；
* verify: ensure quality criteria, such as running integration tests；
* install: install the package into the local repository；
* deploy: deploy the package into remote repository；
[lifecycle参考](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference)

plugin goals包括两个部分：plugin，goal，它可以和build phase绑定
[build phase和plugin goals绑定关系](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#built-in-lifecycle-bindings)

# POM
POM的特点：
* POM（project object model）的继承：指定其它POM为parent，默认继承自super POM：[super POM实例](https://maven.apache.org/ref/3.6.3/maven-model-builder/super-pom.html)；
* POM的聚合：多个POM作为module，聚合为一个POM；
* POM的继承、聚合可以同时使用；

# Profiles
maven的配置可以来自多个地方：
* 系统/用户层次的settings.xml，用来保存repository, plugin repository，也可以保存property；
* 项目pom.xml；

激活profiles的方式：
* 命令行参数-P；
* 配置文件activeProfile；
* 配置文件activation：条件激活；
* 配置文件activeByDefault：如果本POM没有其它profile激活，则激活；

# Directory Layout
目录结构：
* src/main/java；
* src/main/resources；
* src/main/filters；
* src/main/webapp；
* src/test/java；
* src/test/resources；
* src/test/filters；
* src/it；
* src/assembly；
* src/site；

# Dependency Mechanism
依赖模式：
* transitive dependency：比较复杂，传递规则下面讲述；
* optional dependency：需要显示指出依赖，默认情况下不依赖；
* dependency exclusions：不希望transitive的依赖；

dependencies scope：
* compile：编译时依赖；
* provided：和compile类似，但是运行时由JDK或者容器提供，因此不需要依赖；
* runtime：编译时不需要，但是运行时需要；
* test：测试时依赖；
* system：和provided类似，但是运行时使用指定的JAR包；
* import：在pom的dependencymanagement使用，引入依赖版本；

transitive dependencies：依赖会按照一定如下规则传递：

|       |compile|provided|runtime|test|
|-------|:-----:|:------:|:-----:|:--:|
|compile|compile|-|runtime|-|
|provided|provided|-|provided|-|
|runtime|runtime|-|runtime|-|
|test|test|-|test|-|

上表的解释：表左边表示项目A依赖B的scope，表上面表示项目B依赖项目C的scpoe，表格内容表示项目A依赖项目C的scope，-表示不依赖

BOM（bill of materials）POM：
* 在一个POM中定义版本变量，并且在dependencymanagement定义模块版本；
* 模块定义方通过该POM的变量，定义模块版本；
* 模块使用方通过import，使用该模块的版本；

# Appendix

## Examples
```
mvn install -pl module2 -am
```

## Commands
```
mvn clean	# removes file generated at build-time in a project's directory (target by default)
mvn dependency:analyze
mvn dependency:list
mvn dependency:tree
mvn help:active-profiles
mvn help:effective-pom
mvn help:effective-settings
mvn install	# installs the package into the local repository for use as a dependency in other projects locally.
mvn package	# copies the final package to the remote repository for sharing with other developers and projects.
          	# if use build plugins for spring boot, we can run it, such as: java -jar target/koo-ds-webapp-product-stock-0.0.1-SNAPSHOT.jar
```

## Parameter
* -Dapp.id=koo-ds-webapp-product-stock: 设置app id;
* -Denv=neibu: 设置环境；
* -Dmaven.test.skip: 跳过测试;
* -Ptest: 设置profile;
* -pl dfzxvip-api: (projects to build)用于指定要编译的项目，例如子模块;
* -am: (also make dependent)用于编译该子模块所依赖的所有模块;

## pom.xml

### build plugins for spring boot
```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>2.1.5.RELEASE</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.10.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

## stuff
[maven user centre](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)
