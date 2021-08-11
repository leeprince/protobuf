# 一、安装

## （一）安装 protobuf
- 到 https://github.com/protocolbuffers/protobuf/releases/ 下载相应系统的 protoc 文件
> 本人系统是：macos, 下载的是：protoc-3.17.3-osx-x86_64.zip

- 解压后，将 protoc（protoc-3.17.3-osx-x86_64/bin/protoc） 复制到 $GOPATH/bin 路径下

## （二）下载包
### 1. protobuf、grpc 包
```
go get google.golang.org/protobuf
go get google.golang.org/grpc
```
### 2. 与 micro v3 相关包
> https://github.com/asim/go-micro/tree/master/cmd/protoc-gen-micro
```
go get github.com/asim/go-micro/cmd/protoc-gen-micro/v3
```

## （三）安装相关命令到 $GOBIN
### 1. protoc-gen-go、 protoc-gen-go-grpc
```
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
```
```
go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.26
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.1
```

> 注意：go get google.golang.org/grpc 可能下载失败;用下面这个方式代替
```
git clone https://github.com/grpc/grpc-go.git $GOPATH/src/google.golang.org/grpc 
git clone https://github.com/golang/net.git $GOPATH/src/golang.org/x/net
git clone https://github.com/golang/text.git $GOPATH/src/golang.org/x/text
go get -u github.com/golang/protobuf/{proto,protoc-gen-go}
git clone https://github.com/google/go-genproto.git $GOPATH/src/google.golang.org/genproto cd $GOPATH/src/
go install google.golang.org/grpc
```


# 二、编写 proto 文件 

## （一）syntax = "proto3";

## （二）package
### 1. package
### 2. option go_package

## （三）message

## （四）service
```
service Greeter {
    rpc Hello(HelloRequest) returns (HelloResponse) {}
}
```
```
import "google/api/annotations.proto";
service Greeter {
    rpc PingGet(PingReq) returns (PingRsp) {
        option (google.api.http) = {
            get : "/task-api/ping"
        };
    }
    rpc PingPost(PingReq) returns (PingRsp) {
        option (google.api.http) = {
            post : "/task-api/ping"
            body : "*"
        };
    }
}
```


# 三、根据 proto 文件生成 protobuf 的 go 文件
protoc 最简单操作说明
> protoc -I=$SRC_DIR --go_out=$DST_DIR $SRC_DIR/*.proto

## （一）【推荐】protoc 生成 protobuf go 文件到远程仓库（github）

proto 文件与 ptotoc 生成的 grpc protobuf go 文件当作一个项目，并提交到公共仓库中。 

这样服务端和客户端很好共用同一份 protobuf go 文件。




### 1. 生成：greeter.pb.go
- protoc-gen-go 命令自动生成 *.pb.go

> 方式一 【推荐】
1. cd . # 在当前项目protobuf根目录下
2. 执行：
    ```
    protoc -I=proto/greeter --go_out=./grpc/ proto/greeter/greeter.proto
    ```
3. 在 grpc/ 目录下自动创建 greeter 目录并生成的文件：greeter.pb.go

> 方式二
1. cd proto/greeter # 进入 greeter.proto 所在目录
2. 执行：
    ```
    protoc -I=. --go_out=../../grpc ./greeter.proto
    ```
3. 在 grpc/ 目录下自动创建greeter 目录并生成的文件：greeter.pb.go

### 2. 生成：greeter.pb.go、greeter_grpc.pb.go
- protoc-gen-go 命令自动生成 *.pb.go
- protoc-gen-go-grpc 命令自动生成 *_grpc..pb.go


1. cd . # 在当前项目protobuf根目录下
2. 执行：
```
protoc -I=proto/greeter --go_out=./grpc --go-grpc_out=./grpc/ proto/greeter/greeter.proto
```
3. 在 grpc/ 目录下自动创建 greeter 目录并生成的文件：greeter.pb.go、greeter_grpc.pb.go

### 3. 生成：greeter.pb.go、greeter_grpc.pb.go、greeter.micro.go
生成 *.micro.go 前提安装 micro 相关包 `一、` `（二）` `2. 与 micro v3 相关包` 

- protoc-gen-go 命令自动生成 *.pb.go
- protoc-gen-go-grpc  *_grpc.pb.go
- protoc-gen-micro  *.micro.go

1. cd . # 在当前项目protobuf根目录下
2. 执行：
```
protoc -I=proto/greeter --go_out=./grpc --go-grpc_out=./grpc/ --micro_out=./grpc/ proto/greeter/greeter.proto
```
3. 在 grpc/ 目录下自动创建 greeter 目录并生成的文件：greeter.pb.go、greeter_grpc.pb.go、greeter.micro.go



## （二）protoc 生成 protobuf go 文件到 GOROOT
1. cd 到 *.proto 文件所在目录
> 因为设置 `--go_opt` `--go-grpc_opt`，如果未 cd 到 *.proto 文件所在目录，而是在外部的话，
> 会在 `--go_out` `--go_grpc_out` 下重新创建 *.proto 文件的目录到生成的路径中。
2. 执行：
```
protoc --go_out=$GOROOT/src/rpc/greeter/ --go_opt=paths=source_relative \
    --go-grpc_out=$GOROOT/src/rpc/greeter/ --go-grpc_opt=paths=source_relative \
    greeter.proto
```


## （三）protoc 生成 protobuf go 文件保存到当前项目
> 服务端和客户端需同时存在同一份 protobuf go 文件

1. cd 到 *.proto 文件所在目录
2. 执行：
```server
protoc --go_out=../../server/protobuf/hello --go_opt=paths=source_relative \
    --go-grpc_out=../../server/protobuf/hello --go-grpc_opt=paths=source_relative \
    hello.proto
```
```client
protoc --go_out=../../client/protobuf/hello --go_opt=paths=source_relative \
    --go-grpc_out=../../client/protobuf/hello --go-grpc_opt=paths=source_relative \
    hello.proto
```