---
title: Virtuális csomópontok létrehozása a portál használatával az Azure Kubernetes-szolgáltatásokban (AKS)
description: Megtudhatja, hogyan hozhat létre az Azure Kubernetes Services (AKS) fürtöt, amely virtuális csomópontokat használ a podok futtatásához.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 62d8fec4c5c3ff35fb46826cb7118946f66948b2
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392581"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Hozzon létre és konfiguráljon egy Azure Kubernetes-szolgáltatás (AKS) fürtöt virtuális csomópontok használatára az Azure Portalon

A számítási feladatok gyors üzembe helyezéséhez egy Azure Kubernetes-szolgáltatás (AKS) fürtben, virtuális csomópontok at használhat. A virtuális csomópontok, podok gyors kiépítése, és csak másodpercenként a végrehajtási idő. Skálázási forgatókönyvben nem kell megvárnia a Kubernetes-fürt automatikus skálázó virtuális gép számítási csomópontjainak üzembe helyezését a további podok futtatásához. A virtuális csomópontokat csak Linux-podok és csomópontok támogatják.

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat virtuális hálózati erőforrásokat és egy aks-fürtöt, amelyen engedélyezve van a virtuális csomópont.

## <a name="before-you-begin"></a>Előkészületek

A virtuális csomópontok lehetővé teszik a hálózati kommunikációt az Azure Container Instances (ACI) és az AKS-fürtben futó podok között. A kommunikáció biztosításához létrejön egy virtuális hálózati alhálózat, és delegált engedélyek vannak hozzárendelve. A virtuális csomópontok csak *a speciális* hálózatkezeléssel létrehozott AKS-fürtökkel működnek. Alapértelmezés szerint az AKS-fürtök *alapszintű* hálózatkezeléssel jönnek létre. Ez a cikk bemutatja, hogyan hozhat létre virtuális hálózatot és alhálózatokat, majd telepíthet egy speciális hálózati hálózatot használó AKS-fürtöt.

Ha korábban még nem használta az ACI-t, regisztrálja a szolgáltatót az előfizetésével. Az ACI-szolgáltató regisztrációjának állapotát az [az provider list][az-provider-list] parancs segítségével ellenőrizheti, ahogy az a következő példában látható:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft.ContainerInstance* szolgáltatónak regisztráltként kell *jelentenie,* ahogy az a következő példakimeneten látható:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Ha a szolgáltató notregistered néven jelenik *meg,* regisztrálja a szolgáltatót az [az szolgáltató regiszterhasználatával,][az-provider-register] ahogy az a következő példában látható:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A következő régiók támogatják a virtuális csomópontok központi telepítését:

* Ausztrália Kelet (Ausztráliakelet)
* USA középső régiója (centralus)
* USA keleti része (eastus)
* USA keleti része 2 (eastus2)
* Kelet-Japán (japaneast)
* Észak-Európa (Észak-Európa)
* Délkelet-Ázsia (Délkelet-Ázsia)
* USA közép-közép régiója (westcentralus)
* Nyugat-Európa (Nyugat-Európa)
* USA nyugati (westus)
* USA nyugati 2 (westus2)

## <a name="known-limitations"></a>Ismert korlátozások
A virtuális csomópontok funkciói nagymértékben függnek az ACI szolgáltatáskészletétől. A következő forgatókönyvek még nem támogatottak a virtuális csomópontok

* Egyszerű szolgáltatás használata ACR-lemezképek lekérése. [Megoldás:](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) [Kubernetes-titkok](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) használata
* [Virtuális hálózati korlátozások,](../container-instances/container-instances-vnet.md) beleértve a virtuális hálózati társviszony-létesítést, a Kubernetes hálózati házirendeket és a hálózati biztonsági csoportokkal rendelkező kimenő internetes forgalmat.
* Init tárolók
* [Gazdanevek](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Érvek](../container-instances/container-instances-exec.md#restrictions) exec az ACI
* [A DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) nem telepíti a podokat a virtuális csomópontra
* A virtuális csomópontok támogatják a Linux-podok ütemezését. Manuálisan telepítheti a nyílt forráskódú [Virtual Kubelet ACI-szolgáltatót](https://github.com/virtual-kubelet/azure-aci) a Windows Server-tárolók ACI-ra való ütemezéséhez. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az Azure Portal bal felső sarkában válassza az Erőforrás > létrehozása**Kubernetes szolgáltatás** **lehetőséget.**

Az **Alapok** lapon adja meg a következő beállításokat:

- *PROJEKT ADATAI*: Válasszon ki egy Azure-előfizetést, majd válasszon ki vagy hozzon létre egy Azure-erőforráscsoportot, például: *myResourceGroup*. Adja meg a **Kubernetes-fürt nevét**, például *myAKSCluster*.
- *FÜRT ADATAI*: Válasszon egy régiót, Kubernetes-verziót és DNS-névelőtagot az AKS-fürthöz.
- *ELSŐDLEGES CSOMÓPONTKÉSZLET:* Válasszon virtuális gépméretet az AKS-csomópontokhoz. A virtuálisgép-méret az AKS-fürt telepítését követően **nem** módosítható.
     - Adja meg a fürtre telepítendő csomópontok számát. Ebben a cikkben állítsa **a csomópontszám** *1-re.* A csomópontok száma a fürt telepítése után is **módosítható**.

Kattintson **a Tovább gombra: Méretezés gombra.**

A **Méretezés** lapon válassza az *Engedélyezve* lehetőséget a **Virtuális csomópontok**csoportban.

![AKS-fürt létrehozása és a virtuális csomópontok engedélyezése](media/virtual-nodes-portal/enable-virtual-nodes.png)

Alapértelmezés szerint egy Azure Active Directory szolgáltatásnév jön létre. Ez a szolgáltatásnév fürtkommunikációra és más Azure-szolgáltatásokkal való integrációra szolgál. Azt is megteheti, hogy egy felügyelt identitás engedélyek helyett egy egyszerű szolgáltatás. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

A fürt speciális hálózatkezelésre is be van állítva. A virtuális csomópontok saját Azure virtuális hálózati alhálózathasználatára vannak konfigurálva. Ez az alhálózat delegált engedélyekkel rendelkezik az Azure-erőforrások AKS-fürt közötti csatlakoztatásához. Ha még nem rendelkezik delegált alhálózattal, az Azure Portal létrehozza és konfigurálja az Azure virtuális hálózatot és alhálózatot a virtuális csomópontokkal való használatra.

Válassza az **Áttekintés + létrehozás** lehetőséget. Az ellenőrzés befejezése után válassza a **Létrehozás gombot.**

Az AKS-fürt létrehozása és a használatra való előkészítése néhány percet vesz igénybe.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl] eszközt, a Kubernetes parancssori ügyfelét. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

A Cloud Shell megnyitásához válassza a **Próba** a kódblokk jobb felső sarkából lehetőséget. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálhatja a `kubectl` ügyfelet a Kubernetes-fürthöz való csatlakozásra. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimeneti mutatja az egyetlen Virtuálisgép-csomópont létre, majd a virtuális csomópont Linux, *virtual-node-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Mintaalkalmazás telepítése

Az Azure Cloud Shell hozzon `virtual-node.yaml` létre egy nevű fájlt, és másolja a következő YAML. A tároló ütemezéséhez a csomópont, [és][node-selector] [a tolerancia][toleration] van definiálva. Ezek a beállítások lehetővé teszik, hogy a pod ütemezve a virtuális csomóponton, és ellenőrizze, hogy a szolgáltatás sikeresen engedélyezve van.

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
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Futtassa az alkalmazást a [kubectl apply][kubectl-apply] paranccsal.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Használja a [kubectl get pods][kubectl-get] parancsot az argumentumot a `-o wide` podok és az ütemezett csomópont listájának kimenetéhez. Figyelje `virtual-node-helloworld` meg, hogy a `virtual-node-linux` pod van ütemezve a csomóponton.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod van rendelve egy belső IP-címet az Azure virtuális hálózati alhálózat delegált virtuális csomópontok használata.

> [!NOTE]
> Ha az Azure Container Registry szolgáltatásban tárolt lemezképeket használja, [konfigurálja és használja a Kubernetes-titkos kulcsot.][acr-aks-secrets] A virtuális csomópontok jelenlegi korlátozása, hogy nem használhatja az integrált Azure AD szolgáltatás egyszerű hitelesítés. Ha nem használ titkos kulcsot, a virtuális csomópontokon ütemezett podok nem `HTTP response status code 400 error code "InaccessibleImage"`indulnak el, és jelentik a hibát.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópontpod tesztelése

A virtuális csomóponton futó pod teszteléséhez keresse meg a bemutató alkalmazást egy webes klienssel. A pod van rendelve egy belső IP-címet, gyorsan tesztelheti ezt a kapcsolatot egy másik pod az AKS-fürtön. Hozzon létre egy tesztpodot, és csatoljon hozzá egy terminálmunkamenetet:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Telepítse `curl` a pod `apt-get`segítségével:

```console
apt-get update && apt-get install -y curl
```

Most a használatával `curl`érheti el *http://10.241.0.4*a pod címét, például a használatával. Adja meg saját belső IP-címét az előző `kubectl get pods` parancsban látható módon:

```console
curl -L http://10.241.0.4
```

A demo alkalmazás jelenik meg, amint az a következő tömörített példa kimenet:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zárja be a terminálmunkamenetet `exit`a tesztegységhez a segítségével. Amikor a munkamenet befejeződött, a pod a törölt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy pod volt ütemezve a virtuális csomóponton, és hozzárendelt egy privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatás üzembe helyezési és a pod egy terheléselosztó vagy a be- éselőtér-vezérlő keresztül forgalmat. További információ: [Hozzon létre egy alapvető bejövő kapcsolatvezérlőt az AKS-ben.][aks-basic-ingress]

A virtuális csomópontok az AKS skálázási megoldásának egyik összetevője. A skálázási megoldásokról az alábbi cikkekben talál további információt:

- [A Kubernetes vízszintes pod automatikus skálázó használata][aks-hpa]
- [A Kubernetes-fürt automatikus méretezőjének használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok automatikus skálázási mintáját][virtual-node-autoscale]
- [További információ a Virtual Kubelet nyílt forráskódú könyvtárról][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register