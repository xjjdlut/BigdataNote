第一步：检查依赖项

Requirements:

* Unix System
* JDK 1.8
* Maven 3.3 or later
* ProtocolBuffer 2.5.0
* CMake 3.1 or newer (if compiling native code)
* Zlib devel (if compiling native code)
* openssl devel (if compiling native hadoop-pipes and to get the best HDFS encryption performance)
* Linux FUSE (Filesystem in Userspace) version 2.6 or above (if compiling fuse_dfs)
* Internet connection for first build (to fetch all Maven and Hadoop dependencies)
* python (for releasedocs)
* bats (for shell code testing)
* Node.js / bower / Ember-cli (for YARN UI v2 building)

ProtocolBuffer没有安装，需要安装该组件：

wget https://github.com/protocolbuffers/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz

执行如下命令：

```bash
tar -xvf protobuf
cd protobuf
./configure --prefix=/usr/local/protobuf
make
make check
make install
```

更改环境变量：

export PATH=$PATH:/usr/local/protobuf/bin/
export PKG_CONFIG_PATH=/usr/local/protobuf/lib/pkgconfig/

##### 配置动态链接库

vim /etc/ld.so.conf
在文件中添加/usr/local/protobuf/lib（注意: 在新行处添加）

安装cmake

wget -c https://cmake.org/files/v3.5/cmake-3.5.2-Linux-x86_64.tar.gz

 sudo mv cmake-3.5.2-Linux-x86_64 /opt/cmake

设置环境变量：

export CMAKE_HOME=/opt/cmake/bin
export PATH=$CMAKE_HOME:$PATH

开始编译：

mvn -X package -Pdist,native,docs -DskipTests -Dtar

mvn package -Pdist,native -DskipTests -Dtar -Dmaven.javadoc.skip=true

编译报错1：

Downloading: https://repository.cloudera.com/artifactory/cloudera-repos/com/cloudera/cdh/cdh-root/6.3.2/cdh-root-6.3.2.pom
[ERROR] [ERROR] Some problems were encountered while processing the POMs:
[FATAL] Non-resolvable parent POM for org.apache.hadoop:hadoop-main:3.0.0-cdh6.3.2: Could not transfer artifact com.cloudera.cdh:cdh-root:pom:6.3.2 from/to cloudera repos (https://repository.cloudera.com/artifactory/cloudera-repos): Remote host closed connection during handshake and 'parent.relativePath' points at wrong local POM @ line 19, column 11

maven的代理设置没有生效，导致无法上网。正确配置maven的代理，然后将maven的central镜像配置成阿里云的镜像，其他的使用Cloudera的默认配置即可正常进行编译。

编译报错2：

[ERROR] Failed to execute goal org.apache.maven.plugins:maven-enforcer-plugin:3.0.0-M1:enforce (enforce-banned-dependencies) on project hadoop-client-check-test-invariants: Some Enforcer rules have failed. Look above for specific messages explaining why the rule failed. -> [Help 1]

这里的对enforcer的解释是，这是做环境约束检查用，到pom.xml中找到对应的enforce插件：

这里对java.version和maven.version做了约束，把配置改成本地对应的版本号即可。



编译支持snappy压缩：

编译之前要安装snappy-devel rpm包，否则编译会报错。

mvn clean package -Pdist,native -DskipTests -Dtar -Drequire.snappy -Dsnappy.lib=/usr/lib64 -Dbundle.snappy -Drequire.zstd -Dzstd.lib=/usr/lib64 -Dbundle.zstd  -Drequire.openssl -Dopenssl.lib=/usr/lib64 -Dbundle.openssl  -Dmaven.javadoc.skip=true



