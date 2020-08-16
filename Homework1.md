# Homework 1

## 运行tidb

### pd

- 首先配置go环境
- clone pd 代码
- 执行 `make` 命令编译`pd-server`
- 执行 `bin/pd-server --name="pd" --data-dir="/tmp/data/pd"  --client-urls="http://127.0.0.1:2379"  --peer-urls="http://127.0.0.1:2380"` 即可启动 pd

### tikv

- 首先配置rust环境, 使用nightly工具链
- clone tikv代码
- 执行 `make` 命令编译 `tikv-server`
- 执行 `target/debug/tikv-server --addr 127.0.0.1:20160 --advertise-addr 127.0.0.1:20160 --pd 127.0.0.1:2379 --data-dir /tmp/data/tikv` 即可启动一个tikv
- 修改 -addr端口和-advertise-addr端口可以启动额外的 `tikv-server`

### tidb
- clone tidb代码
- 执行 `make` 命令编译 `tidb-server`
- 执行 `bin/tidb-server` 即可启动一个`tidb-server`, 默认配置就可以连接到本地的 `pd-server` 和 `tikv`

## 事务启动时打印 `hello transaction`
- 在`tidb`源码中, 找到多个 `txn.go` 的源文件
- 其中 `session/txn.go` 中有个`getTxnFuture`方法,   `kv/txn.go` 中有个 `RunInNewTxn` 方法,  `tikv/txn.go`中是操作tikv事务的逻辑
- 顺着`session/txn.go` 找到 `session/session.go` 中有个`NewTxn` 方法, 添加日志后发现, 只会在嵌套事务时会执行
- 然后找到`PrepareTxnCtx`方法, 注释说会在必要的时候启动事务, 添加日志后, 会周期性打印`hello-transaction`, 应该是内部会有相关事务执行, 在执行`begin` 语句时, 也会打印`hello-transaction`的日志.
