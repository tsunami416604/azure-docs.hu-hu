---
title: Felhasználó által megadott útvonalak (UDR-EK) testreszabása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan határozhat meg egyéni kimenő útvonalakat az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: d98261629f9e1612966bf74084ae0e0fa70d3605
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829252"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Fürt kilépésének testreszabása felhasználó által megadott útvonallal

Az AK-fürtökből való kilépések testreszabhatók az adott forgatókönyvek kihasználása érdekében. Alapértelmezés szerint az AK kiépít egy szabványos SKU-Load Balancer, amelyet a kimenő forgalomhoz kell beállítani és használni. Előfordulhat azonban, hogy az alapértelmezett beállítás nem teljesíti az összes forgatókönyv követelményeit, ha a nyilvános IP-címek nem megengedettek, vagy további ugrásokra van szükség a kimenő forgalomhoz.

Ez a cikk bemutatja, hogyan szabhatja testre a fürt kimenő útvonalát az egyéni hálózati forgatókönyvek támogatásához, például a nyilvános IP-címek használatát, és a fürtnek a hálózati virtuális berendezés (NVA) mögött kell lennie.

## <a name="prerequisites"></a>Előfeltételek
* Az Azure CLI verziója 2.0.81 vagy újabb
* API-verziója `2020-01-01` vagy nagyobb


## <a name="limitations"></a>Korlátozások
* A OutboundType csak a fürt létrehozási idején definiálható, és ezt követően nem lehet frissíteni.
* A beállításhoz a `outboundType` és a rendszerhez tartozó AK-fürtök szükségesek `vm-set-type` `VirtualMachineScaleSets` `load-balancer-sku` `Standard` .
* A `outboundType` érték beállításához a `UDR` fürthöz érvényes kimenő kapcsolattal rendelkező felhasználó által megadott útvonal szükséges.
* Az `outboundType` értékre való beállítás azt jelenti, hogy a `UDR` bemenő forrás IP-címe, amely a terheléselosztó felé van átirányítva, előfordulhat, hogy **nem felel** meg a fürt kimenő kilépési céljának.

## <a name="overview-of-outbound-types-in-aks"></a>A kimenő típusok áttekintése az AK-ban

Az AK-fürtök testreszabhatók egyedi típusú `outboundType` Load Balancer vagy felhasználó által definiált útválasztás használatával.

> [!IMPORTANT]
> A kimenő típus csak a fürt kimenő forgalmára van hatással. További információ: a [beáramló vezérlők beállítása](ingress-basic.md).

> [!NOTE]
> Saját [útválasztási táblázatot][byo-route-table] is használhat UDR és kubenet hálózatkezeléssel. Győződjön meg arról, hogy a fürt identitása (egyszerű szolgáltatásnév vagy felügyelt identitás) közreműködői engedélyekkel rendelkezik az egyéni útválasztási táblázathoz.

### <a name="outbound-type-of-loadbalancer"></a>A terheléselosztó kimenő típusa

Ha `loadBalancer` be van állítva, az AK a következő konfigurációt automatikusan végrehajtja. A terheléselosztó egy AK-beli hozzárendelt nyilvános IP-címen keresztüli kimenő forgalomhoz használatos. Egy kimenő típus `loadBalancer` támogatja a típusú Kubernetes-szolgáltatásokat `loadBalancer` , ami várhatóan kilép az AK erőforrás-szolgáltató által létrehozott terheléselosztó alól.

A következő konfigurációt az AK hajtja végre.
   * Nyilvános IP-cím van kiépítve a fürt kimenő forgalmához.
   * A rendszer a terheléselosztó erőforráshoz rendeli a nyilvános IP-címet.
   * A terheléselosztó backend-készletei a fürtben lévő ügynökök csomópontjain vannak beállítva.

Az alábbiakban egy olyan hálózati topológia található, amely alapértelmezésben egy AK-alapú fürtbe van telepítve, amely a-t használja `outboundType` `loadBalancer` .

![outboundtype – LB](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>A userDefinedRouting kimenő típusa

> [!NOTE]
> A kimenő típus használata fejlett hálózati forgatókönyv, és megfelelő hálózati konfigurációt igényel.

Ha `userDefinedRouting` be van állítva, az AK nem konfigurálja automatikusan a kimenő útvonalakat. A kimenő telepítést Önnek kell elvégeznie.

Az AK-fürtöt egy korábban konfigurált alhálózattal rendelkező meglévő virtuális hálózatba kell telepíteni, mert a standard Load Balancer (SLB) architektúrájának használatakor explicit kimenő forgalmat kell létrehoznia. Ebben az architektúrában explicit módon kell elküldeni a kimenő forgalmat egy olyan készülékre, mint például a tűzfal, az átjáró, a proxy vagy a hálózati címfordítás (NAT) a standard Load Balancerhez vagy berendezéshez rendelt nyilvános IP-cím használatával.

Az AK erőforrás-szolgáltató telepíti a standard Load balancert (SLB). A terheléselosztó nincs konfigurálva semmilyen szabállyal, és [nem számítunk fel díjat a szabály elhelyezése előtt](https://azure.microsoft.com/pricing/details/load-balancer/). Az AK **nem** épít ki automatikusan nyilvános IP-címet a SLB-előtérbeli felülethez, és nem konfigurálja automatikusan a terheléselosztó-háttérrendszer-készletet.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Fürt üzembe helyezése kimenő UDR-típussal és Azure Firewall

Egy felhasználó által megadott útvonal használatával a kimenő típusú fürt alkalmazásának szemléltetéséhez egy fürt konfigurálható egy, a saját alhálózatán lévő Azure Firewall rendelkező virtuális hálózaton. Tekintse meg ezt a példát a [kimenő forgalom korlátozása az Azure Firewall-mel – példa](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> A UDR kimenő típusa megköveteli, hogy az útválasztási táblázatban a 0.0.0.0/0 és a következő ugrási cél NVA (hálózati virtuális berendezés) legyen.
> Az útválasztási táblázathoz már tartozik egy alapértelmezett 0.0.0.0/0 az internetre, anélkül, hogy a nyilvános IP-cím SNAT csak ezt az útvonalat adja hozzá, a kimenő forgalom nem fog megjelenni. Az AK ellenőrzi, hogy nem hoz létre 0.0.0.0/0 útvonalat az interneten, hanem NVA vagy átjáróként, stb.


## <a name="next-steps"></a>További lépések

Lásd: [Azure Networking UDR – áttekintés](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

Lásd: [útválasztási táblázat létrehozása, módosítása vagy törlése](https://docs.microsoft.com/azure/virtual-network/manage-route-table).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet