Weave Scope是Docker和Kubernetes的可视化和监控工具。它为您的应用程序以及整个基础架构提供了一个自顶向下的视图，并允许您在部署到云提供商时实时诊断分布式集装箱化应用程序的任何问题。

kubectl apply -n weave -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"  #这里可能出错,如果出错的话按照下面的来执行


1.echo "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
2.将输出的网址输入到本地的浏览器并下载对应的scope.yaml文件上传至server
3.执行kubectl apply -f scope.yaml -n weave
4.编辑kubectl edit svc -n weave,将里面的type: ClusterIP改为type: NodePort
5.kubectl get svc -n weave查看生成的NodePort,浏览器打开访问即可


---------------------------------------------------
安装插件:

1.scope-http-statistics  监控http连接,暂未测试

1)安装要求:

kernel version >=4.2
Attaching to kprobes requires >=4.1.
bpf_probe_read() requires >=4.1.
bpf_get_current_pid_tgid() requires >=4.2.
Kernel build directory to be available in /lib/modules/<kernel-version>/build. Depending on your distribution you might need to add this symlink: ln -s /lib/modules/<kernel-version>/build /lib/modules/<kernel-version>/source.
The Scope HTTP Statistics plugin can be executed stand alone. It will respond to GET /report request on the /var/run/scope/plugins/http-statistics/http-statistics.sock in a JSON format. If the running plugin has been registered by Scope, you will see it in the list of PLUGINS in the bottom right of the UI (see the green rectangle in the above figure). The measured values are shown in the STATUS section (see the circle in the above figure).

2)提前下载好镜像
docker pull weaveworksplugins/scope-http-statistics:latest

3)执行
kubectl create -n weave -f https://raw.githubusercontent.com/weaveworks-plugins/scope-http-statistics/master/deployments/k8s-http-statistics.yaml

------------------------------------------------
2.Scope Traffic Control插件允许使用Weave Scope修改容器网络接口的性能参数。

for i in `seq 2 10`;do 
    IP=172.16.20.$i
    ssh $IP modprobe sch_netem
    scp -r /root/weave $IP:/root/
    for m in `ls /root/weave`;do ssh $IP docker load -i /root/weave/$m;done
    echo $IP'----------------------------------------------------done'
done


1)执行
kubectl apply -n weave -f k8s-traffic-control.yaml

--------------------------------------------
3.Scope IOWait Plugin --The Scope IOWait plugin is a GO application that uses iostat to provide host-level CPU IO wait or idle metrics in the Weave Scope UI.


1)kubectl apply -n weave -f k8s-iowait.yaml
