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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186713"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Méretező fürt az Azure Kubernetes Service (AKS) – előzetes verzió

Az Azure Kubernetes Service (AKS) az Azure-ban felügyelt Kubernetes-fürt üzembe helyezése rugalmas megoldást kínál. Erőforrásként a növekvő igények szerint növelje a fürt méretező lehetővé teszi, hogy a fürt, hogy a beállított korlátok alapján igények kielégítését. A fürt méretező (CA) által a függőben lévő podok alapján ügynökcsomópontok méretezése azért teszi ezt. Azt a fürt rendszeres időközönként podok vagy üres csomópontok függőben lévő keresése vizsgálatok, és ha lehetséges növeli a méretét. Alapértelmezés szerint a hitelesítésszolgáltató 10 másodpercenként függőben van a podok keres, és eltávolítja a csomópontot, ha több mint 10 percig szükségtelen. A podok horizontális méretező (HPA) használata esetén a HPA frissíteni fogja podreplikák és az erőforrások igény szerint. Ha ott nem elég csomópontok vagy a felesleges csomópontokat a pod méretezés a következő, a hitelesítésszolgáltató válaszol, és a csomópontok új készletét a podok ütemezése.

> [!IMPORTANT]
> Az Azure Kubernetes Service (AKS)-fürt méretező integrációja jelenleg **előzetes**. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>

## <a name="prerequisites"></a>Előfeltételek

Jelen dokumentum céljából feltételezzük, hogy az RBAC-kompatibilis AKS-fürt. Ha egy AKS-fürtre van szüksége, tekintse meg a [Azure Kubernetes Service (AKS) rövid][aks-quick-start].

 Fürt automatikus méretező használatához, a fürt Kubernetes v1.10.X kell használnia, vagy magasabb szintű és az RBAC-engedélyezve kell lennie. Frissítse a fürtöt, tekintse meg a cikket [AKS-fürt frissítése][aks-upgrade].

## <a name="gather-information"></a>Információgyűjtés

Az alábbi listában jeleníti meg mindent megtalál, meg kell adnia a méretező-definícióban.

- *Előfizetés-azonosító*: megfelelő ehhez a fürthöz használt előfizetés-azonosító
- *Erőforráscsoport-nevet* : a fürthöz tartozó erőforráscsoport neve 
- *Fürt neve*: a fürt neve
- *Ügyfél-azonosító*: lépés generálása engedélyt kapott Alkalmazásazonosító
- *Titkos Ügyfélkód*: alkalmazás titkos kulcs generálása lépés engedélyt kapott
- *Bérlőazonosító*: a bérlő (fióktulajdonos) azonosítója
- *Csomópont erőforráscsoport*: az ügynök a fürt csomópontjain tartalmazó erőforráscsoport neve
- *Készlet csomópontnév*: a csomópont neve tárolókészlet, szeretné, a méretezési csoport
- *Csomópontok minimális száma*: a fürtben található csomópontok minimális száma
- *Csomópontok maximális száma*: a fürtben található csomópontok maximális száma
- *Virtuálisgép-típusra*: a Kubernetes-fürt létrehozásához használt szolgáltatás

Kérje le a saját előfizetés-Azonosítójára: 

``` azurecli
az account show --query id
```

Hozzon létre az Azure hitelesítő adatait a következő parancs futtatásával:

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

Az Alkalmazásazonosító, a jelszó és a bérlő azonosítója lesz a clientID, a clientSecret és a bérlő azonosítója a következő lépésekben.

A csomópont-készlet neve lekérése a következő parancs futtatásával. 

```console
$ kubectl get nodes --show-labels
```

Kimenet:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Ezután bontsa ki a címke értéke **agentpool**. Alapértelmezés szerint a fürt csomópontkészlet ez "nodepool1".

Az erőforráscsoport nevét, a csomópont lekéréséhez a címke értékének kinyerése **kubernetes.azure.com<span></span>/fürt**. A csomópont erőforráscsoport-név alapvetően az űrlap MC_ [erőforráscsoport-]\_[fürt-name] _ [helye].

A vmType paramétert használja, a szolgáltatás, amely itt van az AKS hivatkozik.

Most rendelkeznie kell a következő információkat:

- SubscriptionID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- TenantID
- NodeResourceGroup
- VMType

Ezután az összes ezeket az értékeket a base64 kódolás. Ha például kódolása base64-VMType értékét:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Titkos kód létrehozása
Ezen adatok alapján hozzon létre egy titkos kulcsot, a központi telepítés használatával értékeket az előző lépést a következő formátumban:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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

Ellenőrizze, hogy fut-e a fürt méretező, használja a következő parancsot, és ellenőrizze a podok listáját. Ha egy pod előtaggal van ellátva "fürt-méretező" fut, a fürt méretező lett telepítve.

```console
kubectl -n kube-system get pods
```

Fürt automatikus méretező állapotának megtekintéséhez futtassa

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

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
