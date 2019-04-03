---
title: Hozzon létre a virtuális csomópontok az Azure Kubernetes-szolgáltatások (AKS) az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre Azure Kubernetes Services-(AKS-) fürtöt használó virtuális csomópontok podok futtatásához az Azure CLI használatával.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 54c8e44685bb69e845c819b0c2846b188a771d71
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878230"
---
# <a name="preview---create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Előzetes verzió – létrehozása és konfigurálása az Azure Kubernetes szolgáltatás (AKS-) fürt használata a virtuális csomópontok az Azure CLI használatával

Gyorsan méretezhető alkalmazások és szolgáltatások Azure Kubernetes Service (AKS)-fürtben, a virtuális csomópontok használhatók. Virtuális csomópont esetén podok gyors kiépítés rendelkezik, és csak a végrehajtási idejű másodpercenként kell fizetnie. Nem kell a virtuális gép futtatása az újabb podok számítási csomópontok üzembe helyezése Kubernetes-fürt méretező várja. Ez a cikk bemutatja, hogyan hozzon létre és konfigurálja a virtuális hálózati erőforrások és az AKS-fürtöt, majd engedélyezze a virtuális csomópontok.

> [!IMPORTANT]
> Az AKS előzetes verziójú funkciók a következők: az önkiszolgáló és vehetnek részt. Visszajelzés és hibák gyűjtsön közösségünkhöz előzetes verziók vannak megadva. Azonban nem támogatja őket az Azure műszaki támogatást. Hozzon létre egy fürtöt, vagy adja hozzá ezeket a funkciókat a meglévő fürtökre, ha a fürt nem támogatott, mindaddig, amíg a funkció már nem előzetes verzióban érhető el és hallgatóknak az általánosan elérhető (GA).
>
> Ha az előzetes verziójú szolgáltatásaihoz is problémák merülnek fel [nyisson egy problémát a AKS GitHub-adattárat a] [ aks-github] az előzetes verziójú funkció a bejelentett hiba címét nevére.

## <a name="before-you-begin"></a>Előkészületek

Virtuális csomópontok ACI futtató podok és az AKS-fürt közötti hálózati kommunikáció engedélyezéséhez. Ahhoz, hogy ez a kommunikáció, egy virtuális hálózat alhálózatának létrehozása és delegált engedélyek vannak rendelve. Virtuális csomópontok csak dolgozhat használatával létrehozott AKS-fürtök *speciális* hálózati. Alapértelmezés szerint az AKS-fürtök létrehozása a *alapszintű* hálózati. Ez a cikk bemutatja, hogyan hozzon létre egy virtuális hálózatot és alhálózatot, majd a speciális hálózati használó egy AKS-fürt üzembe helyezése.

Ha korábban nem használta az ACI, regisztrálja a szolgáltatót az előfizetés. Az ACI szolgáltató regisztrációs használatával állapotát ellenőrizheti a [az identitásszolgáltató-listája] [ az-provider-list] parancsot, az alábbi példában látható módon:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

A *Microsoft.ContainerInstance* szolgáltató kell jelentse *regisztrált*, ahogy az alábbi példa kimenetében látható:

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Ha a szolgáltató állapota *NotRegistered*, regisztrálja a szolgáltatót használatával a [az provider register] [ az-provider-register] az alábbi példában látható módon:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A következő régiók virtuális csomópont központi telepítések támogatottak:

* Kelet-Ausztrália (Kelet-Ausztrália)
* USA keleti RÉGIÓJA (USA keleti régiója)
* USA nyugati középső Régiója (régiója)
* Nyugat-Európa (westeurope)
* USA nyugati RÉGIÓJA (westus)

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

## <a name="enable-virtual-nodes-addon"></a>Virtuális csomópontok bővítmény engedélyezése

Ahhoz, hogy a virtuális csomópontok, most már használhatja a [az aks enable-bővítmények] [ az-aks-enable-addons] parancsot. Az alábbi példában a nevű alhálózat *myVirtualNodeSubnet* az előző lépésben létrehozta:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```
> [!NOTE]
> Ha egy virtuális csomópont nem található hibaüzenet kap, szükség lehet a CLI-bővítmény telepítése 
> ```azurecli-interactive
> az extension add --source https://aksvnodeextension.blob.core.windows.net/aks-virtual-node/aks_virtual_node-0.2.0-py2.py3-none-any.whl
> ```

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

> [!NOTE]
> Ha az Azure Container Registryben tárolt rendszerképek [konfigurálhatja és használhatja a Kubernetes titkos][acr-aks-secrets]. A jelenlegi korlátozás miatt a virtuális csomópontok előzetes, hogy nem használható az Azure AD egyszerű szolgáltatás hitelesítése integrálva. Ha egy titkos kulcsot nem használja, a virtuális csomópontok ütemezett podok sikertelen elindításához, és jelentse a hibát `HTTP response status code 400 error code "InaccessibleImage"`.

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
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --remove delegations 0
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy pod lett ütemezve a virtuális csomópont, és privát, belső IP-címet. Ehelyett létrehozhat egy szolgáltatás üzembe helyezésének és irányíthatja a forgalmat a terheléselosztó vagy a bejövőforgalom-vezérlőt a pod. További információkért lásd: [az aks-ben hozzon létre egy alapszintű bejövőforgalom-vezérlőjéhez][aks-basic-ingress].

Virtuális csomópontok gyakran történik az aks-ben egy méretezési megoldás egyik összetevője. Méretezési megoldásokra további információkért tekintse meg a következő cikkeket:

- [A Kubernetes-podok horizontális méretező használatához][aks-hpa]
- [A Kubernetes-fürt méretező használatához][aks-cluster-autoscaler]
- [Tekintse meg az automatikus skálázási minta virtuális csomópontok][virtual-node-autoscale]
- [További információ a Virtual Kubelet nyílt forráskódú kódtár][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

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
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
