cdh 6.3.2编译

cloudera 从6版本开始不再提供源码和单独编译好的安装包进行下载了，就连github上的源码也将hadoop的进行了隐藏，这样就不利于学习交流，所以就需要下载源码进行单独编译。

### 1.hadoop 3.0.0-cdh6.3.2

下载地址：https://github.com/cloudera/hadoop-common/archive/cdh6.3.2-release.tar.gz

编译安装笔记：

### 2.zookeeper：

下载地址：https://github.com/cloudera/zookeeper/archive/cdh6.3.2-release.tar.gz

编译安装笔记：



set ANT_OPTS=-Dhttp.proxyHost=10.18.8.60 -Dhttp.proxyPort=8008 -Dhttp.proxyUser=xujj -Dhttp.proxyPassword=Xx8503023  -Dhttps.proxyHost=10.18.8.60  -Dhttps.proxyPort=8008



xujj:Xx8503023@10.18.8.60:8008

### 3.hbase：

下载地址：https://github.com/cloudera/hbase/archive/cdh6.3.2-release.tar.gz

mvn clean package -DskipTests assembly:single

需要在hbase的pom.xml文件中增加如下内容，用来指定依赖包的来源：

  <repositories>
    <repository>
      <id>cdh.repo</id>
      <url>https://repository.cloudera.com/artifactory/cloudera-repos</url>
      <name>Cloudera Repositories</name>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
    <repository>
      <id>cdh.snapshots.repo</id>
      <url>https://repository.cloudera.com/artifactory/libs-snapshot-local</url>
      <name>Cloudera Snapshots Repository</name>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
      <releases>
        <enabled>false</enabled>
      </releases>
    </repository>
    <repository>
      <id>${distMgmtSnapshotsId}</id>
      <name>${distMgmtSnapshotsName}</name>
      <url>${distMgmtSnapshotsUrl}</url>
    </repository>
    <repository>
      <id>repository.jboss.org</id>
      <url>http://repository.jboss.org/nexus/content/groups/public/</url>
      <snapshots>
        <enabled>false</enabled>
      </snapshots>
    </repository>
  </repositories>

### 4.hive：

下载地址：https://github.com/cloudera/hive/archive/cdh6.3.2-release.tar.gz

mvn clean package -DskipTests -Phadoop-3 -Pdist

编译后的文件放在目录：/ulic/hadoop/cdh-root/hive-cdh6.3.2-release/packaging/target

### 5.hue：

下载地址：https://github.com/cloudera/hue/archive/cdh6.3.2-release.tar.gz

make apps

sudo mount /ulic/iso/CentOS7.6.iso /mnt/cdrom/ 

编译错误1：

In file included from Modules/LDAPObject.c:9:0:
Modules/errors.h:8:18: fatal error: lber.h: No such file or directory
 #include "lber.h"
                  ^
compilation terminated.

 解决办法：sudo yum -y install openldap-devel

编译错误2：

/ulic/hadoop/cdh-root/hue-cdh6.3.2-release/desktop/core/ext-py/lxml-3.3.6/src/lxml/includes/etree_defs.h:9:31: fatal error: libxml/xmlversion.h: No such file or directory
 #include "libxml/xmlversion.h"
                               ^
compilation terminated.

 解决办法：sudo yum install libxslt-devel

编译错误3：

src/_fastmath.c:36:18: fatal error: gmp.h: No such file or directory

include <gmp.h>

解决办法：sudo yum install -y gmp-devel

编译错误4：

c/_cffi_backend.c:15:17: fatal error: ffi.h: No such file or directory

解决办法：sudo yum install -y libffi-devel

hue的依赖检查：

https://docs.gethue.com/administrator/installation/dependencies/

编译错误5：

make[2]: Entering directory `/ulic/hadoop/cdh-root/hue-cdh6.3.2-release/desktop'
--- Installing development tool: ipython[5.2.0]
/ulic/hadoop/cdh-root/hue-cdh6.3.2-release/build/env/bin/python2.7 /ulic/hadoop/cdh-root/hue-cdh6.3.2-release/build/env/bin/easy_install -i https://pypi.python.org/simple/ \
   -H *.cloudera.com,pypi.python.org,files.pythonhosted.org -qq ipython==5.2.0
WARNING: The easy_install command is deprecated and will be removed in a future version.

_execfile
    code = compile(script, filename, 'exec')
  File "/tmp/easy_install-TRm7BR/traitlets-5.0.4/setup.py", line 41
    print(error, file=sys.stderr)
                     ^
SyntaxError: invalid syntax
make[2]: *** [ipython[5.2.0]] Error 1

ln -s  /opt/modules/python36/bin/python3 python2.7

编译错误6：



安装nodejs



### 6.spark：

下载地址：https://github.com/cloudera/spark/archive/cdh6.3.2-release.tar.gz

mvn -DskipTests clean package

编译成打包文件：

```javascript
./dev/make-distribution.sh  \
--name 3.0.0-cdh6.3.2 \
--tgz \
-Pyarn \
-Phadoop-3.0 \
-Phive \
-Phive-thriftserver  \
-Dhadoop.version=3.0.0-cdh6.3.2 
```

### 7.oozie：

下载地址：https://github.com/cloudera/oozie/archive/cdh6.3.2-release.tar.gz

```ruby
bin/mkdistro.sh  -DskipTests
```

### 8.impala：

下载地址：https://github.com/cloudera/Impala/archive/cdh6.3.2-release.tar.gz

设置环境变量：export IMPALA_HOME=/ulic/hadoop/cdh-root/Impala-cdh6.3.2-release

需要修改的配置文件$IMPALA_HOME/bin/impala-config.sh

export IMPALA_MAVEN_OPTIONS="-s /ulic/hadoop/dev_tools/apache-maven-3.3.9/conf/settings.xml "
export DOWNLOAD_CDH_COMPONENTS=false
export HADOOP_HOME="/ulic/hadoop/cdh-root/hadoop-common-cdh6.3.2-release/hadoop-dist/target/hadoop-3.0.0-cdh6.3.2"
export HIVE_HOME=/ulic/hadoop/cdh-root/hive-cdh6.3.2-release/packaging/target/apache-hive-2.1.1-cdh6.3.2-bin/apache-hive-2.1.1-cdh6.3.2-bin
export HBASE_HOME="/opt/hadoop/hbase-2.1.0-cdh6.3.2-client"
export KUDU_BUILD_DIR="/ulic/hadoop/cdh-root/kudu-cdh6.3.2-release/build/release"
export KUDU_CLIENT_DIR="/opt/modules/kudu"

```

$IMPALA_HOME/bin/bootstrap_system.sh  ##该步骤检查依赖包，安装系统确实的rpm包
source $IMPALA_HOME/bin/impala-config.sh
$IMPALA_HOME/buildall.sh -noclean -notests
```

编译好的文件放在$IMPALA_HOME/be/build/debug/service目录下，主要是二进制文件impalad

编译问题1：

pip安装太慢，替换阿里云的镜像

pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/

如果使用代理进行下载的话需要修改apache-impala-3.3.0/infra/python/deps/ 下面有个  pip_download.py 

原文件使用urllib获取包信息和下载文件，但是urllib对https的支持不好，所以需要使用urllib2进行下载，使用urllib2下载文件的方法如下：

```python
url='https://mirrors.aliyun.com/pypi/simple/boto3'
context = ssl._create_unverified_context()
print(urllib2.urlopen(url, context=context).read())
url = 'https://mirrors.aliyun.com/pypi/packages/fc/7c/f9cec79ab9c8fc190821c69fdbcb15ecba438ff35f13b3f8d9675efcfd0d/simplejson-3.3.0.tar.gz'
context = ssl._create_unverified_context()
  f = urllib2.urlopen(pkg_url) 
  with open(file_name, "wb") as code:
    code.write(f.read())
```



无法下载toolchain相关的包：

def wget_and_unpack_package(download_path, file_name, destination, wget_no_clobber):

try:
      sh.wget(download_path+" --no-check-certificate", directory_prefix=destination, no_clobber=wget_no_clobber)
      break
    except Exception, e:
      if attempt == NUM_ATTEMPTS:
        raise



编译错误：

[100%] Built target gen-deps
[ERROR] Error executing Maven.
[ERROR] 1 problem was encountered while building the effective settings
mvn -s /ulic/hadoop/cdh-root/Impala-cdh6.3.2-release/toolchain/cdh_components/m2-settings.xml -B install -DskipTests exited with code 0
make[3]: *** [impala-parent/CMakeFiles/impala-parent] Error 1

### 9.kudu：

下载地址：https://github.com/cloudera/kudu/archive/cdh6.3.2-release.tar.gz

build-support/enable_devtoolset.sh thirdparty/build-**if**-necessary.sh

mkdir -p build/release

cd build/release

编译并安装

```
../../build-support/enable_devtoolset.sh \
   ../../thirdparty/installed/common/bin/cmake \
   -DCMAKE_BUILD_TYPE=release \
  ../..
make -j4
make DESTDIR=/opt/modules/kudu install
```

### 10.flink

下载地址：https://github.com/apache/flink/archive/release-1.10.1.tar.gz

先编译flink-shaded-hadoop：https://codeload.github.com/apache/flink-shaded/tar.gz/release-7.0

mvn -T10C clean install -Dhadoop.version=3.0.0-cdh6.3.2



mvn clean install -DskipTests  -Pvendor-repos -Dhadoop.version=3.0.0-cdh6.3.2 -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true



mvn -T10C clean package -DskipTests -Pvendor-repos -Dhadoop.version=3.0.0-cdh6.3.2  -Pinclude-hadoop -Dmaven.javadoc.skip=true -Dcheckstyle.skip=true 

-rf :flink-yarn_2.11

mvn install -rf :flink-yarn_2.11

编译错误1：

[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.8.0:testCompile (default-testCompile) on project flink-yarn_2.11: Compilation failure
[ERROR] /ulic/hadoop/cdh-root/flink-release-1.9.3/flink-yarn/src/test/java/org/apache/flink/yarn/AbstractYarnClusterTest.java:[89,41] no suitable method found for newInstance(org.apache.hadoop.yarn.api.records.ApplicationId,org.apache.hadoop.yarn.api.records.ApplicationAttemptId,java.lang.String,java.lang.String,java.lang.String,java.lang.String,int,<nulltype>,org.apache.hadoop.yarn.api.records.YarnApplicationState,<nulltype>,<nulltype>,long,long,org.apache.hadoop.yarn.api.records.FinalApplicationStatus,<nulltype>,<nulltype>,float,<nulltype>,<nulltype>)
[ERROR] method org.apache.hadoop.yarn.api.records.ApplicationReport.newInstance(org.apache.hadoop.yarn.api.records.ApplicationId,org.apache.hadoop.yarn.api.records.ApplicationAttemptId,java.lang.String,java.lang.String,java.lang.String,java.lang.String,int,org.apache.hadoop.yarn.api.records.Token,org.apache.hadoop.yarn.api.records.YarnApplicationState,java.lang.String,java.lang.String,long,long,long,org.apache.hadoop.yarn.api.records.FinalApplicationStatus,org.apache.hadoop.yarn.api.records.ApplicationResourceUsageReport,java.lang.String,float,java.lang.String,org.apache.hadoop.yarn.api.records.Token) is not applicable

vim flink-1.9.1/flink-yarn/pom.xml

增加如下内容：

```xml
<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.8.0</version>
				<configuration>
					<source>${java.version}</source>
					<target>${java.version}</target>
					<skip>true</skip>
					<!-- The semantics of this option are reversed, see MCOMPILER-209. -->
					<useIncrementalCompilation>false</useIncrementalCompilation>
					<compilerArgs>
						<!-- Prevents recompilation due to missing package-info.class, see MCOMPILER-205 -->
						<arg>-Xpkginfo:always</arg>
					</compilerArgs>
				</configuration>
           </plugin>
```

