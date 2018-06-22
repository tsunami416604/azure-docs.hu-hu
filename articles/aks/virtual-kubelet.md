---
title: Egy Azure Kubernetes szolgáltatás (AKS) fürt virtuális kubelet futtatása
description: Használja a virtuális kubelet Azure tároló példányok Kubernetes tárolók futtatásához.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: nepeters
ms.openlocfilehash: 0d046970b40f5253b07005ab578035c7c179e7df
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304443"
---
# <a name="virtual-kubelet-with-aks"></a>A AKS virtuális Kubelet

Azure tároló példányok (ACI) adja meg az Azure-ban futó tárolók szolgáltatói környezetben. ACI használata esetén nincs szükség a mögöttes számítási infrastruktúra kezelése érdekében, Azure kezeli az Ön számára ez a felügyeleti. Tárolók ACI fut, ha van szó, a második minden futó tároló.

Ha a virtuális Kubelet szolgáltató használata Azure-tároló példányok, Linux-és Windows ütemezhető tároló példányán mintha ez egy szabványos Kubernetes csomópont. Ez a konfiguráció lehetővé teszi Kubernetes képességeit és a tároló példányok felügyeleti érték és a költséghatékonyság előnyeinek kihasználása.

> [!NOTE]
> Virtuális Kubelet kísérleti nyílt forráskódú projektként, és így kell használni. Közreműködési lehetőségek, fájl problémákat, és olvasási virtuális kubelet kapcsolatos további lásd: a [virtuális Kubelet GitHub-projekt][vk-github].

Ez a dokumentum adatokat tároló példányok virtuális Kubelet konfigurálása egy AKS.

## <a name="prerequisite"></a>Előfeltétel

Jelen dokumentum céljából feltételezzük, hogy rendelkezik-e egy AKS fürthöz. Ha egy AKS fürthöz van szüksége, tekintse meg a [Azure Kubernetes szolgáltatás (AKS) gyors üzembe helyezés][aks-quick-start].

Emellett szükség van az Azure CLI verzió **2.0.33** vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

[Helm](https://docs.helm.sh/using_helm/#installing-helm) is szükséges a virtuális Kubelet telepítéséhez.

## <a name="installation"></a>Telepítés

Használja a [az aks install-összekötő] [ aks-install-connector] parancsot a virtuális Kubelet telepítéséhez. A következő példa telepíti a Linux és a Windows összekötő.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

Ezek az argumentumok érhetők el a `aks install-connector` parancsot.

| Argumentum: | Leírás | Szükséges |
|---|---|:---:|
| `--connector-name` | A ACI összekötő nevét.| Igen |
| `--name` `-n` | A felügyelt fürt nevét. | Igen |
| `--resource-group` `-g` | Erőforráscsoport nevét. | Igen |
| `--os-type` | Példányok operációs rendszer típusa. Megengedett értékek: mindkét, Linux, Windows. Alapértelmezett: Linux. | Nem |
| `--aci-resource-group` | Az erőforráscsoport a ACI tároló csoportok létrehozásához. | Nem |
| `--location` `-l` | A hely a ACI tároló csoportok létrehozásához. | Nem |
| `--service-principal` | Az Azure API-k hitelesítéshez használt egyszerű. | Nem |
| `--client-secret` | Az egyszerű szolgáltatásnév társított titkos. | Nem |
| `--chart-url` | Telepítő ACI összekötő Helm diagram URL-CÍMÉT. | Nem |
| `--image-tag` | A virtuális kubelet tároló kép képcímke. | Nem |

## <a name="validate-virtual-kubelet"></a>Virtuális Kubelet ellenőrzése

Ellenőrizze, hogy virtuális Kubelet telepítve van-e, adja vissza a Kubernetes-csomópontokat használja listáját a [kubectl beolvasása csomópontok] [ kubectl-get] parancsot.

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Futtassa a Linux-tároló

Hozzon létre egy fájlt `virtual-kubelet-linux.yaml` és a következő YAM másolja. Cserélje le a `kubernetes.io/hostname` érték és a Linux virtuális Kubelet csomópont neve. Jegyezze fel, amely egy [nodeSelector] [ node-selector] és [toleration] [ toleration] ütemezni a csomóponton a tárolót használ.

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

Futtassa az alkalmazást az a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

Használja a [kubectl első három munkaállomás-csoporttal] [ kubectl-get] parancsot a `-o wide` argumentum kimeneti három munkaállomás-csoporttal az ütemezett csomóponttal listáját. Figyelje meg, hogy a `aci-helloworld` pod ütemezése megtörtént meg a `virtual-kubelet-virtual-kubelet-linux` csomópont.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Futtassa a Windows-tároló

Hozzon létre egy fájlt `virtual-kubelet-windows.yaml` és a következő YAM másolja. Cserélje le a `kubernetes.io/hostname` érték és a Windows virtuális Kubelet csomópont neve. Jegyezze fel, amely egy [nodeSelector] [ node-selector] és [toleration] [ toleration] ütemezni a csomóponton a tárolót használ.

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

Futtassa az alkalmazást az a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

Használja a [kubectl első három munkaállomás-csoporttal] [ kubectl-get] parancsot a `-o wide` argumentum kimeneti három munkaállomás-csoporttal az ütemezett csomóponttal listáját. Figyelje meg, hogy a `nanoserver-iis` pod ütemezése megtörtént meg a `virtual-kubelet-virtual-kubelet-win` csomópont.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Távolítsa el a virtuális Kubelet

Használja a [az aks remove-összekötő] [ aks-remove-connector] parancs beírásával távolítsa el a virtuális Kubelet. Az argumentum értékek cserélje le az összekötő, AKS fürt és a AKS fürterőforrás-csoport nevét.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>További lépések

További információk a következő virtuális Kubelet a [folyó projektben virtuális Kubelet Github][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
