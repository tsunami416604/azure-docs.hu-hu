---
title: Privát Azure Kubernetes Service-fürt létrehozása
description: Ismerje meg, hogyan hozhat létre egy privát Azure Kubernetes Service-(ak-) fürtöt
services: container-service
ms.topic: article
ms.date: 6/18/2020
ms.openlocfilehash: c788f2009bdc771bcdde20d1c3dbe9eafdbcffcb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244225"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Privát Azure Kubernetes Service-fürt létrehozása

Egy privát fürtben a vezérlő síkja vagy az API-kiszolgáló belső IP-címekkel rendelkezik, amelyek a [magánhálózati internetes dokumentumok RFC1918](https://tools.ietf.org/html/rfc1918) vannak meghatározva. Privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópont-készletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlő síkja vagy az API-kiszolgáló egy Azure Kubernetes szolgáltatásban (ak) felügyelt Azure-előfizetésben található. Az ügyfél fürtje vagy csomópont-készlete az ügyfél előfizetésében található. A kiszolgáló és a fürt vagy a csomópont-készlet képes kommunikálni egymással az API-kiszolgáló virtuális hálózatának [Azure Private link szolgáltatásával][private-link-service] , valamint egy olyan privát végponttal, amely az ügyfél AK-fürt alhálózatán van kitéve.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.2.0 vagy újabb verziója

## <a name="create-a-private-aks-cluster"></a>Privát AK-fürt létrehozása

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az AK-fürthöz.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Alapértelmezett alapszintű hálózatkezelés 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Where *--enable-Private-cluster* kötelező jelző egy privát fürthöz. 

### <a name="advanced-networking"></a>Speciális hálózatkezelés  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
Where *--enable-Private-cluster* kötelező jelző egy privát fürthöz. 

> [!NOTE]
> Ha a Docker-híd CIDR (172.17.0.1/16) ütközne az alhálózati CIDR, módosítsa a Docker-híd megfelelőjét.

## <a name="options-for-connecting-to-the-private-cluster"></a>A privát fürthöz való csatlakozás lehetőségei

Az API-kiszolgáló végpontjának nincs nyilvános IP-címe. Az API-kiszolgáló kezeléséhez olyan virtuális gépet kell használnia, amely hozzáféréssel rendelkezik az AK-fürt Azure-Virtual Networkához (VNet). Több lehetőség is van a magánhálózati kapcsolat létrehozására a privát fürthöz.

* Hozzon létre egy virtuális gépet ugyanabba az Azure-Virtual Networkba (VNet), mint az AK-fürtöt.
* Használjon különálló hálózatban található virtuális GÉPET, és állítsa be a [virtuális hálózatok][virtual-network-peering]közötti társítást.  Erről a lehetőségről az alábbi szakaszban talál további információt.
* [Express Route-vagy VPN-][express-route-or-VPN] kapcsolat használata.

A legegyszerűbb lehetőség a virtuális gép létrehozása ugyanabban a VNET, mint az AK-fürt.  Az expressz útvonal és a VPN-EK növelik a költségeket és további hálózati bonyolultságot igényelnek.  A virtuális hálózat társításához meg kell terveznie a hálózati CIDR-tartományokat, hogy ne legyenek átfedésben lévő tartományok.

## <a name="virtual-network-peering"></a>Virtuális hálózati társviszony

Ahogy említettük, a VNet-társítás az egyik módja a privát fürt elérésének. A VNet-társítás használatához létre kell hoznia egy kapcsolatot a virtuális hálózat és a magánhálózati DNS-zóna között.
    
1. Nyissa meg a Azure Portal csomópont-erőforráscsoportot.  
2. Válassza ki a magánhálózati DNS-zónát.   
3. A bal oldali ablaktáblán válassza ki a **virtuális hálózati** kapcsolatot.  
4. Hozzon létre egy új hivatkozást, amely hozzáadja a virtuális gép virtuális hálózatát a magánhálózati DNS-zónához. Néhány percet vesz igénybe, amíg a DNS-zóna hivatkozása elérhetővé válik.  
5. A Azure Portal navigáljon a fürt VNet tartalmazó erőforráscsoporthoz.  
6. A jobb oldali ablaktáblában válassza ki a virtuális hálózatot. A virtuális hálózat neve: *AK-vnet- \* *.  
7. A bal oldali ablaktáblán válassza **a**társítások lehetőséget.  
8. Válassza a **Hozzáadás**lehetőséget, adja hozzá a virtuális gép virtuális hálózatát, majd hozza létre a társítást.  
9. Nyissa meg a virtuális hálózatot, ahol a virtuális gép rendelkezik **, válassza a társítások lehetőséget,** válassza ki az AK-beli virtuális hálózatot, majd hozza létre a társítást. Ha a címtartomány az AK-beli virtuális hálózaton és a virtuális gép virtuális hálózatának összevonásán alapul, a társítás sikertelen lesz. További információ: [Virtual Network peering][virtual-network-peering].

## <a name="hub-and-spoke-with-custom-dns"></a>A hub és a küllő egyéni DNS-sel

A központilag [és küllős architektúrákat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) gyakran használják az Azure-beli hálózatok üzembe helyezésére. Ezen telepítések többségében a küllős virtuális hálózatok DNS-beállításai úgy vannak konfigurálva, hogy egy központi DNS-továbbítóra hivatkozzon, amely lehetővé teszi a helyszíni és az Azure-alapú DNS-feloldást. Ha egy AK-fürtöt egy ilyen hálózati környezetben helyez üzembe, néhány speciális szempontot figyelembe kell venni.

![Privát fürt hub és küllő](media/private-clusters/aks-private-hub-spoke.png)

1. Alapértelmezés szerint a rendszer a fürt által felügyelt erőforráscsoporthoz (1) és egy privát DNS-zónát (2) hoz létre egy privát fürt üzembe helyezésekor. A fürt egy rekordot használ a privát zónában az API-kiszolgálóval való kommunikációhoz használt privát végpont IP-címének feloldásához.

2. A magánhálózati DNS-zóna csak azon VNet van csatolva, amelyhez a fürtcsomópontok csatlakoznak (3). Ez azt jelenti, hogy a magánhálózati végpontot csak az adott csatolt VNet lévő gazdagépek oldják fel. Olyan esetekben, amikor nincs egyéni DNS konfigurálva a VNet (alapértelmezett), ez nem jelent problémát, mert a 168.63.129.16 for DNS-ben a gazdagépek is feloldják a rekordokat a magánhálózati DNS-zónában, a hivatkozás miatt.

3. Olyan esetekben, amikor a fürtöt tartalmazó VNet egyéni DNS-beállításokkal rendelkezik (4), a fürt üzembe helyezése meghiúsul, ha a magánhálózati DNS-zóna az egyéni DNS-feloldókat (5) tartalmazó VNet van társítva. Ez a hivatkozás manuálisan hozható létre, miután a privát zóna létrejött a fürt kiépítése során vagy az automatizáláson keresztül, amikor az eseményvezérelt központi telepítési mechanizmusok használatával észleli a zóna létrehozását (például Azure Event Grid és Azure Functions).

## <a name="dependencies"></a>Függőségek  

* A Private link Service csak a standard Azure Load Balancer esetén támogatott. Az alapszintű Azure Load Balancer nem támogatott.  
* Ha egyéni DNS-kiszolgálót szeretne használni, adja hozzá a Azure DNS IP-168.63.129.16 a felsőbb rétegbeli DNS-kiszolgálóként az egyéni DNS-kiszolgálón.

## <a name="limitations"></a>Korlátozások 
* Az IP-címekre jogosult tartományok nem alkalmazhatók a privát API-kiszolgálói végpontra, csak a nyilvános API-kiszolgálóra érvényesek.
* A [Availability Zones][availability-zones] jelenleg bizonyos régiókban támogatottak. 
* Az [Azure Private link Service korlátozásai][private-link-service] a privát fürtökre vonatkoznak.
* Az Azure DevOps nem támogatja a Microsoft által üzemeltetett ügynököket privát fürtökkel. Érdemes lehet saját üzemeltetésű [ügynököket][devops-agents]használni. 
* Azon ügyfelek számára, akik számára engedélyezni kell a Azure Container Registryt a privát AK-val való munkavégzéshez, az Container Registry virtuális hálózatot az ügynök-fürt virtuális hálózatának kell megadnia.
* Nincs aktuális támogatás az Azure dev Spaces szolgáltatáshoz
* Meglévő AK-fürtök privát fürtökre való konvertálása nem támogatott
* Ha törli vagy módosítja a magánhálózati végpontot az ügyfél alhálózatán, a fürt működése leáll. 
* A tárolók élő adatAzure Monitor jelenleg nem támogatottak.
* A rendelkezésre állási SLA jelenleg nem támogatott.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md
