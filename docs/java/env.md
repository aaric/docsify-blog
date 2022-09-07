# Java 环境变量

## 1 Install

### 1.1 jdk1.8.0_202

```bash
mkdir /usr/java
tar -zxvf jdk-8u202-linux-x64.tar.gz -C /usr/java/
```

### 1.2 jdk-17.0.4

```bash
mkdir /usr/java
tar -zxvf jdk-17.0.4_linux-x64_bin.tar.gz -C /usr/java/
```

## 2 Linux Env

### 2.1 jdk1.8.0_202

```bash
cat >> /etc/profile <<-'EOF'

# jdk1.8.0_202
export JAVA_HOME=/usr/java/jdk1.8.0_202
# windows | set CLASSPATH=.;%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar
export CLASSPATH=.:$JAVA_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
EOF
source /etc/profile
```

### 2.2 jdk-17.0.4

```bash
cat >> /etc/profile <<-'EOF'

# jdk-17.0.4
export JAVA_HOME=/usr/java/jdk-17.0.4
# windows | set CLASSPATH=.;%JAVA_HOME%\lib\tools.jar;%JAVA_HOME%\lib\dt.jar
export CLASSPATH=.:$JAVA_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
EOF
source /etc/profile
```

## 3 Testing

```bash
java -version
```
