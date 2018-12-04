---
title: Hozzon létre a virtuális csomópontok az Azure Kubernetes-szolgáltatások (AKS) az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre Azure Kubernetes Services-(AKS-) fürtöt használó virtuális csomópontok podok futtatásához az Azure CLI használatával.
services: container-service
author: iainfoulds
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: ee16165352edbacddac0c91f1ff68109982577de
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855957"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Létrehozhat és konfigurálhat egy Azure Kubernetes-szolgáltatások (AKS)-fürtön az Azure CLI használatával virtuális csomópontok használata

Gyorsan méretezhető alkalmazások és szolgáltatások Azure Kubernetes Service (AKS)-fürtben, a virtuális csomópontok használhatók. Virtuális csomópont esetén podok gyors kiépítés rendelkezik, és csak a végrehajtási idejű másodpercenként kell fizetnie. Nem kell a virtuális gép futtatása az újabb podok számítási csomópontok üzembe helyezése Kubernetes-fürt méretező várja. Ez a cikk bemutatja, hogyan hozzon létre és konfigurálja a virtuális hálózati erőforrások és az AKS-fürtöt, majd engedélyezze a virtuális csomópontok.

> [!IMPORTANT]
> Az aks-ben a virtuális csomópontok használata jelenleg **előzetes**. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="before-you-begin"></a>Előkészületek

Virtuális csomópontok ACI futtató podok és az AKS-fürt közötti hálózati kommunikáció engedélyezéséhez. Ahhoz, hogy ez a kommunikáció, egy virtuális hálózat alhálózatának létrehozása és delegált engedélyek vannak rendelve. Virtuális csomópontok csak dolgozhat használatával létrehozott AKS-fürtök *speciális* hálózati. Alapértelmezés szerint az AKS-fürtök létrehozása a *alapszintű* hálózati. Ez a cikk bemutatja, hogyan hozzon létre egy virtuális hálózatot és alhálózatot, majd a speciális hálózati használó egy AKS-fürt üzembe helyezése.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza **kipróbálás** a kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon is elindíthatja a [https://shell.azure.com/bash](https://shell.azure.com/bash) cím megnyitásával. A **Másolás** kiválasztásával másolja és illessze be a kódrészleteket a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha inkább a helyi telepítése és használata a parancssori felület, ehhez a cikkhez az Azure CLI 2.0.49 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Hozzon létre egy virtuális hálózatot a [az network vnet létrehozása] [ az-network-vnet-create] parancsot. Az alábbi példa létrehoz egy virtuális hálózat neve *myVnet* címelőtaggal rendelkező *10.0.0.0/8*, és a egy nevű alhálózatot *myAKSSubnet*. Alapértelmezés szerint ez az alhálózat címelőtagot *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Most hozzon létre egy virtuális csomópontok használatával további alhálózat a [az alhálózaton virtuális hálózat létrehozása] [ az-network-vnet-subnet-create] parancsot. A következő példában létrehozunk egy nevű alhálózatot *myVirtualNodeSubnet* a cím előtagja *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefix 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnév automatikusan létrehozható az Azure CLI-vel vagy a portállal, vagy létrehozhat egyet előre, és hozzárendelhet további engedélyeket.

Hozzon létre egy szolgáltatás egyszerű a [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] parancsot. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```
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

Ahhoz, hogy a fürt használható és felügyelhető a virtuális hálózat, az AKS egyszerű szolgáltatás a hálózati erőforrások használata a megfelelő jogosultságokat kell biztosítania.

Először kérje le a virtuális hálózati erőforrás ID használatával [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Az AKS-fürtöt, a virtuális hálózat használatához megfelelő hozzáférést, hozzon létre egy szerepkör hozzárendelése a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot. Cserélje le az `<appId`> és `<vnetId>` helyőrzőket az előző két lépésben beszerzett értékekre.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Telepít egy AKS-fürtöt az előző lépésben létrehozott AKS-alhálózatban. Az alhálózat használatával Azonosítójának lekéréséhez [az hálózati virtuális hálózat alhálózati show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. Cserélje le `<subnetId>` az az előző lépésben beszerzett Azonosítóját, majd `<appId>` és `<password>` -a 

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

## <a name="enable-virtual-nodes"></a>Engedélyezze a virtuális csomópontok

A virtuális csomópontok összekötő további funkciókat biztosít, használja az Azure CLI-bővítmény. A virtuális csomópontok-összekötő engedélyezése előtt először telepítse a bővítmény használatával a [az bővítmény hozzáadása] [ az-extension-add] parancsot:

```azurecli-interactive
az extension add --source https://aksvnodeextension.blob.core.windows.net/aks-virtual-node/aks_virtual_node-0.2.0-py2.py3-none-any.whl
```

Ahhoz, hogy a virtuális csomópontok, most már használhatja a [az aks enable-bővítmények] [ az-aks-enable-addons] parancsot. Az alábbi példában a nevű alhálózat *myVirtualNodeSubnet* az előző lépésben létrehozta:

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

Az alábbi példa kimenetében látható a létrehozott egyetlen virtuális gép csomópont, majd a virtuális csomópont Linux rendszeren *virtuális-csomópont-aci-linux*:

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Egy mintaalkalmazás üzembe helyezése

Hozzon létre egy fájlt `virtual-node.yaml` másolja be a következő yaml-kódot. A tároló a csomóponton, ütemezni egy [nodeSelector] [ node-selector] és [toleration] [ toleration] vannak definiálva.

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

Futtassa az alkalmazást a [a kubectl a alkalmazni] [ kubectl-apply] parancsot.

```console
kubectl apply -f virtual-node.yaml
```

Használja a [kubectl get pods] [ kubectl-get] parancsot a `-o wide` argumentum a kimenetben podok és az ütemezett csomópont listáját. Figyelje meg, hogy a `aci-helloworld` pod van ütemezve: a `virtual-node-aci-linux` csomópont.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

A pod belső IP-cím van hozzárendelve az Azure virtuális hálózat alhálózatról a virtuális csomópontok segítségével delegált.

## <a name="test-the-virtual-node-pod"></a>A virtuális csomópont pod tesztelése

A pod, a virtuális csomóponton futó teszteléséhez, keresse meg a webes ügyféllel bemutató alkalmazás. A pod belső IP-cím van hozzárendelve, akkor gyorsan tesztelheti e kapcsolat az AKS-fürtöt egy másik podján. Hozzon létre egy teszt pod, és egy terminál-munkamenetben csatlakoztatása:

```console
kubectl run -it --rm virtual-node-test --image=debian
```

Telepítés `curl` be a pod `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Most már elérhető a cím a pod használatának `curl`, mint például *http://10.241.0.4*. Adja meg a saját belső IP-címét, ahogyan az előző `kubectl get pods` parancsot:

```console
curl -L http://10.241.0.4
```

A bemutató alkalmazás jelenik meg, ahogyan az a következő sűrített példához kimenet:

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Zárja be a terminál-munkamenetben, a teszt rendelkező podot `exit`. Ha a munkamenet véget ér, a törölt a pod.

## <a name="remove-virtual-nodes"></a>Távolítsa el a virtuális csomópontok

Ha már nem szeretné használni a virtuális csomópontok, letilthatja azokat használja a [az aks disable-bővítmények] [ az aks disable-addons] parancsot. A következő példa letiltja a virtuális Linux-csomópontok:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Most távolítsa el a virtuális hálózati erőforrások és -erőforráscsoport:

```azurecli-interactive
# Change the name of your resource group and network resources as needed
RES_GROUP=myResourceGroup

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name myVnet --name myAKSSubnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name myVnet --name myAKSSubnet --remove delegations 0
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy pod lett ütemezve a virtuális csomópont, és privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatás üzembe helyezésének és irányíthatja a forgalmat a terheléselosztó vagy a bejövőforgalom-vezérlőt a pod. További információkért lásd: [az aks-ben hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez][aks-basic-ingress].

Virtuális csomópontok gyakran történik az aks-ben egy méretezési megoldás egyik összetevője. Méretezési megoldásokra további információkért tekintse meg a következő cikkeket:

- [A Kubernetes-podok horizontális méretező használatához][aks-hpa]
- [A Kubernetes-fürt méretező használatához][aks-cluster-autoscaler]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/

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
