---
title: Futtassa a Virtual Kubelet Azure Kubernetes Service (AKS)-fürtben
description: Útmutató a Linux és Windows-tárolók futtatásához az Azure Container Instances szolgáltatásban az Azure Kubernetes Service (AKS) használatához a Virtual Kubelet.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: iainfou
ms.openlocfilehash: 0466f416568b2a1a82e264a8508697fc9de87287
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952478"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Virtual Kubelet használata az Azure Kubernetes Service (AKS)

Az Azure Container Instances (ACI) adjon meg egy üzemeltetett környezetben futó tárolók az Azure-ban. ACI használatakor nem kell a mögöttes számítási infrastruktúra kezeléséhez, az Azure végzi, a felügyeleti. Az aci Szolgáltatásban futó tárolók, amikor az egyes futó tárolók második díjkötelesek.

A Virtual Kubelet-szolgáltató Azure Container Instances használatakor Linux- és Windows-tárolókat is ütemezhető egy tárolópéldányt, ha egy standard Kubernetes-csomópontot. Ez a konfiguráció lehetővé teszi a Kubernetes képességeit és a tárolópéldányok felügyeleti érték és a költség előnyeit is kihasználhatja.

> [!NOTE]
> Virtual Kubelet egy kísérleti nyílt forráskódú projekt, és így használható. Közre, fájl problémákat, és tanulmányozza részletesen virtual kubelet, tekintse meg a [Virtual Kubelet GitHub-projekt][vk-github].

Ez a dokumentum részletesen, a container Instances Virtual Kubelet konfigurálása egy AKS-en.

## <a name="prerequisite"></a>Előfeltétel

Jelen dokumentum céljából feltételezzük, hogy egy AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg a [Azure Kubernetes Service (AKS) rövid][aks-quick-start].

Emellett az Azure CLI verziójának **2.0.33** vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A Virtual Kubelet telepítéséhez [Helm](https://docs.helm.sh/using_helm/#installing-helm) is szükség.

### <a name="for-rbac-enabled-clusters"></a>A fürtök RBAC-kompatibilis

Ha az AKS-fürt RBAC-kompatibilis, létre kell hoznia a szolgáltatásfiók és a szerepkör-kötést használja az a tiller valóban. További információkért lásd: [Helm szerepköralapú hozzáférés-vezérlés][helm-rbac].

A *ClusterRoleBinding* is létre kell hozni a Virtual Kubelet. Hozzon létre egy kötést, hozzon létre egy fájlt *rbac-virtualkubelet.yaml* , és illessze be a következő-definíciót:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: virtual-kubelet
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
```

A kötés a alkalmazni [a kubectl a alkalmazni] [ kubectl-apply] , és adja meg a *rbac-virtualkubelet.yaml* fájljához a következő példában látható módon:

```
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/virtual-kubelet created
```

A Virtual Kubelet telepítése be az AKS-fürt most továbbra is.

## <a name="installation"></a>Telepítés

Használja a [az aks install-összekötő] [ aks-install-connector] paranccsal telepíthető a Virtual Kubelet. A következő példa telepíti a Linux és a Windows összekötő.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Ezek az argumentumok érhetők el a `aks install-connector` parancsot.

| Argumentum: | Leírás | Szükséges |
|---|---|:---:|
| `--connector-name` | ACI összekötő nevét.| Igen |
| `--name` `-n` | A felügyelt fürt nevére. | Igen |
| `--resource-group` `-g` | Erőforráscsoport neve. | Igen |
| `--os-type` | Container instances operációs rendszer típusa. Megengedett értékek: mindkét, Linux, Windows. Alapértelmezett: Linux. | Nem |
| `--aci-resource-group` | Az erőforráscsoport, amelyben az ACI-tárolócsoport létrehozásához. | Nem |
| `--location` `-l` | Az a hely az ACI-tárolócsoport létrehozásához. | Nem |
| `--service-principal` | Az Azure API-khoz a hitelesítéshez használt egyszerű szolgáltatást. | Nem |
| `--client-secret` | A szolgáltatásnévhez társított titkos kulcs. | Nem |
| `--chart-url` | Egy Helm-diagram, amely telepíti az ACI összekötő URL-címe. | Nem |
| `--image-tag` | A virtual kubelet tárolórendszerképet képcímke. | Nem |

## <a name="validate-virtual-kubelet"></a>Virtual Kubelet ellenőrzése

Ellenőrizze, hogy a Virtual Kubelet telepítve van, a használt Kubernetes-csomópontok listájának lekéréséhez a [kubectl get csomópontok] [ kubectl-get] parancsot.

```
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Linux-tároló futtatása

Hozzon létre egy fájlt `virtual-kubelet-linux.yaml` másolja be a következő yaml-kódot. Cserélje le a `kubernetes.io/hostname` érték és a Linux Virtual Kubelet csomópont nevét. Jegyezze fel, amely egy [nodeSelector] [ node-selector] és [toleration] [ toleration] ütemezni a csomóponton a tárolót használ.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Futtassa az alkalmazást a [kubectl létrehozása] [ kubectl-create] parancsot.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Használja a [kubectl get pods] [ kubectl-get] parancsot a `-o wide` argumentum a kimenetben az ütemezett csomóponttal podok listáját. Figyelje meg, hogy a `aci-helloworld` pod van ütemezve: a `virtual-kubelet-virtual-kubelet-linux` csomópont.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Futtassa a Windows-tároló

Hozzon létre egy fájlt `virtual-kubelet-windows.yaml` másolja be a következő yaml-kódot. Cserélje le a `kubernetes.io/hostname` érték és a Windows Virtual Kubelet csomópont nevét. Jegyezze fel, amely egy [nodeSelector] [ node-selector] és [toleration] [ toleration] ütemezni a csomóponton a tárolót használ.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Futtassa az alkalmazást a [kubectl létrehozása] [ kubectl-create] parancsot.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Használja a [kubectl get pods] [ kubectl-get] parancsot a `-o wide` argumentum a kimenetben az ütemezett csomóponttal podok listáját. Figyelje meg, hogy a `nanoserver-iis` pod van ütemezve: a `virtual-kubelet-virtual-kubelet-win` csomópont.

```
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Távolítsa el a Virtual Kubelet

Használja a [az aks remove-connector] [ aks-remove-connector] távolítsa el a Virtual Kubelet parancsot. Az argumentum értékeket cserélje le az összekötő AKS-fürt és az AKS-fürt erőforráscsoportja nevét.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>További lépések

További információ a Virtual Kubelet a [folyó projektben Virtual Kubelet Github][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
