# simple-mq
## 目标
- 实现一个简单的mq
## 方案设想
### rpc
- 先采用dubbo或者thrift这类现成的方案组成
- 不采用http的形式
### 存储
- offset 存db 缓存至 redis
- 数据存储至文件系统fdfs（db太重，redis会丢）
- ack模式先支持同步可靠的ack模式（落地后返回ack）
- 先支持拉模型
### 安全要素
- 简单采用账号&密码的形式来实现
- 可采用白名单ip的形式来提升安全性

## 架构
### client端
#### producer
- 提供简单易用的api（参考kakfa & rocketmq）
- 提供一定的重试容错策略（使用retryable来实现）
- 提供异步调用方案（仅关注ack，ack失败提供必要的重试）
- 可提供的功能，retryable失败之后，落本地库（有侵入性），定时任务轮训，目前赞成的方案存redis，数据库有点重
#### consumer
- 提供拉模式（用完再拉，为空则定时），暂不支持长连接（推拉一体的形式）
- 提供简单易用的api
### server端
- producer 发消息落地后，ack回执
- consumer 拉去后通过ack，重置offset
- 可提供功能，producer可能会重发，所以是否提供一个全局唯一的key来做唯一性校验，可考虑方案，布隆过滤器
