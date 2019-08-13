# kubernets理解
> 他的作用是采用分布式的方式来管理多个docker集群。

- 从分布式架构来分析
	1. 首先就是master，用来管理worker。管理的工具就是需要用到	`scheduler` `controller manager`。
	2. 服务自动发现需要使用到`etcd`。
	3. worker就是实际跑任务的，在docker中就是`node`，通过`kubelet`去调用`pod`。

## ops角度学习kubernets
	
- 安装master，会起三个进程：`kube-apiserver，kube-controller-manager，kube-scheduler`。
- 安装worker节点：`kubelet，kube-proxy`。
- 通过抽象系统状态来管理系统，基本的抽象包括：`pod，service，volume，namespace`，高级抽象：`replicaset，deployment，statefulset，daemonset，job`

