# Jenkins下启动、停止Spring Cloud Jar包项目的Shell脚本

## 1.Jenkins中"Execute shell"的Command

```shell
cd /Users/Shared/myservice
sh jenkins-manager.sh stop
BUILD_ID=dontKillMe sh jenkins-manager.sh start &
```

1. “BUILD_ID=dontKillMe”是Jenkins下执行shell后台脚本必须的
2. Linux环境下可能需要写成 “BUILD_ID=dontKillMe nohup”
3. Mac环境下无须“nohup”(会报错)

## 2. jenkins-manager.sh

```shell
#!/bin/sh

APP_PATH=/Users/Shared/Jenkins/Home/workspace/my-maven-test/my-test-project/target/
APP_NAME=my-test-project
APP_FILE=${APP_PATH}${APP_NAME}.jar
APP_APP_PROFILE=dev

JAVA_XMS=128m
JAVA_XMX=1024m

start()
{
echo "==== Do Start ===="
if [ -f ${APP_FILE} ]
then
  chmod 777 ${APP_FILE}
  java -Xms${JAVA_XMS} -Xmx${JAVA_XMX} -jar ${APP_FILE} --spring.profiles.active=${APP_PROFILE}
else
  echo "There is no app file"
fi
}

stop()
{
echo "==== Do Stop ===="
PID=`ps -ef | grep ${APP_NAME}.jar | grep -v grep | awk '{print $2}'`
if [ ${PID} ]
then
  kill -9 ${PID}
else
  echo "=== Do not need to stop ==="
fi
}

case $1 in
  start)
    start
  ;;
  stop)
    stop
  ;;
  restart)
    stop
    start
  ;;
  *)
    echo "Parameter Error"
  ;;
esac
```

