---
title: Az Azure Kubernetes Service (AKS) hálózat kubenet konfigurálása
description: Ismerje meg, hogyan kubenet (alapszintű) hálózat konfigurálása az Azure Kubernetes Service (AKS) helyezheti üzembe egy AKS-fürtöt egy meglévő virtuális hálózatot és alhálózatot.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/31/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: b80177d17e0dc5a4e54396907ecee61890ec523f
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011347"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Hálózatkezelés a saját IP-címtartományok Azure Kubernetes Service (AKS) kubenet használata

Alapértelmezés szerint az AKS-fürtök használata [kubenet][kubenet], és a egy Azure virtuális hálózatot és alhálózatot jönnek létre az Ön számára. A *kubenet*, csomópontok IP-cím lekérése az Azure virtuális hálózat alhálózatához. Podok logikailag különböző címtér az IP-címet kapnak a csomópontok az Azure virtuális hálózat alhálózatához. Hálózati címfordítás (NAT) majd van konfigurálva, így a podok elérheti az erőforrásokat az Azure-beli virtuális hálózaton. A forgalmat a forrás IP-címe NAT lenne a csomópont elsődleges IP-cím. Ez a megközelítés nagymértékben csökkenti a podok használatára a hálózati tárhelyre fenn kell IP-címek számát.

A [Azure tároló-hálózati adapter (CNI)][cni-networking], minden pod IP-címet kap az alhálózatról, és közvetlenül is elérhetők. Ezen IP-címek a hálózati hely belül egyedinek kell lennie, és előre kell készülni. Minden egyes csomópont rendelkezik egy konfigurációs paraméter, amely támogatja a podok maximális számát. IP-címek száma csomópontonként megfelelőjét majd számára vannak fenntartva meghozni a csomóponton. Ez a megközelítés további tervezést igényel, és IP-cím Erőforrásfogyás vagy kell építeni a teljesítményszinten növekedésével nagyobb alhálózat a fürtök gyakran vezet.

Ez a cikk bemutatja, hogyan használható *kubenet* hálózatkezelés hozhat létre és használhat a virtuális hálózat alhálózatának az AKS-fürt. Hálózati lehetőségek és megfontolandó szempontok a további információkért lásd: [fogalmak hálózati Kubernetes, az AKS][aks-network-concepts].

## <a name="before-you-begin"></a>Előkészületek

Az Azure CLI 2.0.56 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>A saját alhálózattal rendelkező hálózat kubenet áttekintése

Sok környezetben definiált virtuális hálózatok és alhálózatok a lefoglalt IP-címtartományok. Ezek a virtuális hálózati erőforrások segítségével több szolgáltatásokat és alkalmazásokat támogatja. Adja meg a hálózati kapcsolatot, az AKS-fürt segítségével *kubenet* (alapszintű hálózatkezelési) vagy Azure CNI (*speciális hálózatkezelés*).

A *kubenet*, csak a csomópontok kapnak IP-címet a virtuális hálózat alhálózatán. Podok nem kommunikálnak közvetlenül egymással. Ehelyett a felhasználó által megadott útválasztás (UDR) és IP-továbbítás podok csomópont közötti kapcsolatot szolgál. Podok mögött egy olyan szolgáltatás, amely megkapja a hozzárendelt IP-címet is telepíthet, és elosztja a forgalmat az alkalmazáshoz. Az alábbi ábrán látható, hogy az AKS-csomópontok IP-címet kapnak a virtuális hálózat alhálózatához, de nem a podok:

![Az AKS-fürt Kubenet hálózati modell](media/use-kubenet/kubenet-overview.png)

Az Azure a 400 útvonalak legfeljebb egy udr-t, a támogatja, így nem lehet nagyobb, mint 400 csomópontok egy AKS-fürtöt. Az AKS szolgáltatások, például a [virtuális csomópontok] [ virtual-nodes] vagy a hálózati szabályzatok nem támogatottak olyan *kubenet*.

A *Azure CNI*, minden egyes pod IP-alhálózat IP-címet kap, és közvetlenül kommunikálhatnak más podok és -szolgáltatásokhoz. Lehet, hogy a fürtök akkora, mint a megadott IP-címtartományt. Azonban az IP-címtartományt előre kell készülni, és az összes IP-címeket használnak fel az AKS-csomópontok támogatására képes podokat maximális száma alapján. Speciális hálózati funkciók és alkalmazási helyzetek például [virtuális csomópontok] [ virtual-nodes] vagy hálózati házirendek használata támogatott *Azure CNI*.

### <a name="ip-address-availability-and-exhaustion"></a>IP-cím elérhetőségét és az Erőforrásfogyás

A *Azure CNI*, gyakori probléma a hozzárendelt IP-címtartomány túl kicsi, majd további csomópontok hozzáadása, méretezése vagy a fürt frissítése. A csoport nem is tud kiadni egy elég nagy IP-címtartomány a várt teljesítményszinten támogatásához.

Biztonság sérülését, mint használó egy AKS-fürtöt hozhat létre *kubenet* és a egy meglévő virtuális hálózat alhálózatához csatlakozik. Ez a megközelítés lehetővé teszi, hogy a csomópontok megadott IP-címek fenntartott IP-címek meghozni a potenciális podok futtatható a fürt összes nagy számú nélkül kapnak.

A *kubenet*, egy sokkal kisebb IP-címtartományt használjon, és képes támogatni a nagy méretű fürtök és alkalmazások számára. Például akkor is igaz az olyan */27* IP-címtartományt, a futhat egy 20-25 csomópontot tartalmazó fürt méretezése vagy frissítéséhez elegendő hely a. A fürt mérete legfeljebb támogatná *2200-2750* podok (az alapértelmezett legfeljebb 110 podok száma csomópontonként).

A következő alapvető számítások a különbség a hálózati modellek összehasonlítása:

- **kubenet** – egy egyszerű */24* IP-címtartományt is támogatja az akár *251* (minden egyes Azure virtuális hálózat alhálózatának fenntartja az első három IP-címek felügyeleti műveletek) a fürtben található csomópontok
  - A csomópontok száma legfeljebb támogathatják *27,610* podok (alapértelmezett legfeljebb 110 podok a csomópontonkénti *kubenet*)
- **Az Azure CNI** –, hogy ugyanazon alapszintű */24* alhálózati címtartomány csak támogatja a legfeljebb *8* a fürtben található csomópontok
  - A csomópontok száma legfeljebb csak támogatni *240* podok (alapértelmezett legfeljebb 30 podok a csomópontonkénti *Azure CNI*)

> [!NOTE]
> Ezek maximális értékei nem figyelembe a fiók frissítését vagy csoport skálázási műveletei. A gyakorlatban nem futtatható, amely támogatja az alhálózati IP-címtartományt csomópontok maximális számát. Méretezési csoport frissítési műveletek során meg kell hagyni néhány IP-cím használható.

### <a name="virtual-network-peering-and-expressroute-connections"></a>Virtuális hálózatok közötti társviszony és ExpressRoute-kapcsolatok

A helyszíni kapcsolatot, mindkettő *kubenet* és *Azure-CNI* hálózati módszer használható [Azure virtuális hálózatok közötti társviszony] [ vnet-peering]vagy [az ExpressRoute-kapcsolatok][express-route]. Tervezze meg gondosan megakadályozására között átfedés van, és helytelen forgalom-útválasztást, az IP-címtartományok. Például használja a túl sok a helyszíni hálózathoz egy *10.0.0.0/8* -címtartományt, amely az ExpressRoute-kapcsolaton keresztül hirdeti meg. Azt javasoljuk, hogy hozzon létre például az AKS-fürtök a címtartományon kívül az Azure virtual network alhálózatokra *172.26.0.0/16*.

### <a name="choose-a-network-model-to-use"></a>Válassza ki a használandó hálózati modellt

A választás, mely hálózati beépülő modul használata az AKS-fürt általában rugalmasságot és a speciális konfigurációt igénylő közötti egyensúly. Az alábbi szempontokat vázlat segítségével, ha lehet, hogy minden egyes hálózati modell a legmegfelelőbb.

Használat *kubenet* során:

- IP-címterület korlátozott számú.
- A pod kommunikáció többsége a fürtön belül van.
- Nincs szükség speciális funkciókat, például virtuális csomópontok vagy a hálózati házirend.

Használat *Azure CNI* során:

- Elérhető IP-címtér van.
- A pod kommunikációt a legtöbb, hogy a fürtön kívüli erőforrásokhoz.
- Nem kívánja az udr-EK kezelése.
- Speciális funkciók, például a virtuális csomópontok vagy a hálózati házirend van szüksége.

> [!NOTE]
> Kuberouter lehetővé teszi a hálózati házirend engedélyezése kubenet használatakor és a egy AKS-fürt a daemonset is telepíthető. Felhívjuk a figyelmét arra, kube-útválasztó bétaverzióban van, és nem támogatott érhető el a Microsoft által a projekthez.

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A használatának első lépései *kubenet* és a saját virtuális hálózat alhálózatán, először hozzon létre egy erőforrás csoport a [az csoport létrehozása] [ az-group-create] parancsot. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *EastUS* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Ha nem rendelkezik egy meglévő virtuális hálózatot és alhálózatot használni, hozzon létre a hálózati erőforrásokhoz a [az network vnet létrehozása] [ az-network-vnet-create] parancsot. A következő példában a virtuális hálózat neve *myVnet* a cím előtagja *10.0.0.0/8*. Egy alhálózat létre van hozva elnevezett *myAKSSubnet* a címelőtaggal rendelkező *10.240.0.0/16*.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

## <a name="create-a-service-principal-and-assign-permissions"></a>Egyszerű szolgáltatás létrehozása és az engedélyek hozzárendelése

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. A szolgáltatásnévnek kell a virtuális hálózatot és alhálózatot, amelyet az AKS-csomópontok használata kezelheti engedélyekkel kell rendelkeznie. Egyszerű szolgáltatás létrehozásához használja a [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] parancsot:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Az alábbi példa kimenetében látható az alkalmazás Azonosítóját és jelszavát a szolgáltatásnévhez tartozó. Ezekkel az értékekkel a további lépésekben rendelhet egy szerepkört a szolgáltatásnévhez, és ezután az AKS-fürt létrehozása:

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

Rendelje hozzá a megfelelő delegálásokat a hátralévő lépések során, használja a [az network vnet show] [ az-network-vnet-show] és [az hálózati virtuális hálózat alhálózati show] [ az-network-vnet-subnet-show] parancsokat a szükséges erőforrás-azonosítók beolvasása. Változóként tárolt, a hátralévő lépések során hivatkozott erőforrások azonosítóit a részletekben:

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

Most már az AKS-fürthöz tartozó egyszerű szolgáltatás hozzárendelése *közreműködői* engedélyeket a virtuális hálózat használatával a [az szerepkör-hozzárendelés létrehozása] [ az-role-assignment-create] parancsot. Adja meg a saját  *\<appId >* , ahogyan az egyszerű szolgáltatás létrehozása az előző parancs kimenete:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AKS-fürt létrehozása a virtuális hálózatban

Sikeresen létrehozott egy virtuális hálózatot és alhálózatot, és a létrehozott és hozzárendelt egy egyszerű szolgáltatás engedélyeit a hálózati erőforrások használata. Most hozzon létre egy AKS-fürt a virtuális hálózat és alhálózat használatával a [az aks létrehozása] [ az-aks-create] parancsot. Adja meg a saját szolgáltatásnév  *\<appId >* és  *\<jelszó >*, ahogyan az egyszerű szolgáltatás létrehozása az előző parancs kimenetében.

A következő IP-címtartományok is vannak meghatározva, a fürt létrehozása a folyamat:

* A *--service-cidr* szolgál az AKS-fürtöt a belső szolgáltatások IP-címet hozzárendelni. Az IP-címtartomány egy nem használt más részében lévő a hálózati környezet címteret kell lennie. Ez magában foglalja a helyszíni hálózat tartományokat csatlakozzon, illetve szeretne csatlakozni, az Azure virtuális hálózatok az Express Route vagy egy helyek közötti VPN-kapcsolatok használatával.

* A *ip---dns-szolgáltatás* címének kell lennie a *.10* a szolgáltatás IP-címtartomány-címét.

* A *--pod-cidr* kell lennie a nem használt más részében lévő a hálózati környezet nagy címtér. Ez magában foglalja a helyszíni hálózat tartományokat csatlakozzon, illetve szeretne csatlakozni, az Azure virtuális hálózatok az Express Route vagy helyek közötti VPN-kapcsolat használatával.
    * Ez a címtartomány elég nagy csomópontok várhatóan fel kell lennie. Ez a címtartomány nem módosítható, ha több címet van szüksége további csomópontokat a fürt üzembe helyezése után.
    * A pod IP-címtartomány segítségével hozzárendelni egy */24* címtér a fürt egyes csomópontjaihoz. A következő példában a *--pod-cidr* , *192.168.0.0/16* rendeli hozzá az első fürtcsomópont *192.168.0.0/24*, a második csomópont *192.168.1.0/24*, és a harmadik csomópont *192.168.2.0/24*.
    * A fürt skálázását követve rugalmasan méretezhető, vagy frissítéseket az Azure platform egy pod IP-címtartományt rendelni minden új csomópont továbbra is.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 192.168.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

## <a name="associate-network-resources-with-the-node-subnet"></a>Hálózati erőforrások társítani a csomópont-alhálózat

Amikor egy AKS-fürtöt hoz létre, egy hálózati biztonsági csoport és az útválasztási táblázat jönnek létre. A hálózati erőforrások kezeli az AKS vezérlősík és létrehozásakor és szolgáltatások frissítése. A következő társítsa a hálózati biztonsági csoport és az útválasztási táblázat a virtuális hálózati alhálózat:

```azurecli-interactive
# Get the MC_ resource group for the AKS cluster resources
MC_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)

# Get the route table for the cluster
ROUTE_TABLE=$(az network route-table list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Get the network security group
NODE_NSG=$(az network nsg list -g ${MC_RESOURCE_GROUP} --query "[].id | [0]" -o tsv)

# Update the subnet to associate the route table and network security group
az network vnet subnet update \
    --route-table $ROUTE_TABLE \
    --network-security-group $NODE_NSG \
    --ids $SUBNET_ID
```

## <a name="next-steps"></a>További lépések

Az AKS-fürt üzembe helyezve a meglévő virtuális hálózat alhálózatán most már használhatja a fürt szokásos módon. – Első lépések [alkalmazásokat az Azure-fejlesztési szóközzel] [ dev-spaces] vagy [Draft segítségével][use-draft], vagy [Helmhasználatávalalkalmazásokközpontitelepítése] [use-helm].

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
