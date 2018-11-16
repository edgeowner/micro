## go-micro服务加入service mesh示范

使用go-micro的`client`&`server`istio插件: [hb-go/micro-plugins](https://github.com/hb-go/micro-plugins)

### TODO
- 命令参数与micro的兼容
- micro-plugins
    - http
        - FQDN Server/Service address定义
        - 支持server.HandlerOption定义api.Endpoint
        - 支持stream
    - gRPC
        - ……

## K8s
```bash
# 部署
$ kubectl apply -f service_deployment.yaml
$ kubectl apply -f destination-rule.yaml
$ kubectl apply -f virtual-service.yaml
$ kubectl apply -f gateway.yaml

# 验证
$ curl -H "Content-Type:application/json" -X POST -d '{"post":{ "name":{"key":"name","values":["Hobo"]}}}' http://192.168.99.100:31380/Example.Call
{"statusCode":200,"body":"{\"msg\":\"Hello Hobo\"}"}
```

## 开发测试

使用[sofa-mosn](https://github.com/alipay/sofa-mosn)做代理调试，参考[sofa-mosn/examples/http-sample](https://github.com/alipay/sofa-mosn/tree/master/examples/http-sample)

- 需要一个编译好的SOFAMosn程序`sofa-mosn`

### 运行示例
> 由于命令参数与micro有兼容问题，要运行示例sa、ca暂时需要手动修改代码
```bash
$ cd pwd

# run api service
$ cd api
$ go run main.go -sa localhost:8081 -ca :2046

# run srv service
$ cd srv
$ go run main.go -sa localhost:8082

# run api mosn 代理
$ ./sofa-mosn start -c mosn_api.json

# run srv mosn 代理
$ ./sofa-mosn start -c mosn_srv.json
```

### 测试
```bash
$ curl -H "Content-Type:application/json" -X POST -d '{"post":{ "name":{"key":"name","values":["Hobo"]}}}' http://127.0.0.1:2045/Example.Call
{"statusCode":200,"body":"{\"msg\":\"Hello Hobo\"}"}
```