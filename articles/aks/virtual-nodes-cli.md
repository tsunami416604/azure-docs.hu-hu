---
title: Virtuális csomópontok létrehozása az Azure CLI használatával
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használhatja az Azure CLI-t egy Azure Kubernetes Services (AKS) fürt létrehozásához, amely virtuális csomópontokat használ a podok futtatásához.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: b3dec8a7d46226b9d6f4416c98332f0023c0c294
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392590"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Hozzon létre és konfiguráljon egy Azure Kubernetes-szolgáltatás (AKS) fürtöt virtuális csomópontok használatára az Azure CLI használatával

Az Azure Kubernetes-szolgáltatás (AKS) fürtben az alkalmazás-munkaterhelések gyors méretezéséhez virtuális csomópontokat használhat. A virtuális csomópontok, podok gyors kiépítése, és csak másodpercenként a végrehajtási idő. Nem kell megvárnia, amíg a Kubernetes-fürt automatikus skálázója virtuális gép számítási csomópontjainak üzembe helyezéséhez a további podok futtatásához. A virtuális csomópontokat csak Linux-podok és csomópontok támogatják.

Ez a cikk bemutatja, hogyan hozhat létre és konfigurálhat a virtuális hálózati erőforrásokat és az AKS-fürtöt, majd engedélyezheti a virtuális csomópontokat.

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

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Próba** a kódblokk jobb felső sarkából lehetőséget. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/bash](https://shell.azure.com/bash)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a CLI helyi telepítését és használatát szeretné használni, ez a cikk az Azure CLI 2.0.49-es vagy újabb verzióját igényli. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot az [az hálózati virtuális hálózat create][az-network-vnet-create] paranccsal. A következő példa létrehoz egy virtuális hálózati nevet *myVnet* egy cím előtag *10.0.0.0/8*, és egy alhálózat nevű *myAKSSubnet*. Az alhálózat címelőtagja alapértelmezés szerint *10.240.0.0/16:*

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Most hozzon létre egy további alhálózatot a virtuális csomópontokhoz az [az hálózati virtuális hálózat létrehozási][az-network-vnet-subnet-create] parancsával. A következő példa létrehoz egy *nAVirtualNodeSubnet* nevű alhálózatot *10.241.0.0/16*címmel.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Egyszerű szolgáltatás létrehozása vagy felügyelt identitás használata

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnév automatikusan létrehozható az Azure CLI-vel vagy a portállal, vagy létrehozhat egyet előre, és hozzárendelhet további engedélyeket. Azt is megteheti, hogy egy felügyelt identitás engedélyek helyett egy egyszerű szolgáltatás. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="assign-permissions-to-the-virtual-network"></a>Engedélyek hozzárendelése a virtuális hálózathoz

Ahhoz, hogy a fürt használhassa és kezelhesse a virtuális hálózatot, meg kell adnia az AKS egyszerű szolgáltatásának a megfelelő jogokat a hálózati erőforrások használatához.

Először is, kap a virtuális hálózati erőforrás-azonosító segítségével [az hálózati vnet show:][az-network-vnet-show]

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Ha az AKS-fürt számára megfelelő hozzáférést szeretne biztosítani a virtuális hálózat használatához, hozzon létre egy szerepkör-hozzárendelést az [az szerepkör-hozzárendelés létrehozás][az-role-assignment-create] parancsával. Cserélje le az `<appId`> és `<vnetId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Egy AKS-fürt üzembe helyezése az előző lépésben létrehozott AKS-alhálózatba. Az alhálózat azonosítójának beszereznie [az az hálózati virtuális hálózat alhálózatának megjelenítése][az-network-vnet-subnet-show]használatával:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Cserélje `<subnetId>` le az előző lépésben kapott `<appId>` `<password>` azonosítót, majd az előző szakaszban összegyűjtött értékeket.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Néhány perc múlva befejeződik a parancs végrehajtása, és visszaadja a fürttel kapcsolatos adatokat JSON formátumban.

## <a name="enable-virtual-nodes-addon"></a>Virtuális csomópontok bővítményének engedélyezése

A virtuális csomópontok engedélyezéséhez használja az [aks enable-addons parancsot.][az-aks-enable-addons] A következő példa az előző lépésben létrehozott *myVirtualNodeSubnet* nevű alhálózatot használja:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az [aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. Ebben a lépésben a rendszer hitelesítő adatokat tölt le, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimeneti mutatja az egyetlen Virtuálisgép-csomópont létre, majd a virtuális csomópont Linux, *virtual-node-aci-linux*:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Mintaalkalmazás telepítése

Hozzon létre `virtual-node.yaml` egy elnevezett fájlt, és másolja a következő YAML.Create a file named and copy in the following YAML. A tároló ütemezéséhez a csomópont, [és][node-selector] [a tolerancia][toleration] van definiálva.

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

```console
kubectl apply -f virtual-node.yaml
```

Használja a [kubectl get pods][kubectl-get] parancsot az argumentumot a `-o wide` podok és az ütemezett csomópont listájának kimenetéhez. Figyelje `aci-helloworld` meg, hogy a `virtual-node-aci-linux` pod van ütemezve a csomóponton.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod van rendelve egy belső IP-címet az Azure virtuális hálózati alhálózat delegált virtuális csomópontok használata.

> [!NOTE]
> Ha az Azure Container Registry szolgáltatásban tárolt lemezképeket használja, [konfigurálja és használja a Kubernetes-titkos kulcsot.][acr-aks-secrets] A virtuális csomópontok jelenlegi korlátozása, hogy nem használhatja az integrált Azure AD szolgáltatás egyszerű hitelesítés. Ha nem használ titkos kulcsot, a virtuális csomópontokon ütemezett podok nem `HTTP response status code 400 error code "InaccessibleImage"`indulnak el, és jelentik a hibát.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópontpod tesztelése

A virtuális csomóponton futó pod teszteléséhez keresse meg a bemutató alkalmazást egy webes klienssel. A pod van rendelve egy belső IP-címet, gyorsan tesztelheti ezt a kapcsolatot egy másik pod az AKS-fürtön. Hozzon létre egy tesztpodot, és csatoljon hozzá egy terminálmunkamenetet:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
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

## <a name="remove-virtual-nodes"></a>Virtuális csomópontok eltávolítása

Ha a továbbiakban nem kívánja használni a virtuális csomópontokat, letilthatja őket az [aks disable-addons][az aks disable-addons] paranccsal. 

Ha szükséges, [https://shell.azure.com](https://shell.azure.com) nyissa meg az Azure Cloud Shellt a böngészőjében.

Először törölje `aci-helloworld` a virtuális csomóponton futó podot:

```console
kubectl delete -f virtual-node.yaml
```

A következő példaparancs letiltja a Linux virtuális csomópontjait:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Most távolítsa el a virtuális hálózati erőforrásokat és az erőforráscsoportot:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy pod volt ütemezve a virtuális csomóponton, és hozzárendelt egy privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatás üzembe helyezési és a pod egy terheléselosztó vagy a be- éselőtér-vezérlő keresztül forgalmat. További információ: [Hozzon létre egy alapvető bejövő kapcsolatvezérlőt az AKS-ben.][aks-basic-ingress]

A virtuális csomópontok gyakran az AKS skálázási megoldásának egyik összetevője. A skálázási megoldásokról az alábbi cikkekben talál további információt:

- [A Kubernetes vízszintes pod automatikus skálázó használata][aks-hpa]
- [A Kubernetes-fürt automatikus méretezőjének használata][aks-cluster-autoscaler]
- [Tekintse meg a virtuális csomópontok automatikus skálázási mintáját][virtual-node-autoscale]
- [További információ a Virtual Kubelet nyílt forráskódú könyvtárról][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
