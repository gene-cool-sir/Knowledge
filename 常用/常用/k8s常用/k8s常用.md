# k8s常用

> 本地安装Docker

1.  idea安装docker插件, docker远程服务
    ```text
     vi /usr/lib/systemd/system/docker.service
     修改: ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2375
     systemctl daemon-reload
     systemctl restart docker
     查看 http:192.168.62.130:2375/info
    ```

> jenkins 部署路径 cd /root/cicd
> ​		     执行脚本路径： /data/cicd

> 环境部署

> [http://confluence.irootech.com/pages/viewpage.action?pageId=279881650](http://confluence.irootech.com/pages/viewpage.action?pageId=279881650 "http://confluence.irootech.com/pages/viewpage.action?pageId=279881650")
> 进入跳转机:
> 123.57.214.149   3H3llb58j1\$7NwVU
> 进入节点机器: node1   :  ssh  node1

1.  查看pods 命名空间在mds下的节点信息
    kubectl get pods -n mds
2.  本地docker打包并上传服务
    1.  更新打包包版本,在本地Idea打包

> ​     docker build -t devops-system-master\_2021081001 .

> ​       2) 查看本地镜像
> ​     docker images | grep mds

> ​     3 ) 本地镜像打tar包
> ​    docker save -o devops-system-master\_2021081001.tar devops-system-master\_2021081001

1.  上传tar包至节点服务器上
    方式一: node1节点上使用rz -e  上传命令
    > 方式二: fz:上传到跳板机, 然后通过scp命令到node1节点
    > eg. scp /root/devops-system-master\_2021081001.tar node1:/root/jarDir/
2.  load 上传后的tar包到本地镜像仓库
    docker load -i devops-system-master\_2021081001.tar
3.  更新tag ,镜像标签信息
    1.  查看k8s对于devops-system发布的配置文件中的image配置的版本号 (  kubectl edit deploy  资源容器名称  -n  命名空间 )
        dockerhub.kubekey.local/rootcloud/mds/devops-system:master\_2021081001
        查看docker images   -> devops-system-master\_2021081001
    2.  变更tag标签
    > ​     docker tag devops-system-master\_2021081001 dockerhub.kubekey.local/rootcloud/mds/devops-system:master\_2021081001
4.  kubectl 操作变更镜像版本,并且发布

> ​    kubectl get pods -n mds

```纯文本
- 查看 pods 下 mds的容器信息
- 查看已经发布的容器节点信息
```

> ​    kubectl get deploy -n mds

1.  编辑发布资源的配置文件,保存后重新发布

> kubectl edit deploy  资源容器名称  -n  命名空间
> kubect edit deploy devops-system  -n mds

1.  查看发布情况
    > kubectl get pods -n mds
2.  删除容器并且重启
    kubectl delete pods  容器pods的名称 -n mds

> 10 . 查看日志

> kubectl logs -f  容器pods的ID

1.  查看kubectl的一些命令别名配置
    cat  .bash\_profile
    1.别名配置 vi  /etc/profile
    2.设置默认命名空间: kubectl  config set-context  --current  --namespace=名字
    查看相应参数: kubectl  config view
2.  kw 命令
    > alias k4='kubectl -n rootcloud-v4'
    > alias kw='kubectl -n rootcloud-mw'
    > alias kt='kubectl -n rootcloud-tool'
    > alias k4desc='kubectl -n rootcloud-v4 describe '
    > alias k4log='kubectl -n rootcloud-v4 logs '
    > alias km='kubectl -n mds'
    > alias ks='kubectl -n kube-system'
    > alias kd='kubectl -n rootcloud-devops'
    > alias kn='kubectl -n ingress-nginx'
    > alias kv='kubectl -n rootcloud-visual'
    > alias kis='kubectl -n iot-sanycloud'
    > alias kl='kubectl -n logging'
    > alias kr='kubectl -n rook-ceph'
    > alias kc='kubectl -n cattle-system'
    > alias kops='kubectl -n kube-ops
3.  kw 复制文件到容器
    kw  需要同步的文件   容器ID:/容器内的路径
4.  k8s, 读取环境配置
    kubectl get cm -n mds
    kubectl edit cm devops-cnfmap -n mds
5.  查看容器日志
    宿主机:/var/lib/docker/containers/容器id
6.  获取容器lables
    kubectl  get pod --show-labels
7.  获取命名空间
    kubectl get namespaces
8.  描述信息
    kubectl describe pods
9.  查看日志
    kubectl logs \$POD\_NAME
10. 查看所有pods
    kubectl get pods -A
11. 查看所有对外暴露的service
    kubectl get service
12. 查看ing转发
    km get ing
13. alarmManager
    kubectl get cm -n mds
    kubectl edit cm alertmanager -n mds -o yaml
    重启alertmanager

> 24.## 查看 内存、CPU资源
> kubectl top pods -n mds
> 调整内存参数，对JVM进行优化配置
> kubectl edit deploy devops-dashboard
> 25 kubectl进入多副本容器
> km logs -f prometheus-bbb699c56-6kqvp  #此时会提示进入哪个容器查看日志
> km exec -it prometheus-bbb699c56-6kqvp -c prometheus-server -- sh  # -c 进入指定的容器
> curl -XPOST [http://prom.dc-pre.rootcloudapp.com/-/reload](http://prom.dc-pre.rootcloudapp.com/-/reload "http://prom.dc-pre.rootcloudapp.com/-/reload")  # k8s 重新发起请求加载配置文件, curl -XPOST ip/-/reload
> kubectl delete pod prometheus-bbb699c56-x8x9c # 删除容器,并自动重启

> \##进入容器
> kw exec -it mysql-788686777f-kn2rr -- sh
> \##登录MYSQL
> mysql -uroot -pPassw0rd\@Mysql

<https://github.com/docker/for-win/issues/8204>

<http://devops-web.vpc-ali.rootcloudapp.com/devops/system/userLoginOut>

<http://devops-web.vpc-ali.rootcloudapp.com/devops/system/userLog/list>

> docker 构建

docker build -t devops-mds-master\_2021081402 .

> 本地save

docker save -o devops-mds-master\_2021081402 .tar devops-mds-master\_2021081402

> 远程scp : 密码 :  3H3llb58j1\$7NwVU

scp  devops-mds-master\_2021081403.tar root\@123.57.214.149:/root/xzb-devops

> 远程node :

docker tag devops-mds-master\_2021081404 dockerhub.kubekey.local/rootcloud/mds/devops-mds:master\_202108140

```纯文本
kubectl [command] [TYPE] [NAME] [flags]

command`：指定要对一个或多个资源执行的操作，例如 `create`、`get`、`describe`、`delete
TYPE : 资源类型对应: api-resources 
NAME：指定资源的名称。名称区分大小写。

```

1.  查看节点信息

> kubectl get nodes

1.  查看容器的pods和service信息

> kubectl get pod,svc -o wide

1.  创建deployment资源

> kubectl create deployment  nginx --image=nginx

> kubectl expose  deployment nginx --port=80 --type=NodePort     --对外暴露服务

1.  获取api-resources信息

> kubectl get cm

> kubectl get ns/ev /no/ pvc/pv /po /rc/

1.  -o 输出信息

> kubectl \[command] \[TYPE] \[NAME] -o=\<output\_format>

> kubectl  get  pods   名称 -o  wide/json/yaml/name

1.  `kubectl apply` - 以文件或标准输入为准应用或更新资源。

> kubectl apply -f example-service.yaml

1.  `kubectl get` - 列出一个或多个资源。

> kubectl get rc,services

1.  `kubectl describe` - 显示一个或多个资源的详细状态，默认情况下包括未初始化的资源

> kubectl describe pods  pods名称

> kubectl describe node node节点名称

1.  `kubectl delete` - 从文件、stdin 或指定标签选择器、名称、资源选择器或资源中删除资源

> kubectl delete -f  文件.yaml

> kubectl delete pod   pod-name

1.  `kubectl exec` - 对 pod 中的容器执行命令。

> kubectl exec -ti \<pod-name> -- /bin/bash

> kubectl exec  pod-name  -- ls

> kubectl exec -it pod-name -- sh

> kubectl exec pod-name -- env

1.  `kubectl logs` - 打印 Pod 中容器的日志

> kubectl logs -f \<pod-name>

1.  获取资源deployment信息

> kubectl  get  deployment

1.  观察pod状态

> kubectl get pod -w

1.  删除某一个pod

> kubectl get deployment

> kubectl delete  deployment   资源的名称     # 此时同时会删除pod

> kubectl delete  pods   pod的名称    # 在删除后k8s会自动拉取一个新的容器服务

> kubectl delete service -l  标签名=标签值  # 通过标签删除服务

1.  通过label查询pod/services , 添加label

> kubectl get pods  -l  标签名=标签值

> kubectl get  services  -l app=标签

> kubectl label  pod pod名称  标签名=标签值

> 小总结:

> 1 kubectl create deployment #创建一个deployment来管理创建的容器
> 2 kubectl get #显示一个或多个资源，可以使用标签过滤，默认查看当前名称空间的资源
> 3 kubectl expose #将一个资源暴露为一个新的kubernetes的service资源，资源包括pod (po)， service (svc)，
> replicationcontroller (rc)，deployment(deploy)， replicaset (rs)
> 4 kubectl describe #显示特定资源或资源组的详细信息
> 5 kubectl scale #可以对Deployment, ReplicaSet, Replication Controller, 或者StatefulSet设置新的值，可
> 以指定一个或多个先决条件
> 6 kubectl set #更改现有的应用程序资源
> 7 kubectl rollout #资源回滚管理

> 16） Ingress,类似NGINX代理转发

> kubectl get ing

1.  排查服务失败问题

> kubectl  describe pods  pod名称

> [https://github.com/fabric8io/kubernetes-client](https://github.com/fabric8io/kubernetes-client "https://github.com/fabric8io/kubernetes-client")

| kubectl                                                                          | Fabric8 Kubernetes Client                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| -------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `kubectl config view`                                                            | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigViewEquivalent.java)[ConfigViewEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigViewEquivalent.java "ConfigViewEquivalent.java")                                                                     |
| `kubectl config get-contexts`                                                    | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigGetContextsEquivalent.java)[ConfigGetContextsEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigGetContextsEquivalent.java "ConfigGetContextsEquivalent.java")                                         |
| `kubectl config current-context`                                                 | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigGetCurrentContextEquivalent.java)[ConfigGetCurrentContextEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigGetCurrentContextEquivalent.java "ConfigGetCurrentContextEquivalent.java")                 |
| `kubectl config use-context minikube`                                            | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigUseContext.java)[ConfigUseContext.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigUseContext.java "ConfigUseContext.java")                                                                                     |
| `kubectl config view -o jsonpath='{.users[*].name}'`                             | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigGetCurrentContextEquivalent.java)[ConfigGetCurrentContextEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigGetCurrentContextEquivalent.java "ConfigGetCurrentContextEquivalent.java")                 |
| `kubectl get pods --all-namespaces`                                              | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListGlobalEquivalent.java)[PodListGlobalEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListGlobalEquivalent.java "PodListGlobalEquivalent.java")                                                         |
| `kubectl get pods`                                                               | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListEquivalent.java)[PodListEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListEquivalent.java "PodListEquivalent.java")                                                                                 |
| `kubectl get pods -w`                                                            | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodWatchEquivalent.java)[PodWatchEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodWatchEquivalent.java "PodWatchEquivalent.java")                                                                             |
| `kubectl get pods --sort-by='.metadata.creationTimestamp'`                       | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListGlobalEquivalent.java)[PodListGlobalEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListGlobalEquivalent.java "PodListGlobalEquivalent.java")                                                         |
| `kubectl run`                                                                    | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodRunEquivalent.java)[PodRunEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodRunEquivalent.java "PodRunEquivalent.java")                                                                                     |
| `kubectl create -f test-pod.yaml`                                                | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodCreateYamlEquivalent.java)[PodCreateYamlEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodCreateYamlEquivalent.java "PodCreateYamlEquivalent.java")                                                         |
| `kubectl exec my-pod -- ls /`                                                    | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodExecEquivalent.java)[PodExecEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodExecEquivalent.java "PodExecEquivalent.java")                                                                                 |
| `kubectl delete pod my-pod`                                                      | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodDelete.java)[PodDelete.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodDelete.java "PodDelete.java")                                                                                                                 |
| `kubectl delete -f test-pod.yaml`                                                | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodDeleteViaYaml.java)[PodDeleteViaYaml.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodDeleteViaYaml.java "PodDeleteViaYaml.java")                                                                                     |
| `kubectl cp /foo_dir my-pod:/bar_dir`                                            | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/UploadDirectoryToPod.java)[UploadDirectoryToPod.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/UploadDirectoryToPod.java "UploadDirectoryToPod.java")                                                                     |
| `kubectl cp my-pod:/tmp/foo /tmp/bar`                                            | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/DownloadFileFromPod.java)[DownloadFileFromPod.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/DownloadFileFromPod.java "DownloadFileFromPod.java")                                                                         |
| `kubectl cp my-pod:/tmp/foo -c c1 /tmp/bar`                                      | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/DownloadFileFromMultiContainerPod.java)[DownloadFileFromMultiContainerPod.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/DownloadFileFromMultiContainerPod.java "DownloadFileFromMultiContainerPod.java")                 |
| `kubectl cp /foo_dir my-pod:/tmp/bar_dir`                                        | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/UploadFileToPod.java)[UploadFileToPod.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/UploadFileToPod.java "UploadFileToPod.java")                                                                                         |
| `kubectl logs pod/my-pod`                                                        | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodLogsEquivalent.java)[PodLogsEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodLogsEquivalent.java "PodLogsEquivalent.java")                                                                                 |
| `kubectl logs pod/my-pod -f`                                                     | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodLogsFollowEquivalent.java)[PodLogsFollowEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodLogsFollowEquivalent.java "PodLogsFollowEquivalent.java")                                                         |
| `kubectl logs pod/my-pod -c c1`                                                  | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodLogsMultiContainerEquivalent.java)[PodLogsMultiContainerEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodLogsMultiContainerEquivalent.java "PodLogsMultiContainerEquivalent.java")                         |
| `kubectl port-forward my-pod 8080:80`                                            | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PortForwardEquivalent.java)[PortForwardEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PortForwardEquivalent.java "PortForwardEquivalent.java")                                                                 |
| `kubectl get pods --selector=version=v1 -o jsonpath='{.items[*].metadata.name}'` | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListFilterByLabel.java)[PodListFilterByLabel.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListFilterByLabel.java "PodListFilterByLabel.java")                                                                     |
| `kubectl get pods --field-selector=status.phase=Running`                         | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListFilterFieldSelector.java)[PodListFilterFieldSelector.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodListFilterFieldSelector.java "PodListFilterFieldSelector.java")                                             |
| `kubectl get pods --show-labels`                                                 | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodShowLabels.java)[PodShowLabels.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodShowLabels.java "PodShowLabels.java")                                                                                                 |
| `kubectl label pods my-pod new-label=awesome`                                    | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodAddLabel.java)[PodAddLabel.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodAddLabel.java "PodAddLabel.java")                                                                                                         |
| `kubectl annotate pods my-pod icon-url=http://goo.gl/XXBTWq`                     | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodAddAnnotation.java)[PodAddAnnotation.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/PodAddAnnotation.java "PodAddAnnotation.java")                                                                                     |
| `kubectl get configmap cm1 -o jsonpath='{.data.database}'`                       | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigMapJsonPathEquivalent.java)[ConfigMapJsonPathEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ConfigMapJsonPathEquivalent.java "ConfigMapJsonPathEquivalent.java")                                         |
| `kubectl create -f test-svc.yaml`                                                | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/LoadAndCreateService.java)[LoadAndCreateService.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/LoadAndCreateService.java "LoadAndCreateService.java")                                                                     |
| `kubectl create -f test-deploy.yaml`                                             | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/LoadAndCreateDeployment.java)[LoadAndCreateDeployment.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/LoadAndCreateDeployment.java "LoadAndCreateDeployment.java")                                                         |
| `kubectl set image deploy/d1 nginx=nginx:v2`                                     | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutSetImageEquivalent.java)[RolloutSetImageEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutSetImageEquivalent.java "RolloutSetImageEquivalent.java")                                                 |
| `kubectl scale --replicas=4 deploy/nginx-deployment`                             | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ScaleEquivalent.java)[ScaleEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/ScaleEquivalent.java "ScaleEquivalent.java")                                                                                         |
| `kubectl rollout restart deploy/d1`                                              | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutRestartEquivalent.java)[RolloutRestartEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutRestartEquivalent.java "RolloutRestartEquivalent.java")                                                     |
| `kubectl rollout pause deploy/d1`                                                | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutPauseEquivalent.java)[RolloutPauseEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutPauseEquivalent.java "RolloutPauseEquivalent.java")                                                             |
| `kubectl rollout resume deploy/d1`                                               | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutResumeEquivalent.java)[RolloutResumeEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutResumeEquivalent.java "RolloutResumeEquivalent.java")                                                         |
| `kubectl rollout undo deploy/d1`                                                 | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutUndoEquivalent.java)[RolloutUndoEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/RolloutUndoEquivalent.java "RolloutUndoEquivalent.java")                                                                 |
| `kubectl create -f test-crd.yaml`                                                | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/LoadAndCreateCustomResourceDefinition.java)[LoadAndCreateCustomResourceDefinition.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/LoadAndCreateCustomResourceDefinition.java "LoadAndCreateCustomResourceDefinition.java") |
| `kubectl create -f customresource.yaml`                                          | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CustomResourceCreateDemo.java)[CustomResourceCreateDemo.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CustomResourceCreateDemo.java "CustomResourceCreateDemo.java")                                                     |
| `kubectl create -f customresource.yaml`                                          | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CustomResourceCreateDemoTypeless.java)[CustomResourceCreateDemoTypeless.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CustomResourceCreateDemoTypeless.java "CustomResourceCreateDemoTypeless.java")                     |
| `kubectl get ns`                                                                 | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/NamespaceListEquivalent.java)[NamespaceListEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/NamespaceListEquivalent.java "NamespaceListEquivalent.java")                                                         |
| `kubectl apply -f test-resource-list.yml`                                        | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CreateOrReplaceResourceList.java)[CreateOrReplaceResourceList.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CreateOrReplaceResourceList.java "CreateOrReplaceResourceList.java")                                         |
| `kubectl get events`                                                             | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/EventsGetEquivalent.java)[EventsGetEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/EventsGetEquivalent.java "EventsGetEquivalent.java")                                                                         |
| `kubectl top nodes`                                                              | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/TopEquivalent.java)[TopEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/TopEquivalent.java "TopEquivalent.java")                                                                                                 |
| `kubectl auth can-i create deployment.apps`                                      | [](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CanIEquivalent.java)[CanIEquivalent.java](https://github.com/fabric8io/kubernetes-client/blob/master/kubernetes-examples/src/main/java/io/fabric8/kubernetes/examples/kubectl/equivalents/CanIEquivalent.java "CanIEquivalent.java")                                                                                             |

### 案列

> 准备工作:

1.  我现在已经有一个可用的k8s集群, 并且安装了 kubectl 工具
2.  现在有一个java-app.jar包
    要求完成内容
    1.使用Docker完成进行镜像打包上传
    2.创建一个namespace
    3.创建ingress 使用 域名映射
    4.创建Config 配置java-app 的环境变量内容,比如 MESSAGE: zh-CN
    5.创建Service 配置标签,负载均衡
    6.创建Deployment 部署pod, 关联Service
    7.浏览器访问http:xxx, 可以进行前后端交互
3.  基于以上内容, 请给出详细步骤,以及关键配置的详细注释内容,比如 域名与ingress映射关系,ingress与service与pod与config等之间的关系

> 好的，我会尽力帮助您完成这些任务。以下是详细步骤和注释：

1.  使用 Docker 完成进行镜像打包上传

> 首先，将 Java 应用程序打包成 Docker 镜像，然后上传到 Docker 镜像仓库以便 Kubernetes 部署。

-   使用 Dockerfile 创建 Docker 镜像

> Dockerfile 是一个文本文件，它包含一组指令来告诉 Docker 如何构建镜像。在此示例中，我们将使用 Dockerfile 来构建 Java 应用程序的镜像。

> Copy

```纯文本
FROM openjdk:8-jdk-alpine
COPY java-app.jar /app.jar
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]
```

> 上面的 Dockerfile 指令：

-   使用 openjdk:8-jdk-alpine 作为基础镜像，它是一个轻量级的基础镜像，预装了 OpenJDK 8 和 Alpine Linux 操作系统。
-   将 java-app.jar 拷贝到容器中的 /app.jar 目录下。
-   配置容器的启动命令，即运行 java -jar /app.jar。
-   构建 Docker 镜像并上传到 Docker 镜像仓库

> 在构建 Docker 镜像之前，您需要确保 Docker 已经正确安装并登录到 Docker 镜像仓库。下面是构建和上传 Docker 镜像的命令：

> Copy

```纯文本
# 构建 Docker 镜像
docker build -t your-docker-repo/java-app:1.0 .

# 上传 Docker 镜像到 Docker 镜像仓库
docker push your-docker-repo/java-app:1.0
```

1.  创建一个 namespace

> 在 Kubernetes 中，namespace 用于隔离不同的资源和团队。以下是创建 namespace 的命令：

> Copy

```纯文本
kubectl create namespace your-namespace
```

1.  创建 Ingress 使用域名映射

> Ingress 是一种 Kubernetes 资源，它允许将外部流量路由到 Kubernetes 集群内部的服务。在此示例中，我们将使用 Ingress 将域名映射到我们的服务。以下是创建 Ingress 的命令：

> angelscript

> Copy

```纯文本
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: your-ingress
  namespace: your-namespace
  annotations:
    # 使用 nginx 作为 Ingress 控制器
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: your-domain.com # 修改为您自己的域名
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: your-service
            port:
              name: http
```

> 上面的 Ingress 配置中：

-   使用 nginx 作为 Ingress 控制器。
-   将域名 your-domain.com 映射到 Kubernetes 集群中的 your-service 服务。

> 注意：您需要将 your-domain.com 替换为您自己的域名。

1.  创建 Config 配置 Java 应用程序的环境变量内容

> ConfigMap 是 Kubernetes 的一种资源类型，它用于管理配置数据。在此示例中，我们将使用 ConfigMap 来配置 Java 应用程序的环境变量内容。以下是创建 ConfigMap 的命令：

> Copy

```纯文本
apiVersion: v1
kind: ConfigMap
metadata:
  name: your-config
  namespace: your-namespace
data:
  MESSAGE: zh-CN
```

> 上面的 ConfigMap 配置中：

-   创建了一个名为 your-config 的 ConfigMap。
-   在 ConfigMap 中定义了一个 MESSAGE 环境变量，它的值为 zh-CN。

1.  创建 Service 配置标签，负载均衡

> Service 是 Kubernetes 的一种资源类型，它用于将流量路由到 Kubernetes 集群中的容器。在此示例中，我们将使用 Service 来配置标签和负载均衡。以下是创建 Service 的命令：

> angelscript

> Copy

```纯文本
apiVersion: v1
kind: Service
metadata:
  name: your-service
  namespace: your-namespace
spec:
  selector:
    app: your-app
  ports:
  - name: http
    port: 80
    targetPort: 8080
  type: LoadBalancer
```

> 上面的 Service 配置中：

-   创建了一个名为 your-service 的 Service。
-   Service 的 selector 标签选择器指向了带有 app=your-app 标签的 Pod。
-   Service 的端口为 80，目标端口为 8080。
-   在 Service 中设置了负载均衡器，以便将流量路由到 Kubernetes 集群中的多个 Pod 上。

1.  创建 Deployment 部署 Pod，关联 Service

> Deployment 是 Kubernetes 的一种资源类型，它用于部署和管理 Pod。在此示例中，我们将使用 Deployment 部署 Pod 并将其与 Service 关联。以下是创建 Deployment 的命令：

> angelscript

> Copy

```纯文本
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-deployment
  namespace: your-namespace
spec:
  replicas: 3
  selector:
    matchLabels:
      app: your-app
  template:
    metadata:
      labels:
        app: your-app
    spec:
      containers:
      - name: your-container
        image: your-docker-repo/java-app:1.0
        ports:
        - name: http
          containerPort: 8080
        envFrom:
        - configMapRef:
            name: your-config
```

> 上面的 Deployment 配置中：

-   创建了一个名为 your-deployment 的 Deployment。
-   Deployment 中的 replicas 选项指定了要创建的 Pod 数量。
-   Deployment 的 selector 标签选择器指向了带有 app=your-app 标签的 Pod。
-   在 Pod 模板中定义了一个名为 your-container 的容器。
-   容器的镜像为 your-docker-repo/java-app:1.0。
-   容器的端口为 8080。
-   在容器中设置了 MESSAGE 环境变量，它的值来自 ConfigMap 中的 MESSAGE 环境变量。

1.  浏览器访问 [http://your-domain.com，可以进行前后端交互](http://your-domain.xn--com%2C-ju5fudou68tp3d6ii988a3r7aysu/ "http://your-domain.com，可以进行前后端交互")

> 在完成上述步骤后，您可以在浏览器中访问您的域名 [http://your-domain.com，以与部署的](http://your-domain.xn--com%2C-ob5fn8bwy1or6oo70b/ "http://your-domain.com，以与部署的") Java 应用程序进行前后端交互。

> 注意：您需要将 your-domain.com 替换为您自己的域名。
