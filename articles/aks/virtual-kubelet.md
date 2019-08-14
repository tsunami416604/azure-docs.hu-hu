---
title: Virtuális Kubelet futtatása Azure Kubernetes szolgáltatásbeli (ak-beli) fürtben
description: Ismerje meg, hogyan használhatja a virtuális Kubelet az Azure Kubernetes szolgáltatással (ak) Linux-és Windows-tárolók futtatásához Azure Container Instanceson.
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: f18992be353d2d6cc739412d98ccd97d5e78d4c7
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67613861"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Virtuális Kubelet használata az Azure Kubernetes szolgáltatással (ak)

Azure Container Instances (ACI) üzemeltetett környezetet biztosít a tárolók Azure-beli futtatásához. ACI használata esetén nincs szükség a mögöttes számítási infrastruktúra felügyeletére, az Azure kezeli ezt a kezelést. A tárolók ACI-ban való futtatásakor minden futó tároló esetében a második díjat kell fizetnie.

Ha Azure Container Instances virtuális Kubelet-szolgáltatót használ, a Linux-és Windows-tárolókat is ütemezheti egy tároló példányra, mintha az egy szabványos Kubernetes-csomópont legyen. Ez a konfiguráció lehetővé teszi, hogy kihasználhassa a Kubernetes képességeit, valamint a tároló-példányok kezelési értékét és költséghatékonyságát.

> [!NOTE]
> Az AK-ban most már beépített támogatással rendelkezik a tárolók ütemezéséhez a *virtuális csomópontok*nevű ACI-ben. Ezek a virtuális csomópontok jelenleg támogatják a Linux Container instances-t. Ha Windows Container instances-t kell ütemeznie, továbbra is használhatja a virtuális Kubelet. Ellenkező esetben virtuális csomópontokat kell használnia a jelen cikkben ismertetett kézi virtuális Kubelet utasítás helyett. A virtuális csomópontok az [Azure CLI][virtual-nodes-cli] vagy a [Azure Portal][virtual-nodes-portal]használatával indíthatók el.
>
> A virtuális Kubelet egy kísérleti, nyílt forráskódú projekt, amelyet ilyenként kell használni. A következő témakörben talál további információt a virtuális kubelet: [Virtual Kubelet GitHub-projekt][vk-github], amely hozzájárul a problémák megoldásához

## <a name="before-you-begin"></a>Előkészületek

Ez a dokumentum feltételezi, hogy rendelkezik egy AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az [Azure Kubernetes szolgáltatás (ak)][aks-quick-start]gyors útmutatóját.

Szüksége lesz az Azure CLI **2.0.65** vagy újabb verziójára is. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

A virtuális Kubelet telepítéséhez telepítse és konfigurálja a [Helm][aks-helm] -t az AK-fürtben. Szükség esetén győződjön meg arról, hogy a kormányrúd a [KUBERNETES RBAC való használatra van konfigurálva](#for-rbac-enabled-clusters).

### <a name="register-container-instances-feature-provider"></a>Container Instances szolgáltatás-szolgáltató regisztrálása

Ha korábban még nem használta az Azure Container instance (ACI) szolgáltatást, regisztrálja a szolgáltatót az előfizetésében. Az ACI-szolgáltató regisztrációjának állapotát az az [Provider List][az-provider-list] parancs használatával tekintheti meg, ahogy az az alábbi példában is látható:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft. ContainerInstance* szolgáltatónak regisztráltkéntkell jelentenie, ahogy az alábbi példában is látható:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Ha a szolgáltató *NotRegistered*-ként jelenik meg, regisztrálja a szolgáltatót az az [Provider Register][az-provider-register] paranccsal az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>RBAC-kompatibilis fürtök esetén

Ha az AK-fürt RBAC-engedélyezve van, létre kell hoznia egy szolgáltatásfiókot és egy szerepkör-kötést a Kormányrúdhoz való használathoz. További információ: [Helm szerepköralapú hozzáférés-vezérlés][helm-rbac]. Szolgáltatásfiók és szerepkör-kötés létrehozásához hozzon létre egy *RBAC-Virtual-kubelet. YAML* nevű fájlt, és illessze be a következő definíciót:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Alkalmazza a szolgáltatásfiókot és a kötést a [kubectl alkalmazással][kubectl-apply] , és adja meg a *RBAC-Virtual-kubelet. YAML* fájlt a következő példában látható módon:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Állítsa be a Helm-t a kormányrúd-szolgáltatásfiók használatára:

```console
helm init --service-account tiller
```

Most már továbbra is telepítheti a virtuális Kubelet az AK-fürtbe.

## <a name="installation"></a>Telepítés

A virtuális Kubelet telepítéséhez használja az az [AK install-Connector][aks-install-connector] parancsot. A következő példa a Linux és a Windows összekötőt is telepíti.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Ezek az argumentumok az az [AK install-Connector][aks-install-connector] parancshoz érhetők el.

| Argumentum | Leírás | Kötelező |
|---|---|:---:|
| `--connector-name` | Az ACI-összekötő neve.| Igen |
| `--name` `-n` | A felügyelt fürt neve. | Igen |
| `--resource-group` `-g` | Erőforráscsoport neve. | Igen |
| `--os-type` | A Container instances operációs rendszer típusa. Megengedett értékek: Mindkettő, Linux, Windows. Alapértelmezett: Linux. | Nem |
| `--aci-resource-group` | Az az erőforráscsoport, amelyben létre kell hozni az ACI-tároló csoportokat. | Nem |
| `--location` `-l` | Az ACI-tároló csoportok létrehozási helye. | Nem |
| `--service-principal` | Egyszerű szolgáltatásnév az Azure API-khoz való hitelesítéshez. | Nem |
| `--client-secret` | Az egyszerű szolgáltatáshoz társított titkos kulcs. | Nem |
| `--chart-url` | Az ACI-összekötőt telepítő Helm-diagram URL-címe. | Nem |
| `--image-tag` | A virtuális kubelet-tároló rendszerképének címkéje | Nem |

## <a name="validate-virtual-kubelet"></a>Virtuális Kubelet ellenőrzése

Annak ellenőrzéséhez, hogy a virtuális Kubelet telepítve van-e, a Kubernetes-csomópontok listájának visszaadása a [kubectl Get Nodes][kubectl-get] parancs használatával:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Linux-tároló futtatása

Hozzon létre egy `virtual-kubelet-linux.yaml` nevű fájlt, és másolja a következő YAML. Vegye figyelembe, hogy a [nodeSelector][node-selector] és a [tolerancia][toleration] a tároló a csomóponton való beosztásához használatos.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
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
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Futtassa az alkalmazást a [kubectl Create][kubectl-create] paranccsal.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Használja a [kubectl Get hüvely][kubectl-get] parancsot az `-o wide` argumentummal, amely megjeleníti a hüvelyek listáját az ütemezett csomóponttal. `aci-helloworld` Figyelje`virtual-kubelet-virtual-kubelet-linux` meg, hogy a pod ütemezve van a csomóponton.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Windows-tárolók futtatása

Hozzon létre egy `virtual-kubelet-windows.yaml` nevű fájlt, és másolja a következő YAML. Vegye figyelembe, hogy a [nodeSelector][node-selector] és a [tolerancia][toleration] a tároló a csomóponton való beosztásához használatos.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Futtassa az alkalmazást a [kubectl Create][kubectl-create] paranccsal.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Használja a [kubectl Get hüvely][kubectl-get] parancsot az `-o wide` argumentummal, amely megjeleníti a hüvelyek listáját az ütemezett csomóponttal. `nanoserver-iis` Figyelje`virtual-kubelet-virtual-kubelet-windows` meg, hogy a pod ütemezve van a csomóponton.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Virtuális Kubelet eltávolítása

A virtuális Kubelet eltávolításához használja az az [AK Remove-Connector][aks-remove-connector] parancsot. Cserélje le az argumentum értékeit az összekötő, az AK-fürt és az AK-beli fürterőforrás-csoport nevére.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Ha az operációs rendszer összekötőit nem távolítja el, vagy csak a Windows vagy Linux operációs rendszer összekötőjét szeretné eltávolítani, manuálisan is megadhatja az operációs rendszer típusát. Adja hozzá `--os-type` a paramétert az `az aks remove-connector` előző parancshoz, `Windows` és `Linux`adja meg a vagy a parancsot.

## <a name="next-steps"></a>További lépések

A virtuális Kubelet kapcsolatos lehetséges problémákért tekintse meg az [ismert mókás és megkerülő megoldásokat][vk-troubleshooting]. A virtuális Kubelet kapcsolatos problémák jelentéséhez [Nyisson meg egy GitHub-problémát][vk-issues].

További információk a virtuális Kubelet a virtuális [Kubelet GitHub][vk-github]-projektben.

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
