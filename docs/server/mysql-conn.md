# 如何使MYSQL保持长连接

Note: 在node中使用mysql driver的时候，往往隔一段时间就会出错，提示mysql连接断掉了

## 原因

MYSQL内部有一个wait timeout变量，当一个mysql连接(没有行为)超过这个时间之后，server就会关闭这个连接，也节省了资源

## 解决方式

1. 提高server的wait timeout
1. 每隔一段时间向服务器发送一个"select 1;"查询，重新开始超时计数
1. 使用连接池，自动管理连接

## Update

Node使用mysql连接池的时候，使用query方法, 可以自动回收连接