---
title: Futtassa a Virtual Kubelet Azure Kubernetes Service (AKS)-fürtben
description: Útmutató a Linux és Windows-tárolók futtatásához az Azure Container Instances szolgáltatásban az Azure Kubernetes Service (AKS) használatához a Virtual Kubelet.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: cc0c3becf21cb54b97a88e9ba35b38308af81a85
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475425"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Virtual Kubelet használata az Azure Kubernetes Service (AKS)

Az Azure Container Instances (ACI) adjon meg egy üzemeltetett környezetben futó tárolók az Azure-ban. ACI használatakor nem kell a mögöttes számítási infrastruktúra kezeléséhez, az Azure végzi, a felügyeleti. Az aci Szolgáltatásban futó tárolók, amikor az egyes futó tárolók második díjkötelesek.

A Virtual Kubelet-szolgáltató Azure Container Instances használatakor Linux- és Windows-tárolókat is ütemezhető egy tárolópéldányt, ha egy standard Kubernetes-csomópontot. Ez a konfiguráció lehetővé teszi a Kubernetes képességeit és a tárolópéldányok felügyeleti érték és a költség előnyeit is kihasználhatja.

> [!NOTE]
> Az AKS most már rendelkezik beépített támogatást nyújt, a tárolók az aci-ban, nevű ütemezés *virtuális csomópontok*. Ezek a virtuális csomópontok jelenleg támogatja a Linux container Instances szolgáltatásban. Ha Windows tárolópéldányok ütemezése van szüksége, folytathatja a Virtual Kubelet használatával. Ellenkező esetben használjon virtuális csomópontok helyett a manuális Virtual Kubelet feljegyzett ebben a cikkben. A virtuális csomópontok használatával megkezdheti a [Azure CLI] [ virtual-nodes-cli] vagy [az Azure portal][virtual-nodes-portal].
>
> Virtual Kubelet egy kísérleti nyílt forráskódú projekt, és így használható. Közre, fájl problémákat, és tanulmányozza részletesen virtual kubelet, tekintse meg a [Virtual Kubelet GitHub-projekt][vk-github].

## <a name="before-you-begin"></a>Előkészületek

Jelen dokumentum céljából feltételezzük, hogy egy AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg a [Azure Kubernetes Service (AKS) rövid][aks-quick-start].

Emellett az Azure CLI verziójának **2.0.65** vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Telepítse a Virtual Kubelet, telepítse és konfigurálja [Helm] [ aks-helm] az AKS-fürt található. Győződjön meg arról, hogy a tiller valóban [Kubernetes RBAC használatra konfigurált](#for-rbac-enabled-clusters), ha szükséges.

### <a name="register-container-instances-feature-provider"></a>Container Instances szolgáltatás-szolgáltató regisztrálása

Ha korábban nem használta az Azure Container Instance (ACI) szolgáltatás, regisztrálja a szolgáltatót az előfizetés. Az ACI szolgáltató regisztrációs használatával állapotát ellenőrizheti a [az identitásszolgáltató-listája] [ az-provider-list] parancsot, az alábbi példában látható módon:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft.ContainerInstance* szolgáltató kell jelentse *regisztrált*, ahogy az alábbi példa kimenetében látható:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Ha a szolgáltató állapota *NotRegistered*, regisztrálja a szolgáltatót használatával a [az provider register] [ az-provider-register] az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>A fürtök RBAC-kompatibilis

Ha az AKS-fürt RBAC-kompatibilis, létre kell hoznia a szolgáltatásfiók és a szerepkör-kötést használja az a tiller valóban. További információkért lásd: [Helm szerepköralapú hozzáférés-vezérlés][helm-rbac]. A szolgáltatásfiók és a szerepkör kötést, hozzon létre egy fájlt *rbac virtuális kubelet.yaml* , és illessze be a következő-definíciót:

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

A alkalmazni a szolgáltatásfiók és a kötési [a kubectl a alkalmazni] [ kubectl-apply] , és adja meg a *rbac virtuális kubelet.yaml* fájljához a következő példában látható módon:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Helm használata a tiller valóban szolgáltatásfiók konfigurálása:

```console
helm init --service-account tiller
```

A Virtual Kubelet telepítése be az AKS-fürt most továbbra is.

## <a name="installation"></a>Telepítés

Használja a [az aks install-összekötő] [ aks-install-connector] paranccsal telepíthető a Virtual Kubelet. A következő példa telepíti a Linux és a Windows összekötő.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Ezek az argumentumok érhetők el a [az aks install-összekötő] [ aks-install-connector] parancsot.

| Argumentum: | Leírás | Szükséges |
|---|---|:---:|
| `--connector-name` | ACI összekötő nevét.| Igen |
| `--name` `-n` | A felügyelt fürt nevére. | Igen |
| `--resource-group` `-g` | Erőforráscsoport neve. | Igen |
| `--os-type` | Container instances operációs rendszer típusa. Megengedett értékek: Both, Linux, Windows. alapértelmezett érték: Linux. | Nem |
| `--aci-resource-group` | Az erőforráscsoport, amelyben az ACI-tárolócsoport létrehozásához. | Nem |
| `--location` `-l` | Az a hely az ACI-tárolócsoport létrehozásához. | Nem |
| `--service-principal` | Az Azure API-khoz a hitelesítéshez használt egyszerű szolgáltatást. | Nem |
| `--client-secret` | A szolgáltatásnévhez társított titkos kulcs. | Nem |
| `--chart-url` | Egy Helm-diagram, amely telepíti az ACI összekötő URL-címe. | Nem |
| `--image-tag` | A virtual kubelet tárolórendszerképet képcímke. | Nem |

## <a name="validate-virtual-kubelet"></a>Virtual Kubelet ellenőrzése

Ellenőrizze, hogy a Virtual Kubelet telepítve van, a használt Kubernetes-csomópontok listájának lekéréséhez a [kubectl get csomópontok] [ kubectl-get] parancsot:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Linux-tároló futtatása

Hozzon létre egy fájlt `virtual-kubelet-linux.yaml` másolja be a következő yaml-kódot. Jegyezze fel, amely egy [nodeSelector] [ node-selector] és [toleration] [ toleration] ütemezni a csomóponton a tárolót használ.

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

Futtassa az alkalmazást a [kubectl létrehozása] [ kubectl-create] parancsot.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Használja a [kubectl get pods] [ kubectl-get] parancsot a `-o wide` argumentum a kimenetben az ütemezett csomóponttal podok listáját. Figyelje meg, hogy a `aci-helloworld` pod van ütemezve: a `virtual-kubelet-virtual-kubelet-linux` csomópont.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Windows-tárolók futtatása

Hozzon létre egy fájlt `virtual-kubelet-windows.yaml` másolja be a következő yaml-kódot. Jegyezze fel, amely egy [nodeSelector] [ node-selector] és [toleration] [ toleration] ütemezni a csomóponton a tárolót használ.

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

Futtassa az alkalmazást a [kubectl létrehozása] [ kubectl-create] parancsot.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Használja a [kubectl get pods] [ kubectl-get] parancsot a `-o wide` argumentum a kimenetben az ütemezett csomóponttal podok listáját. Figyelje meg, hogy a `nanoserver-iis` pod van ütemezve: a `virtual-kubelet-virtual-kubelet-windows` csomópont.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Távolítsa el a Virtual Kubelet

Használja a [az aks remove-connector] [ aks-remove-connector] távolítsa el a Virtual Kubelet parancsot. Az argumentum értékeket cserélje le az összekötő AKS-fürt és az AKS-fürt erőforráscsoportja nevét.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Ha mindkét operációs rendszer összekötők eltávolítása hibákat észlel, vagy csak a Windows vagy Linux operációs rendszer összekötő eltávolítja, manuálisan megadhatja az operációs rendszer típusa. Adja hozzá a `--os-type` paramétert az előző `az aks remove-connector` parancsot, és adjon meg `Windows` vagy `Linux`.

## <a name="next-steps"></a>További lépések

A Virtual Kubelet lehetséges problémák, lásd: a [ismert régi stílusú és megkerülő megoldások][vk-troubleshooting]. Problémák jelentése és a Virtual Kubelet [nyisson meg egy GitHub-problémát][vk-issues].

További információ a Virtual Kubelet a [Virtual Kubelet GitHub-projekt][vk-github].

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
