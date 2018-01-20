Weave Scope��Docker��Kubernetes�Ŀ��ӻ��ͼ�ع��ߡ���Ϊ����Ӧ�ó����Լ����������ܹ��ṩ��һ���Զ����µ���ͼ�����������ڲ������ṩ��ʱʵʱ��Ϸֲ�ʽ��װ�仯Ӧ�ó�����κ����⡣

kubectl apply -n weave -f "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"  #������ܳ���,�������Ļ������������ִ��


1.echo "https://cloud.weave.works/k8s/scope.yaml?k8s-version=$(kubectl version | base64 | tr -d '\n')"
2.���������ַ���뵽���ص�����������ض�Ӧ��scope.yaml�ļ��ϴ���server
3.ִ��kubectl apply -f scope.yaml -n weave
4.�༭kubectl edit svc -n weave,�������type: ClusterIP��Ϊtype: NodePort
5.kubectl get svc -n weave�鿴���ɵ�NodePort,������򿪷��ʼ���


---------------------------------------------------
��װ���:

1.scope-http-statistics  ���http����,��δ����

1)��װҪ��:

kernel version >=4.2
Attaching to kprobes requires >=4.1.
bpf_probe_read() requires >=4.1.
bpf_get_current_pid_tgid() requires >=4.2.
Kernel build directory to be available in /lib/modules/<kernel-version>/build. Depending on your distribution you might need to add this symlink: ln -s /lib/modules/<kernel-version>/build /lib/modules/<kernel-version>/source.
The Scope HTTP Statistics plugin can be executed stand alone. It will respond to GET /report request on the /var/run/scope/plugins/http-statistics/http-statistics.sock in a JSON format. If the running plugin has been registered by Scope, you will see it in the list of PLUGINS in the bottom right of the UI (see the green rectangle in the above figure). The measured values are shown in the STATUS section (see the circle in the above figure).

2)��ǰ���غþ���
docker pull weaveworksplugins/scope-http-statistics:latest

3)ִ��
kubectl create -n weave -f https://raw.githubusercontent.com/weaveworks-plugins/scope-http-statistics/master/deployments/k8s-http-statistics.yaml

------------------------------------------------
2.Scope Traffic Control�������ʹ��Weave Scope�޸���������ӿڵ����ܲ�����

for i in `seq 2 10`;do 
    IP=172.16.20.$i
    ssh $IP modprobe sch_netem
    scp -r /root/weave $IP:/root/
    for m in `ls /root/weave`;do ssh $IP docker load -i /root/weave/$m;done
    echo $IP'----------------------------------------------------done'
done


1)ִ��
kubectl apply -n weave -f k8s-traffic-control.yaml

--------------------------------------------
3.Scope IOWait Plugin --The Scope IOWait plugin is a GO application that uses iostat to provide host-level CPU IO wait or idle metrics in the Weave Scope UI.


1)kubectl apply -n weave -f k8s-iowait.yaml
