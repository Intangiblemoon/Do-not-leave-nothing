#Maven依赖文件pom.xml解析

###<scope>

####取值

- **compile**

如果在一项依赖中不进行配置，那么其会遵守其特性Convention Over Configuration（惯例优于配置）将scope值默认为compile。也就是说当不进行配置scope属性时，scope的值为compile。compile意味着被依赖项目需要参与当前项目的编译，打包、测试等一系列活动都离不开它。

- **test**
test表示依赖项目仅仅参与测试相关的工作，包括测试代码的编译，执行。

- **runntime**
runntime表示被依赖项目无需参与项目的编译，但是项目后期的测试和项目运行周期中该依赖需要进行参与。可以说较之compile只是不参与编译，如：oracle jdbc驱动包，scope为runntime。

- **provided**
provided表示在工程编译打包时*可不*打包，可由其他服务提供（所以打包理论上也是没问题的，你好又见面了compile）。

- **system**
system表示在工程编译打包时*可不*打包，依赖项从本地文件系统抓取，需要配合systemPath属性使用。

####继承
- A–>B–>C

当前项目为A，A依赖于B，B依赖于C。
当C是test或者provided时，C直接被丢弃，A不依赖C； 
否则A依赖C，C的scope继承于B的scope。