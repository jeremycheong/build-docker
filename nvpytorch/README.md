### 镜像构建        
默认参数变量：  
* VERSION="22.12"
* USER_NAME=zhangym
* GUID=1000   
构建指令：
```sh
docker build -t nvpytorch:1.13-cuda11.8 .
``` 
使用变量构建：
```sh
docker build --build-arg VERSION=22.12 -t nvpytorch:1.14-cuda11.8 .
```
