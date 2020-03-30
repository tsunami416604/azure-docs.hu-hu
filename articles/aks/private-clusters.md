---
title: Privát Azure Kubernetes-szolgáltatásfürt létrehozása
description: Ismerje meg, hogyan hozhat létre privát Azure Kubernetes-fürt (AKS) fürtöt
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499309"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>Privát Azure Kubernetes-szolgáltatásfürt létrehozása

Egy privát fürtben a vezérlősík vagy AZ API-kiszolgáló rendelkezik az [RFC1918 – A magáninternet-címek címei](https://tools.ietf.org/html/rfc1918) dokumentumban meghatározott belső IP-címekkel. Egy privát fürt használatával biztosíthatja, hogy az API-kiszolgáló és a csomópontkészletek közötti hálózati forgalom csak a magánhálózaton maradjon.

A vezérlősík vagy API-kiszolgáló egy Azure Kubernetes-szolgáltatás (AKS) által felügyelt Azure-előfizetésben található. Az ügyfél fürt- vagy csomópontkészlete az ügyfél előfizetésében található. A kiszolgáló és a fürt vagy csomópontkészlet kommunikálhat egymással az [Azure Private Link szolgáltatáson][private-link-service] keresztül az API-kiszolgáló virtuális hálózatában és egy privát végponton keresztül, amely az ügyfél AKS-fürtjének alhálózatában van elérhető.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure CLI 2.2.0-s vagy újabb verziója

## <a name="create-a-private-aks-cluster"></a>Saját AKS-fürt létrehozása

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévő erőforráscsoportot az AKS-fürthöz.

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>Alapértelmezett alaphálózati kapcsolat 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
Ahol *--enable-private-cluster* kötelező jelző egy privát fürthöz. 

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
Ahol *--enable-private-cluster* kötelező jelző egy privát fürthöz. 

> [!NOTE]
> Ha a Docker-híd cidr (172.17.0.1/16) ütközik az alhálózat CIDR, módosítsa a Docker-híd címét megfelelően.

## <a name="options-for-connecting-to-the-private-cluster"></a>A magánfürthöz való csatlakozás lehetőségei

Az API-kiszolgáló végpontja nem rendelkezik nyilvános IP-címmel. Az API-kiszolgáló kezeléséhez egy virtuális gépet kell használnia, amely hozzáfér az AKS-fürt Azure virtuális hálózatához (VNet). A magánfürthöz való hálózati kapcsolat létrehozására számos lehetőség kínálkodik.

* Hozzon létre egy virtuális gépet ugyanabban az Azure virtuális hálózatban (VNet) az AKS-fürttel.
* Használjon virtuális gépet egy külön hálózatban, és állítsa be [a virtuális hálózati társviszony-létesítést.][virtual-network-peering]  Erről a lehetőségről az alábbi szakaszban olvashat bővebben.
* Használjon [expressz útvonal- vagy VPN-kapcsolatot.][express-route-or-VPN]

Virtuális gép létrehozása ugyanabban a virtuális hálózatban, mint az AKS-fürt a legegyszerűbb megoldás.  Az Expressz útvonal és a VPN-ek növelik a költségeket, és további hálózati összetettséget igényelnek.  A virtuális hálózati társviszony-létesítéshez meg kell terveznie a hálózati CIDR-tartományokat, hogy megbizonyosodjon arról, hogy nincsenek átfedések.

## <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között

Mint említettük, a virtuális hálózat társviszony-létesítés egyik módja a privát fürt eléréséhez. A virtuális hálózati társviszony-létesítés használatához létre kell hoznia egy kapcsolatot a virtuális hálózat és a magán DNS-zóna között.
    
1. Nyissa meg a MC_* erőforráscsoportot az Azure Portalon.  
2. Válassza ki a privát DNS-zónát.   
3. A bal oldali ablaktáblában jelölje ki a **Virtuális hálózati** kapcsolatot.  
4. Hozzon létre egy új hivatkozást a virtuális gép virtuális hálózatának a privát DNS-zónához való hozzáadásához. A DNS-zóna hivatkozása néhány percet vesz igénybe.  
5. Lépjen vissza a MC_* erőforráscsoportaz Azure Portalon.  
6. A jobb oldali ablaktáblában jelölje ki a virtuális hálózatot. A virtuális hálózat neve *aks-vnet-\** formában van.  
7. A bal oldali ablaktáblában válassza a **Társviszony-létesítések**lehetőséget.  
8. Válassza **a Hozzáadás**lehetőséget, adja hozzá a virtuális gép virtuális hálózatát, majd hozza létre a társviszony-létesítést.  
9. Nyissa meg azt a virtuális hálózatot, ahol a virtuális gép rendelkezik, válassza a **Társviszony-létesítések**lehetőséget, válassza ki az AKS virtuális hálózatot, majd hozza létre a társviszony-létesítést. Ha a cím tartományok az AKS virtuális hálózat és a virtuális gép virtuális hálózati összecsapása, társviszony-létesítés sikertelen lesz. További információt a [Virtuális hálózati társviszony-létesítés][virtual-network-peering]című témakörben talál.

## <a name="dependencies"></a>Függőségek  

* A Private Link szolgáltatás csak a standard Azure load balancer támogatott. Az egyszerű Azure load balancer nem támogatott.  
* Egyéni DNS-kiszolgáló használatához adja hozzá az Azure DNS IP 168.63.129.16-ot az egyéni DNS-kiszolgáló felső áramú DNS-kiszolgálójaként.

## <a name="limitations"></a>Korlátozások 
* Az IP-engedélyezett tartományok nem alkalmazhatók a privát API-kiszolgáló végpontjára, csak a nyilvános API-kiszolgálóra vonatkoznak
* A rendelkezésre állási zónák jelenleg bizonyos régiókban támogatottak, lásd a dokumentum kezdetét 
* [Az Azure Private Link szolgáltatás korlátozásai][private-link-service] a privát fürtökre, az Azure privát végpontjaira és a virtuális hálózati szolgáltatás végpontjaira vonatkoznak, amelyek jelenleg nem támogatottak ugyanabban a virtuális hálózatban.
* Nincs támogatás a privát fürtvirtuális csomópontjaihoz a privát Azure Container Instances (ACI) privát Azure virtuális hálózatban való pörgetéséhez
* Nincs támogatás az Azure DevOps-integrációhoz a magánfürtökkel
* Az ügyfelek, amelyek nek engedélyezniük kell az Azure Container Registry, hogy működjön együtt a privát AKS, a Container Registry virtuális hálózat társviszonyt kell létesíteni az ügynök fürt virtuális hálózat.
* Nincs aktuális támogatás az Azure dev spaces
* Nem támogatott a meglévő AKS-fürtök magánfürtökké alakítása
* A privát végpont törlése vagy módosítása az ügyfél alhálózatában a fürt működésének leállását eredményezi. 
* Az Azure Monitor a tárolók live data jelenleg nem támogatott.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

