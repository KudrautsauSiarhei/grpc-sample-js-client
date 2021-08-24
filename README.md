# grpc-sample-js-client

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

```shell
protoc --proto_path=proto --js_out=import_style=commonjs,binary:src/proto --grpc-web_out=import_style=commonjs,mode=grpcwebtext:src/proto/ proto/GreetingService.proto
```


```shell
envoy -c envoy.yaml
```
