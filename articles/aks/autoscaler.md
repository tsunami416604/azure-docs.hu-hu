---
title: Az Azure - fürt méretező Kubernetes
description: Megtudhatja, hogyan, hogy megfeleljenek az igényeknek fürtök automatikus méretezése az Azure Kubernetes Service (AKS) fürt automatikus méretező használatához.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 8431181c1f3d5fbe31fa6c96303367ee71f83b17
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480458"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Méretező fürt az Azure Kubernetes Service (AKS) – előzetes verzió

Az Azure Kubernetes Service (AKS) az Azure-ban felügyelt Kubernetes-fürt üzembe helyezése rugalmas megoldást kínál. Erőforrásként a növekvő igények szerint növelje a fürt méretező lehetővé teszi, hogy a fürt, hogy a beállított korlátok alapján igények kielégítését. A fürt méretező (CA) által a függőben lévő podok alapján ügynökcsomópontok méretezése azért teszi ezt. Azt a fürt rendszeres időközönként podok vagy üres csomópontok függőben lévő keresése vizsgálatok, és ha lehetséges növeli a méretét. Alapértelmezés szerint a hitelesítésszolgáltató 10 másodpercenként függőben van a podok keres, és eltávolítja a csomópontot, ha több mint 10 percig szükségtelen. Együtt használva az [podok horizontális méretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) (HPA), a HPA frissíti, podreplikák és az erőforrások igény szerint. Ha nincs elegendő csomópontok vagy a felesleges csomópontokat a pod méretezés a következő, a hitelesítésszolgáltató válaszol, és a csomópontok új készletét a podok ütemezése.

Ez a cikk ismerteti, hogyan helyezhet üzembe az ügynökcsomópontok a fürt automatikus méretező. Azonban mivel a fürt méretező a kube rendszer névtér üzemel, méretező fog nem méretezhető a pod futtató csomópont.

> [!IMPORTANT]
> Az Azure Kubernetes Service (AKS)-fürt méretező integrációja jelenleg **előzetes**. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="prerequisites"></a>Előfeltételek

Jelen dokumentum céljából feltételezzük, hogy az RBAC-kompatibilis AKS-fürt. Ha egy AKS-fürtre van szüksége, tekintse meg a [Azure Kubernetes Service (AKS) rövid][aks-quick-start].

 Fürt automatikus méretező használatához, a fürt Kubernetes v1.10.X kell használnia, vagy magasabb szintű és az RBAC-engedélyezve kell lennie. Frissítse a fürtöt, tekintse meg a cikket [AKS-fürt frissítése][aks-upgrade].

## <a name="gather-information"></a>Információgyűjtés

Hozzon létre a fürt méretező a fürtön történő futásra engedélyeit, futtassa a bash-szkript:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

CLUSTER_INFO=`az aks show --name $cluster_name  --resource-group $resource_group -o json`
NODE_RESOURCE_GROUP=`echo $CLUSTER_INFO | sed -e 's/^.*"nodeResourceGroup"[ ]*:[ ]*"//' -e 's/".*//' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

A parancsfájl a lépések végrehajtása után a parancsfájl kimenete egy titkos kulcsot formájában adatait, például így:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Ezután kérdezze le a csomópontkészlet neve a következő parancs futtatásával. 

```console
$ kubectl get nodes --show-labels
```

Kimenet:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Ezután bontsa ki a címke értéke **agentpool**. Alapértelmezés szerint a fürt csomópontkészlet ez "nodepool1".

Most a titkos kulcs és a csomópont tárolókészletet használja, a központi telepítési diagram is létrehozhat.

## <a name="create-a-deployment-chart"></a>Központi telepítési diagram létrehozása

Hozzon létre egy fájlt `aks-cluster-autoscaler.yaml`, és másolja bele a következő YAML-kódot.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Másolja és illessze be a titkos kulcs az előző lépésben létrehozott, és szúrja be a fájl elején.

Következő lépésként állítsa be a tartomány a csomópontok, töltse ki az argumentuma `--nodes` alatt `command` MIN:MAX:NODE_POOL_NAME formájában. Például: `--nodes=3:10:nodepool1` állítja be a csomópontok minimális száma 3., 10 csomópontok és a csomópont készlet nevére nodepool1 maximális számát.

Töltse ki a Kép mezőre **tárolók** használni kívánt fürt automatikus méretező verziójával. Az AKS igényel v1.2.2 vagy újabb. Az alábbi példában v1.2.2.

## <a name="deployment"></a>Környezet

Fürt-méretező üzembe futtatásával

```console
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Ellenőrizze, hogy fut-e a fürt méretező, használja a következő parancsot, és ellenőrizze a podok listáját. Előtaggal van ellátva "fürt-méretező" fut egy pod kell lennie. Ha ezt látja, a fürt méretező lett telepítve.

```console
kubectl -n kube-system get pods
```

Fürt automatikus méretező állapotának megtekintéséhez futtassa

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>A fürt méretező állapotának értelmezése

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

A fürt méretező állapotának lehetővé teszi, hogy a fürt méretező állapotának megtekintéséhez két különböző szinteken: fürtre kiterjedő és minden egyes csomópont csoporton belül. Az AKS jelenleg csak támogatja egy csomópont-készletet, mivel ezek a metrikák azonosak.

* Állapot azt jelzi, hogy a csomópontok általános állapotát. Ha a fürt méretező struggles hozhat létre, vagy távolít el csomópontokat a fürtben, ez az állapot "Nem kifogástalan" értékre vált. Egy másik csomópont állapota áttekintését is van:
    * "Kész" azt jelenti, hogy egy csomópont készen áll a podok rajta ütemezve van.
    * "Unready" azt jelenti, hogy egy csomópont, amely a meghibásodott, elindítása után.
    * "NotStarted" azt jelenti, hogy a csomópont teljesen még nincs elindítva.
    * "LongNotStarted" azt jelenti, hogy a csomópont nem sikerült elindítani egy ésszerű időkorláton belül.
    * "Regisztrált azt jelenti, hogy a csoport regisztrálva van egy csomópont
    * "Nem regisztrált" azt jelenti, hogy egy csomópont megtalálható a fürt szolgáltató oldalon, de nem sikerült regisztrálni a Kubernetesben.
  
* ScaleUp lehetővé teszi, hogy ellenőrizze, amikor a fürt meghatározza, hogy a fürt vertikális felskálázási történjen.
    * Az átmenet, amikor az változik a fürtben található csomópontok számát, vagy a csomópont állapotát.
    * Készen áll a csomópontok számát az elérhető és készen áll a fürtben található csomópontok számát. 
    * A cloudProviderTarget a fürt automatikus méretező megállapítása szerint a fürtnek értesülnie kell kezelni a számítási csomópontok számát.

* ScaleDown lehetővé teszi, hogy ellenőrizze, hogy vannak-e a deduplikációra méretezési le. 
    * Vertikális leskálázási csatlakozni kívánó egy csomópont megállapítása szerint a fürt automatikus méretező távolíthatja el a fürt képes kezelni a számítási feladatok befolyásolása nélkül. 
    * Megadott időpontok megjelenítése a vertikális leskálázási jelöltek utoljára ellenőrizve a fürt és az utolsó váltás ideje.

Végül események, a másolatot bármilyen méret esetén lásd: vagy is események, sikertelen vagy sikeres, és időponthoz képest, a fürt méretező elvégző leskálázása.

## <a name="next-steps"></a>További lépések

Az automatikus méretező podok horizontális fürt automatikus méretező használatához, tekintse meg [méretezés a Kubernetes-alkalmazás és az infrastruktúra][aks-tutorial-scale].

Az AKS üzembe helyezésével és kezelésével kapcsolatos további információkért lásd az AKS oktatóanyagait.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
