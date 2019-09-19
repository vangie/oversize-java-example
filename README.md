# 函数计算 Java 大项目模板

本项目模板用于支持打包后大于 50 M 的 java 项目。其工作原理是将 pom.xml 内声明的第三方 jar 包拷贝到 nas 目录，然后通过自定义的 Classloader 从 nas 目录装载这些 Jar 包 。

## 初始化

```bash
fun init vangie/oversize-java-example
```

文件说明
```bash
$ tree src/main/java
src/main/java
└── example
    ├── App.java
    └── Entrypoint.java

1 directory, 2 files
```

Entrypoint.java 文件是入口文件，该文件会负责装载  App 类。开发者只需要在 App.java 文件中添加业务逻辑即可。


## 工作原理

函数默认的 Classloader 首先会装载 Entrypoint 类，然后 Entrypoint 类去装载其他类的时候会用自己的 Classloader（Entrypoint.class.getClassloader)，而这个 classloader 不包含 NAS 目录的，也就没有办法装载到放置在 NAS 目录下的 jar 包。

所以我们这里首先创建一个新的 ChildFirstURLClassLoader，类如其名，会优先自己加载 class，而不是双亲委派。然后将 App 类通过 Class.forName 的方式进行装载，给 Class.forName 指定我们新建的 ChildFirstURLClassLoader。这样后续的类装载其他类都会用新的 ClassLoader。

## 本地运行

```bash
mvn package && fun local invoke
```

## 部署

```bash
mvn package && fun deploy
```