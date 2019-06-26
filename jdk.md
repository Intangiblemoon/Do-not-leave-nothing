#jdk安装
- 1、查看系统中的java环境

　　[root@localhost ~]# java -version
　　openjdk version "1.8.0_101"
　　OpenJDK Runtime Environment (build 1.8.0_101-b13)
　　OpenJDK 64-Bit Server VM (build 25.101-b13, mixed mode)

　　进一步查看JDK信息：

　　[root@localhost ~]# rpm -qa | grep java
　　javapackages-tools-3.4.1-11.el7.noarch
　　java-1.8.0-openjdk-headless-1.8.0.101-3.b13.el7_2.x86_64
　　python-javapackages-3.4.1-11.el7.noarch
　　tzdata-java-2016f-1.el7.noarch
　　java-1.8.0-openjdk-1.8.0.101-3.b13.el7_2.x86_64


　　卸载 OpenJDK：rpm -e --nodeps

　　[root@localhost ~]# rpm -e --nodeps javapackages-tools-3.4.1-11.el7.noarch
　　[root@localhost ~]# rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.101-3.b13.el7_2.x86_64
　　[root@localhost ~]# rpm -e --nodeps python-javapackages-3.4.1-11.el7.noarch
　　[root@localhost ~]# rpm -e --nodeps tzdata-java-2016f-1.el7.noarch
　　[root@localhost ~]# rpm -e --nodeps java-1.8.0-openjdk-1.8.0.101-3.b13.el7_2.x86_64

- 2、 创建安装目录
mkdir /usr/local/java/
- 3、解压至安装目录
tar -zxvf jdk-8u171-linux-x64.tar.gz -C /usr/local/java/
- 4、设置环境变量
vim /etc/profile
在末尾添加
```
export JAVA_HOME=/usr/local/java/jdk1.8.0_171
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```
source /etc/profile
添加软链接
ln -s /usr/local/java/jdk1.8.0_171/bin/java /usr/bin/java

- 5、检查
java -version