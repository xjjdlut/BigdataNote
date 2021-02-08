如何在hdfs中增加一个用户：

sudo -u hdfs hadoop fs -mkdir /user/hadoop
sudo -u hdfs hadoop fs -chown hadoop:supergroup /user/hadoop

更新hdfs的用户组和操作系统的一致：

sudo -u hdfs hdfs dfsadmin -refreshUserToGroupsMappings