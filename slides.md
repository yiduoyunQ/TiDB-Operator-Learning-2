---
theme: academic
transition: fade
coverAuthor: Jirong Qiu
exportFilename: TiDB-Operator-Learning-2
hideInToc: true
lineNumbers: true
titleTemplate: '%s • Jirong Qiu'
---

# TiDB-Operator Learning 2

---

# Agenda

- 部署

---
layout: figure
figureCaption: 
figureUrl: ./version_relationship.png
---

# [TiDB Operator 与适用的 TiDB 版本的对应关系如下](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/tidb-operator-overview/)

---

# CRD

- `TidbCluster` 用于描述用户期望的 TiDB 集群
- `TidbMonitor` 用于描述用户期望的 TiDB 集群监控组件
- `TidbInitializer` 用于描述用户期望的 TiDB 集群初始化 Job
- `Backup` 用于描述用户期望的 TiDB 集群备份
- `Restore` 用于描述用户期望的 TiDB 集群恢复
- `BackupSchedule` 用于描述用户期望的 TiDB 集群周期性备份

---

# CRD

```shell
➜  ~ kubectl create -f https://raw.githubusercontent.com/pingcap/tidb-operator/v1.6.1/manifests/crd.yaml
customresourcedefinition.apiextensions.k8s.io/backups.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/backupschedules.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/dmclusters.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/restores.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/tidbclusterautoscalers.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/tidbclusters.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/tidbdashboards.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/tidbinitializers.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/tidbmonitors.pingcap.com created
customresourcedefinition.apiextensions.k8s.io/tidbngmonitorings.pingcap.com created
```

---

# CRD

```shell
➜  ~ kubectl api-resources -o wide | grep -E "NAME|pingcap.com"
NAME                                SHORTNAMES   APIVERSION                        NAMESPACED   KIND                               VERBS                                                        CATEGORIES
backups                             bk           pingcap.com/v1alpha1              true         Backup                             delete,deletecollection,get,list,patch,create,update,watch
backupschedules                     bks          pingcap.com/v1alpha1              true         BackupSchedule                     delete,deletecollection,get,list,patch,create,update,watch
dmclusters                          dc           pingcap.com/v1alpha1              true         DMCluster                          delete,deletecollection,get,list,patch,create,update,watch
restores                            rt           pingcap.com/v1alpha1              true         Restore                            delete,deletecollection,get,list,patch,create,update,watch
tidbclusterautoscalers              ta           pingcap.com/v1alpha1              true         TidbClusterAutoScaler              delete,deletecollection,get,list,patch,create,update,watch
tidbclusters                        tc           pingcap.com/v1alpha1              true         TidbCluster                        delete,deletecollection,get,list,patch,create,update,watch
tidbdashboards                      td           pingcap.com/v1alpha1              true         TidbDashboard                      delete,deletecollection,get,list,patch,create,update,watch
tidbinitializers                    ti           pingcap.com/v1alpha1              true         TidbInitializer                    delete,deletecollection,get,list,patch,create,update,watch
tidbmonitors                        tm           pingcap.com/v1alpha1              true         TidbMonitor                        delete,deletecollection,get,list,patch,create,update,watch
tidbngmonitorings                   tngm         pingcap.com/v1alpha1              true         TidbNGMonitoring                   delete,deletecollection,get,list,patch,create,update,watch
```

---

# CRD

```shell
➜  ~ kubectl get crd
NAME                                 CREATED AT
backups.pingcap.com                  2025-03-17T02:23:29Z
backupschedules.pingcap.com          2025-03-17T02:23:29Z
dmclusters.pingcap.com               2025-03-17T02:23:29Z
restores.pingcap.com                 2025-03-17T02:23:29Z
tidbclusterautoscalers.pingcap.com   2025-03-17T02:23:29Z
tidbclusters.pingcap.com             2025-03-17T02:23:30Z
tidbdashboards.pingcap.com           2025-03-17T02:23:30Z
tidbinitializers.pingcap.com         2025-03-17T02:23:30Z
tidbmonitors.pingcap.com             2025-03-17T02:23:30Z
tidbngmonitorings.pingcap.com        2025-03-17T02:23:30Z
```

---

# CRD

```yaml
➜  ~ kubectl get crd tidbclusters.pingcap.com -o yaml | head -n 20
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  annotations:
    controller-gen.kubebuilder.io/version: v0.15.0
  creationTimestamp: "2025-03-17T02:23:30Z"
  generation: 2
  name: tidbclusters.pingcap.com
  resourceVersion: "1417"
  uid: 8dae5a97-9e0e-412d-a6d5-707372f9ed6d
spec:
  conversion:
    strategy: None
  group: pingcap.com
  names:
    kind: TidbCluster
    listKind: TidbClusterList
    plural: tidbclusters
    shortNames:
    - tc
```

<Arrow v-click="1" x1="650" y1="200" x2="500" y2="250" color="red" />
<div v-click="1" class="absolute top-44 left-25 w-100 h-32 border-3 border-red-500"></div>
<p v-click="1" class="absolute top-40 left-135 opacity-100 transform -rotate-20 text-red-500"> 每次创建都会变！</p>

---

# CRD

```shell
➜  ~ kubectl replace -f https://raw.githubusercontent.com/pingcap/tidb-operator/v1.6.1/manifests/crd.yaml
customresourcedefinition.apiextensions.k8s.io/backups.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/backupschedules.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/dmclusters.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/restores.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/tidbclusterautoscalers.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/tidbclusters.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/tidbdashboards.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/tidbinitializers.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/tidbmonitors.pingcap.com replaced
customresourcedefinition.apiextensions.k8s.io/tidbngmonitorings.pingcap.com replaced
```

---

# tidbclusters

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  ...
spec:
  ...
  pd:
    ...
  tidb:
    ...
  tikv:
    ...
  tiflash:
    ...
  ticdc:
    ...
```

---

# tidbclusters status

```shell
kubectl -n {ns} get tc {name} -o yaml
```

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  ...
spec:
  ...
  pd:
    ...
  ...
status:
  ...
  pd:
    ...
  ...
```

---

# tidbclusters status

```shell
➜  ~ kubectl -n tidb-cluster get tc basic
NAME    READY   PD                                         STORAGE   READY   DESIRE   TIKV                                         STORAGE   READY   DESIRE   TIDB                                         READY   DESIRE   AGE
basic   True    uhub.service.ucloud.cn/pingcap/pd:v8.5.0   1Gi       1       1        uhub.service.ucloud.cn/pingcap/tikv:v8.5.0   1Gi       1       1        uhub.service.ucloud.cn/pingcap/tidb:v8.5.0   1       1        82s
```

<div v-click="1">
```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  name: basic
spec:
  version: v8.5.0
  pd:
    baseImage: uhub.service.ucloud.cn/pingcap/pd
    replicas: 1
    requests:
      storage: "1Gi"
  tikv:
    baseImage: uhub.service.ucloud.cn/pingcap/tikv
    replicas: 1
    requests:
      storage: "1Gi"
  tidb:
    baseImage: uhub.service.ucloud.cn/pingcap/tidb
    replicas: 1
```
</div>

<!-- meta name -->
<Arrow v-click="1" x1="180" y1="230" x2="120" y2="160" color="red" />

<!-- version -->
<Arrow v-click="1" x1="210" y1="270" x2="480" y2="160" color="red" />

<!-- image -->
<Arrow v-click="1" x1="300" y1="305" x2="300" y2="160" color="red" />

<!-- storage -->
<Arrow v-click="1" x1="240" y1="360" x2="530" y2="160" color="red" />

<!-- replica -->
<Arrow v-click="1" x1="215" y1="320" x2="600" y2="160" color="red" />

---

# tidbclusters pod

```shell
➜  ~ kubectl -n tidb-cluster get po
NAME                               READY   STATUS    RESTARTS   AGE
basic-discovery-5bb557b88c-wp9q4   1/1     Running   0          25m
basic-pd-0                         1/1     Running   0          25m
basic-tidb-0                       2/2     Running   0          22m
basic-tikv-0                       1/1     Running   0          25m
```

---

# tidbclusters pd

```yaml
➜  ~ kubectl -n tidb-cluster get po basic-pd-0 -o yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app.kubernetes.io/component: pd
    app.kubernetes.io/instance: basic
    app.kubernetes.io/managed-by: tidb-operator
    app.kubernetes.io/name: tidb-cluster
```

```shell
➜  ~ kubectl -n tidb-cluster get all -l app.kubernetes.io/instance=basic,app.kubernetes.io/component=pd
NAME             READY   STATUS    RESTARTS   AGE
pod/basic-pd-0   1/1     Running   0          19m

NAME                    TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)             AGE
service/basic-pd        ClusterIP   10.96.130.129   <none>        2379/TCP            19m
service/basic-pd-peer   ClusterIP   None            <none>        2380/TCP,2379/TCP   19m

NAME                        READY   AGE
statefulset.apps/basic-pd   1/1     19m
```

---

# tidbclusters pd

```shell
➜  ~ kubectl -n tidb-cluster get pvc -l app.kubernetes.io/instance=basic,app.kubernetes.io/component=pd
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
pd-basic-pd-0   Bound    pvc-af028da8-d2a0-4b8e-b390-636870507ebd   1Gi        RWO            standard       <unset>                 22m

➜  ~ kubectl get pv -l app.kubernetes.io/instance=basic,app.kubernetes.io/component=pd
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                        STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pvc-af028da8-d2a0-4b8e-b390-636870507ebd   1Gi        RWO            Retain           Bound    tidb-cluster/pd-basic-pd-0   standard       <unset>                          20m
```

---

# tidbclusters pd statefulset

```shell
➜  ~ kubectl -n tidb-cluster get sts -l app.kubernetes.io/instance=basic,app.kubernetes.io/component=pd
NAME       READY   AGE
basic-pd   1/1     24m
```

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  name: basic
spec:
  pd:
    replicas: 1
```

<!-- replica -->
<Arrow x1="208" y1="288" x2="200" y2="160" color="red" />

---

# tidbclusters service

```shell
➜  ~ kubectl -n tidb-cluster get svc
NAME              TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)               AGE
basic-discovery   ClusterIP   10.96.136.30    <none>        10261/TCP,10262/TCP   31m
basic-pd          ClusterIP   10.96.130.129   <none>        2379/TCP              31m
basic-pd-peer     ClusterIP   None            <none>        2380/TCP,2379/TCP     31m
basic-tidb        ClusterIP   10.96.19.147    <none>        4000/TCP,10080/TCP    30m
basic-tidb-peer   ClusterIP   None            <none>        10080/TCP             30m
basic-tikv-peer   ClusterIP   None            <none>        20160/TCP             31m
```

- [headless service](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services)

---

# tidbclusters service

```shell
➜  ~ kubectl -n tidb-cluster get po -o wide -l app.kubernetes.io/instance=basic,app.kubernetes.io/component=pd
NAME         READY   STATUS    RESTARTS   AGE   IP            NODE                 NOMINATED NODE   READINESS GATES
basic-pd-0   1/1     Running   0          36m   10.244.0.24   kind-control-plane   <none>           <none>
```

```shell
➜  ~ kubectl -n tidb-cluster exec -it basic-tidb-0 -- sh
/ # ping basic-pd-0.basic-pd-peer.tidb-cluster.svc.cluster.local
PING basic-pd-0.basic-pd-peer.tidb-cluster.svc.cluster.local (10.244.0.24): 56 data bytes
64 bytes from 10.244.0.24: seq=0 ttl=63 time=0.143 ms

/ # ping basic-pd-0.basic-pd-peer.tidb-cluster
PING basic-pd-0.basic-pd-peer.tidb-cluster (10.244.0.24): 56 data bytes
64 bytes from 10.244.0.24: seq=0 ttl=63 time=0.169 ms

/ # ping basic-pd-0.basic-pd-peer
PING basic-pd-0.basic-pd-peer (10.244.0.24): 56 data bytes
64 bytes from 10.244.0.24: seq=0 ttl=63 time=0.150 ms

/ # curl http://basic-pd:2379/health
{"health":"true","reason":""}

/ # exit
```

---

# tidbclusters config

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  name: basic
spec:
  pd:
    config: |
      [log]
        level = "info"
```

```shell
➜  ~ kubectl -n tidb-cluster get cm -l app.kubernetes.io/instance=basic,app.kubernetes.io/component=pd
NAME               DATA   AGE
basic-pd-3230666   2      7s
basic-pd-6130373   2      46m
```

---

# tidbclusters config

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  name: basic
spec:
  pd:
    config: {}
```

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  name: basic
spec:
  pd:
    config: ""
```

---

# tidbclusters config

without config

```shell
➜  ~ kubectl -n tidb-cluster describe po basic-pd-0

Events:
  Type     Reason       Age              From               Message
  ----     ------       ----             ----               -------
  Normal   Scheduled    8s               default-scheduler  Successfully assigned tidb-cluster/basic-pd-0 to kind-control-plane
  Warning  FailedMount  4s (x4 over 7s)  kubelet            MountVolume.SetUp failed for volume "config" : configmap "basic-pd" not found
  Warning  FailedMount  4s (x4 over 7s)  kubelet            MountVolume.SetUp failed for volume "startup-script" : configmap "basic-pd" not found
```

---

# tidbclusters version

```yaml
apiVersion: pingcap.com/v1alpha1
kind: TidbCluster
metadata:
  name: basic
spec:
  version: v8.5.0
  pd:
    version: v8.5.0-hotfix
  tikv:
    version: v8.5.1
  tidb:
```

---

# tidbclusters version

- [在 ARM64 机器上部署 TiDB 集群](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/deploy-cluster-on-arm64/)

![Alt text](/8.5.1.png)


---
layout: figure
figureCaption: 
figureUrl: /version_check.png
---

# tidbclusters version

---

# tidbclusters storageclass

```yaml
spec:
  pd:
    storageClassName: gp3
  tikv:
    storageClassName: gp3-tikv
```

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: gp3-tikv
provisioner: ebs.csi.aws.com
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
parameters:
  type: gp3
  fsType: ext4
  iops: "8000"
  throughput: "800"
mountOptions:
  - nodelalloc
  - noatime
```

---

# tidbclusters pvReclaimPolicy

```yaml
spec
  pvReclaimPolicy: Retain // Delete
```

```
➜  ~ kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                            STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pvc-4763034a-bb1e-4e8a-a427-c67d09489120   1Gi        RWO            Retain           Bound    tidb-cluster/tikv-basic-tikv-0   standard       <unset>                          119s
pvc-bbcfad27-dc57-4d67-8ee6-6b364beac11b   1Gi        RWO            Retain           Bound    tidb-cluster/pd-basic-pd-0       standard       <unset>                          2m37s
```

---

# [tidbclusters 多盘挂载](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/configure-a-tidb-cluster/#%E5%A4%9A%E7%9B%98%E6%8C%82%E8%BD%BD)

```yaml
tikv:
    ...
    config: |
      [rocksdb]
        wal-dir = "/data_sbi/tikv/wal"
      [titan]
        dirname = "/data_sbj/titan/data"
    storageVolumes:
    - name: wal
      storageSize: "2Gi"
      mountPath: "/data_sbi/tikv/wal"
    - name: titan
      storageSize: "2Gi"
      mountPath: "/data_sbj/titan/data"
```

![Alt text](/storageVolume.png)

---

# [tidbclusters 多盘挂载](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/configure-a-tidb-cluster/#%E5%A4%9A%E7%9B%98%E6%8C%82%E8%BD%BD)

[Separate RAFT-log migration](https://pingcap.feishu.cn/wiki/X1qHwbHIgiBU0ZkdD8Vcwnq1n1d)

---

# tidbclusters RollingUpdate

```yaml
spec
  configUpdateStrategy: RollingUpdate // InPlace
```

---

# [tidbclusters TLS](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/enable-tls-between-components/)

[将非 TLS 集群升级为 TLS 集群](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/enable-tls-between-components/#%E5%B0%86%E9%9D%9E-tls-%E9%9B%86%E7%BE%A4%E5%8D%87%E7%BA%A7%E4%B8%BA-tls-%E9%9B%86%E7%BE%A4)

---

# tidbclusters nodeSelector

```yaml
spec:
  pd:
    nodeSelector:
      node-role.kubernetes.io/pd: true
```

---

# tidbclusters taints&tolerations

```yaml
nodeGroups:
- name: pd-1a
    desiredCapacity: 1
    privateNetworking: true
    availabilityZones: ["ap-northeast-1a"]
    instanceType: c7g.xlarge
    labels:
      dedicated: pd
    taints:
      dedicated: pd:NoSchedule
    iam:
      withAddonPolicies:
        ebs: true
```

```yaml
spec:
  pd:
    tolerations:
      - effect: NoSchedule
        key: dedicated
        operator: Equal
        value: pd
```

---

# tidbclusters Affinity & AntiAffinity

```yaml
spec:
  pd:
    affinity:
      podAntiAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
        - labelSelector:
            matchExpressions:
            - key: app.kubernetes.io/component
              operator: In
              values:
              - pd
          topologyKey: kubernetes.io/hostname
```

---

# [tidbclusters topologySpreadConstraints](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/configure-a-tidb-cluster/#%E9%80%9A%E8%BF%87-topologyspreadconstraints-%E5%AE%9E%E7%8E%B0-pod-%E5%9D%87%E5%8C%80%E5%88%86%E5%B8%83)

```yaml
spec:
  topologySpreadConstraints:
  - topologyKey: kubernetes.io/hostname
  - topologyKey: topology.kubernetes.io/zone
```

---

# [tidbclusters 数据的高可用](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/configure-a-tidb-cluster/#%E6%95%B0%E6%8D%AE%E7%9A%84%E9%AB%98%E5%8F%AF%E7%94%A8)

![Alt text](/label_ha.png)

---

# tidbclusters limit

```yaml
spec:
  pd:
    requests:
      cpu: 4
      memory: 16Gi
    limits:
      cpu: 4
      memory: 16Gi
```

[Guaranteed QoS](https://kubernetes.io/docs/concepts/workloads/pods/pod-qos/#guaranteed)

[Resource units in Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)

[Node Static CPU policy](https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/) for NUMA

---

# tidbclusters Discovery

```yaml
spec:
  discovery:
    limits:
      cpu: "0.2"
    requests:
      cpu: "0.2"
  ...
```

--- 

# tidbclusters tidb service

```yaml
spec:
  tidb:
    service:
      type: ClusterIP
```

```yaml
spec:
  tidb:
    service:
      type: NodePort
      # externalTrafficPolicy: Local
```

---

# tidbclusters tidb service

```yaml
spec:
  tidb:
    service:
      type: LoadBalancer
      exposeStatus: true  // 10080
      externalTrafficPolicy: Local
      annotations:
        service.beta.kubernetes.io/aws-load-balancer-cross-zone-load-balancing-enabled: 'true'
        service.beta.kubernetes.io/aws-load-balancer-type: nlb
        service.beta.kubernetes.io/aws-load-balancer-scheme: internal
        service.beta.kubernetes.io/aws-load-balancer-internal: "true"
        service.beta.kubernetes.io/aws-load-balancer-target-node-labels: dedicated=tidb
```

[externalTrafficPolicy](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/#preserving-the-client-source-ip)

---

# tidbclusters IPv6

```yaml
spec:
  preferIPv6: true
  # ...
```

![Alt text](/IPv6.png)

---

# tidbclusters 异构集群

```yaml
metadata:
  name: ${heterogeneous_cluster_name}
spec:
  configUpdateStrategy: RollingUpdate
  version: v8.5.0
  pvReclaimPolicy: Retain
  discovery: {}
  cluster:
    name: ${origin_cluster_name}
  tikv:
    baseImage: pingcap/tikv
    replicas: 3
    # storageClassName: gp3-tikv
    requests:
      storage: "100Gi"
    config: {}
```

---
layout: figure
figureCaption: 
figureUrl: /heterogeneous1.png
---

# tidbclusters 异构集群

---
layout: figure
figureCaption: 
figureUrl: /heterogeneous2.png
---

# tidbclusters 异构集群

---

# [tidbclusters 跨 Kubernetes 集群](https://docs.pingcap.com/zh/tidb-in-kubernetes/stable/build-multi-aws-eks/)

```yaml
metadata:
  name: ${tc_name_1}
  namespace: ${namespace_1}
spec:
  clusterDomain: "cluster.local"
  acrossK8s: true
```

```yaml
metadata:
  name: "${tc_name_2}"
  namespace: ${namespace_2}
spec:
  clusterDomain: "cluster.local"
  acrossK8s: true
  cluster:
    name: "${tc_name_1}"
    namespace: "${namespace_1}"
    clusterDomain: "cluster.local"
```

---
layout: figure
figureCaption: 
figureUrl: /heterogeneous3.png
---

# tidbclusters [在三数据中心下就近读取数据](https://docs.pingcap.com/zh/tidb/stable/three-dc-local-read/)

