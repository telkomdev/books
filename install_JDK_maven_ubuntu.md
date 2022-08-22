## Install Java and Maven on Ubuntu

### Install JRE and JDK

install JDK
```shell
$ sudo apt-get install default-jdk
$ javac -version
```

install JRE
```shell
$ sudo apt-get install default-jre
$ java -version
```

### Install Maven
Visit Maven download page https://maven.apache.org/download.cgi, and choose the latest version.
download the latest version of maven
```shell
$ wget https://downloads.apache.org/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz
```

extract the file
```shell
$ tar -xvf apache-maven-3.6.3-bin.tar.gz
```

move to `opt` folder
```shell
$ sudo mv apache-maven-3.6.3 /opt/maven
```

add Maven to environment variables
```shell
$ sudo vi ~/.profile
```

add the following configuration
```shell
export JAVA_HOME=/usr/lib/jvm/default-java
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```

save and load the environment variables 
```shell
$ source ~/.profile
```

check Maven version
```shell
$ mvn -v
Apache Maven 3.6.3 (cecedd343002696d0abb50b32b541b8a6ba2883f)
Maven home: /opt/maven
Java version: 11.0.9.1, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-129-generic", arch: "amd64", family: "unix"
```

