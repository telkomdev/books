## Install

Install JRE 11
```shell
$ sudo apt install openjdk-11-jre-headless
```

Install Scala 2 https://www.scala-lang.org/download/scala2.html
```shell
$ wget https://downloads.lightbend.com/scala/2.13.7/scala-2.13.7.tgz
$ tar -xvzf scala-2.13.7.tgz
$ sudo mv scala-2.13.7 /usr/local/scala
```

Add Scala to your Environment and add this line `export PATH=$PATH:/usr/local/scala/bin`
```shell
$ sudo vi ~/.profile

  # ~/.profile: executed by the command interpreter for login shells.
  # This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login
  # exists.
  # see /usr/share/doc/bash/examples/startup-files for examples.
  # the files are located in the bash-doc package.

  # the default umask is set in /etc/profile; for setting the umask
  # for ssh logins, install and configure the libpam-umask package.
  #umask 022

  # if running bash
  if [ -n "$BASH_VERSION" ]; then
      # include .bashrc if it exists
      if [ -f "$HOME/.bashrc" ]; then
    . "$HOME/.bashrc"
      fi
  fi

  # set PATH so it includes user's private bin if it exists
  if [ -d "$HOME/bin" ] ; then
      PATH="$HOME/bin:$PATH"
  fi

  # set PATH so it includes user's private bin if it exists
  if [ -d "$HOME/.local/bin" ] ; then
      PATH="$HOME/.local/bin:$PATH"
  fi

  #scala
  export PATH=$PATH:/usr/local/scala/bin
```

Make the changes
```shell
$ source vi ~/.profile
```

Validate Scala installation
```shell
$ scala -version
$ Scala code runner version 2.13.7 -- Copyright 2002-2021, LAMP/EPFL and Lightbend, Inc.
```

Download Apache Spark https://spark.apache.org/downloads.html
```shell
$  wget https://downloads.apache.org/spark/spark-3.2.0/spark-3.2.0-bin-hadoop3.2.tgz
```

Extract Apache Spark
```shell
$ tar -xvzf spark-3.2.0-bin-hadoop3.2.tgz
```

Install Apache Spark
```shell
$ sudo mv spark-3.2.0-bin-hadoop3.2 /opt/spark
```

Add Apache Spark to your Environment and add this three line
```
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
export PYSPARK_PYTHON=/usr/bin/python3
```

```shell
$ sudo vi ~/.profile
  # ~/.profile: executed by the command interpreter for login shells.
  # This file is not read by bash(1), if ~/.bash_profile or ~/.bash_login
  # exists.
  # see /usr/share/doc/bash/examples/startup-files for examples.
  # the files are located in the bash-doc package.

  # the default umask is set in /etc/profile; for setting the umask
  # for ssh logins, install and configure the libpam-umask package.
  #umask 022

  # if running bash
  if [ -n "$BASH_VERSION" ]; then
      # include .bashrc if it exists
      if [ -f "$HOME/.bashrc" ]; then
    . "$HOME/.bashrc"
      fi
  fi

  # set PATH so it includes user's private bin if it exists
  if [ -d "$HOME/bin" ] ; then
      PATH="$HOME/bin:$PATH"
  fi

  # set PATH so it includes user's private bin if it exists
  if [ -d "$HOME/.local/bin" ] ; then
      PATH="$HOME/.local/bin:$PATH"
  fi

  #scala
  export PATH=$PATH:/usr/local/scala/bin

  #spark
  export SPARK_HOME=/opt/spark
  export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
  export PYSPARK_PYTHON=/usr/bin/python3
```

Make the changes
```shell
$ source vi ~/.profile
```

Run the following command to start the Spark master service and slave service.
```shell
$ start-master.sh
$ start-worker.sh spark://ubuntu-bionic:7077
```

Once the service is started go to the browser and type the following URL access spark page. From the page, you can see my master and slave service is started.
```
http://localhost:8080/
```

You can also check if spark-shell works fine by launching the spark-shell command.
```shell
$ spark-shell

  21/11/23 07:57:08 WARN Utils: Your hostname, ubuntu-bionic resolves to a loopback address: 127.0.1.1; using 10.0.2.15 instead (on interface enp0s3)
  21/11/23 07:57:08 WARN Utils: Set SPARK_LOCAL_IP if you need to bind to another address
  WARNING: An illegal reflective access operation has occurred
  WARNING: Illegal reflective access by org.apache.spark.unsafe.Platform (file:/opt/spark/jars/spark-unsafe_2.12-3.2.0.jar) to constructor java.nio.DirectByteBuffer(long,int)
  WARNING: Please consider reporting this to the maintainers of org.apache.spark.unsafe.Platform
  WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
  WARNING: All illegal access operations will be denied in a future release
  Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
  Setting default log level to "WARN".
  To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
  21/11/23 07:57:16 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
  Spark context Web UI available at http://10.0.2.15:4040
  Spark context available as 'sc' (master = local[*], app id = local-1637654237377).
  Spark session available as 'spark'.
  Welcome to
        ____              __
       / __/__  ___ _____/ /__
      _\ \/ _ \/ _ `/ __/  '_/
     /___/ .__/\_,_/_/ /_/\_\   version 3.2.0
        /_/

  Using Scala version 2.12.15 (OpenJDK 64-Bit Server VM, Java 11.0.11)
  Type in expressions to have them evaluated.
  Type :help for more information.

  scala>
```

Or with `pyspark`
```shell
$ pyspark
  Python 3.6.9 (default, Jan 26 2021, 15:33:00)
  [GCC 8.4.0] on linux
  Type "help", "copyright", "credits" or "license" for more information.
  21/11/23 07:58:21 WARN Utils: Your hostname, ubuntu-bionic resolves to a loopback address: 127.0.1.1; using 10.0.2.15 instead (on interface enp0s3)
  21/11/23 07:58:21 WARN Utils: Set SPARK_LOCAL_IP if you need to bind to another address
  WARNING: An illegal reflective access operation has occurred
  WARNING: Illegal reflective access by org.apache.spark.unsafe.Platform (file:/opt/spark/jars/spark-unsafe_2.12-3.2.0.jar) to constructor java.nio.DirectByteBuffer(long,int)
  WARNING: Please consider reporting this to the maintainers of org.apache.spark.unsafe.Platform
  WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
  WARNING: All illegal access operations will be denied in a future release
  Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
  Setting default log level to "WARN".
  To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
  21/11/23 07:58:23 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
  /opt/spark/python/pyspark/context.py:238: FutureWarning: Python 3.6 support is deprecated in Spark 3.2.
    FutureWarning
  Welcome to
        ____              __
       / __/__  ___ _____/ /__
      _\ \/ _ \/ _ `/ __/  '_/
     /__ / .__/\_,_/_/ /_/\_\   version 3.2.0
        /_/

  Using Python version 3.6.9 (default, Jan 26 2021 15:33:00)
  Spark context Web UI available at http://10.0.2.15:4040
  Spark context available as 'sc' (master = local[*], app id = local-1637654304261).
  SparkSession available as 'spark'.
  >>> txt = sc.textFile('file:////usr/share/doc/python/copyright')
  >>> print(txt.count())
  316
```

## Run Your first Python Job
Create `hello_spark.py`
```shell
$ vi hello_spark.py
```

Add this code
```python
import pyspark
sc = pyspark.SparkContext('local[*]')
sc.setLogLevel('WARN')

txt = sc.textFile('file:////usr/share/doc/python/copyright')
python_lines = txt.filter(lambda line: 'python' in line.lower())

with open('results.txt', 'w') as file_obj:
    file_obj.write(f'Number of lines: {txt.count()}\n')
    file_obj.write(f'Number of lines with python: {python_lines.count()}\n')
```

Submit Code to the Cluster
```shell
$ spark-submit hello_spark.py
  ...
  21/11/23 08:02:25 INFO BlockManagerMasterEndpoint: Registering block manager 10.0.2.15:35431 with 434.4 MiB RAM, BlockManagerId(driver, 10.0.2.15, 35431, None)
  21/11/23 08:02:25 INFO BlockManagerMaster: Registered BlockManager BlockManagerId(driver, 10.0.2.15, 35431, None)
  21/11/23 08:02:25 INFO BlockManager: Initialized BlockManager: BlockManagerId(driver, 10.0.2.15, 35431, None)
  /opt/spark/python/lib/pyspark.zip/pyspark/context.py:238: FutureWarning: Python 3.6 support is deprecated in Spark 3.2.
    FutureWarning
```
