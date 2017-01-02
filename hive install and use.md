# hive install and use



### 设置环境变量（/etc/profile）

export HIVE_HOME=/home/corly/download/hive-2.1.1
export PATH=\$JAVA_HOME/bin:\$JAVA_HOME/jre/bin:\$HIVE_HOME/bin:\$PATH

### 创建Hive运行时目录并赋予权限

➜  hadoop-2.7.3 bin/hdfs dfs -mkdir /tmp

➜  hadoop-2.7.3 bin/hdfs dfs -mkdir -p /user/hive/warehouse

➜  hadoop-2.7.3 bin/hdfs dfs -chmod g+w /tmp

➜  hadoop-2.7.3 bin/hdfs dfs -chmod g+w /user/hive/warehouse



### 修改配置文件



- hdf-site.xml

hive默认使用derby保存元数据，此时我们将其修改为mysql。我们知道配置一个数据库连接，必须要使用username、password、jdbcDriver和url，在hive-site.xml中，我们修改如下配置

```xml
<property>	
	<name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost:3306/hive_metastore</value>
</property>
 
<property>
            <name>javax.jdo.option.ConnectionDriverName</name>
            <value>com.mysql.jdbc.Driver</value>
    </property>
 
    <property>
            <name>javax.jdo.option.ConnectionUserName</name>
            <value>hive</value>
    </property>
 
    <property>
            <name>javax.jdo.option.ConnectionPassword</name>
            <value>hive</value>
    </property>
```


```xml
<name>hive.exec.scratchdir</name>
<value>/tmp/hive-${user.name}</value>

 <name>hive.exec.local.scratchdir</name>
 <value>/tmp/${user.name}</value>

<name>hive.downloaded.resources.dir</name>
<value>/tmp/${user.name}_resources</value>

<name>hive.scratch.dir.permission</name>
    <value>733</value>
```





## wordcount



1. create table wordcount

   > create table wordcount(sentence string);

2. load data to table

   > load data local inpath '/home/corly/desktop/wordcount.txt' into table wordcount;

3. 用split 去查看数据，将每行的数据用空格分割成数组

   > select split(sentence,' ') from wordcount;

4. **explode()** takes in an **array** (or a map) as an **input** and gives the elements of the array (map) as **separate rows** for **output.**

   > select explode(split(sentence,' ')) AS Each_Word from wordcount;

5. use **count **and **group by** command to group similar words and perform count operation on the grouped words.

   > select Each_word,count(1) as count 
   >
   > 	from (select explode(split(sentence,' ')) as Each_Word from wordcount)
   > 	  	ResultTable group by Each_Word;