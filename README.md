
# 编写 proto 文件 


# 根据 proto 文件生成 protobuf 的 go 文件
## 1. cd 到 *.proto 文件所在目录

## 2. 执行如下命令 
### 2.1 protoc 生成 protobuf go 文件格式
```
protoc --go_out={保存*.pb.go文件路径} --go_opt=paths=source_relative \
    --go-grpc_out={保存*_grpc.pb.go文件路径} --go-grpc_opt=paths=source_relative \
    {proto 文件}
```

#### 2.1.1 protoc 生成 protobuf go 文件保存到当前项目
protoc 生成的 grpc protobuf go 文件保存到当前项目（同一个 go.mod 项目）中
> 服务端和客户端需同时存在同一份 protobuf go 文件
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

#### 2.1.2 protoc 生成 protobuf go 文件到GOROOT
protoc 生成的 grpc protobuf go 文件保存到 GOROOT
> 服务端和客户端很好共用同一份 protobuf go 文件
```
protoc --go_out=$GOROOT/src/rpc/grpc_protobuf/proto/hello/ --go_opt=paths=source_relative \
    --go-grpc_out=$GOROOT/src/rpc/grpc_protobuf/proto/hello/ --go-grpc_opt=paths=source_relative \
    hello.proto
```

#### 2.1.3 protoc 生成 protobuf go 文件到远程仓库（github）
protoc 生成的 grpc protobuf go 文件外部 github 项目(单独存放 proto 文件也保存在外部项目中)
```
protoc --go_out=../../grpc/hello --go_opt=paths=source_relative \
    --go-grpc_out=../../grpc/hello --go-grpc_opt=paths=source_relative \
    hello.proto
```
