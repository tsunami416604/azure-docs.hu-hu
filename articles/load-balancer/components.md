---
title: Összetevők Azure Load Balancer
description: Azure Load Balancer-összetevők áttekintése
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 4a84c43b57ec4f632a2bfabb10d112e4975249bf
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733107"
---
# <a name="azure-load-balancer-components"></a>Összetevők Azure Load Balancer

Azure Load Balancer számos kulcsfontosságú összetevőt tartalmaz a művelethez. Ezek az összetevők az előfizetésben Azure Portal, Azure CLI, Azure PowerShell vagy sablonok használatával konfigurálhatók.

## <a name="frontend-ip-configurations"></a>Előtér-IP-konfigurációk

A terheléselosztó IP-címe. Ez a kapcsolódási pont az ügyfelek számára. Ezek a címek a következők lehetnek:

- **Nyilvános IP-cím**
- **Magánhálózati IP-cím**

Az IP-cím kiválasztása meghatározza a létrehozott terheléselosztó **típusát** . A magánhálózati IP-címek kiválasztása létrehoz egy belső terheléselosztó. A nyilvános IP-cím kiválasztása nyilvános Load balancert hoz létre.

|  | Nyilvános terheléselosztó  | Belső terheléselosztó |
| ---------- | ---------- | ---------- |
| Előtér-IP-konfiguráció| Nyilvános IP-cím | Magánhálózati IP-cím|
| Leírás | A nyilvános terheléselosztó a nyilvános IP-címet és a bejövő forgalom portját a virtuális gép magánhálózati IP-címére és portjára képezi le. A Load Balancer a virtuális gépről érkező válasz felé irányuló forgalmat is leképezi. A terheléselosztási szabályok alkalmazásával a különböző típusú adatforgalmat több virtuális gépre vagy szolgáltatásra is terjesztheti. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.| A belső terheléselosztó a virtuális hálózaton belüli erőforrásokra osztja el a forgalmat. Az Azure korlátozza a hozzáférést egy elosztott virtuális hálózat előtér-IP-címeihez. Az előtér-IP-címeket és a virtuális hálózatokat a rendszer soha nem teszi elérhetővé közvetlenül az internetes végpontok számára. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el. |
| Támogatott SKU-i | Alapszintű, standard | Alapszintű, standard |

![Rétegű terheléselosztó – példa](./media/load-balancer-overview/load-balancer.png)

## <a name="backend-pool"></a>A háttérkészlet

Egy virtuálisgép-méretezési csoportban lévő virtuális gépek vagy példányok csoportja, amely a beérkező kérést szolgálja. Ahhoz, hogy a költséghatékonyan megfeleljen a nagy mennyiségű bejövő forgalomnak, a számítástechnikai irányelvek általában azt ajánlják, hogy további példányokat vegyen fel a háttér-készletbe. 

A Load Balancer azonnal újrakonfigurálja magát az automatikus újrakonfigurálással, ha feljebb vagy lejjebb méretezi a példányokat. Ha virtuális gépeket ad hozzá vagy távolít el a háttér-készletből, a terheléselosztó további műveletek nélkül újrakonfigurálja a terheléselosztó-t. A háttér-készlet hatóköre a virtuális hálózatban található bármely virtuális gép. 

A háttérbeli készlet kialakításának megtervezése során tervezze meg a minimális számú egyéni háttér-készlet erőforrást a felügyeleti műveletek hosszának optimalizálása érdekében. Nincs különbség az adatsíkok teljesítményében vagy méretezésében.

## <a name="health-probes"></a>Állapotminták

A backend-készletben lévő példányok állapotának meghatározásához a rendszer állapot-mintavételt használ. Megadhatja az állapot-mintavételek nem megfelelő állapotát. Ha egy mintavétel nem válaszol, a terheléselosztó leállítja az új kapcsolatok küldését a nem kifogástalan állapotú példányokra. A mintavételi hiba nem érinti A meglévő kapcsolatokat. A kapcsolatok addig folytatódnak, amíg az alkalmazás:

- A folyamat vége
- Tétlen időtúllépés történik
- A virtuális gép leáll

Load Balancer különböző állapot-mintavételi típusokat biztosít a végpontokhoz:

- TCP
- HTTP
- HTTPS

Az alapszintű Load Balancer nem támogatja a HTTPS-próbákat. Az alapszintű Load Balancer minden TCP-kapcsolatot bezár (beleértve a létesített kapcsolatokat is).

## <a name="load-balancing-rules"></a>Terheléselosztási szabályok

A terheléselosztási szabályok közlik a terheléselosztó-vel, hogy mi a teendő. A terheléselosztási szabály egy adott előtérbeli IP-konfigurációt és portot képez le több háttérbeli IP-cím és port számára.

## <a name="inbound-nat-rules"></a>Bejövő NAT-szabályok

A bejövő NAT-szabályok továbbítják a forgalmat az előtér-IP-címről a virtuális hálózaton belüli háttérbeli példányra. A portok továbbítása a terheléselosztással megegyező kivonatoló alapú eloszlással történik. 

Példa: RDP protokoll (RDP) vagy Secure Shell (SSH) munkamenetek a virtuális hálózaton belüli virtuálisgép-példányok elkülönítésére. Több belső végpont is rendelhető ugyanahhoz az előtér-IP-címhez tartozó portokhoz. Az előtérbeli IP-címek használatával távolról felügyelheti a virtuális gépeket egy további Jump Box nélkül.

## <a name="outbound-rules"></a>Kimenő szabályok

Egy kimenő szabály konfigurálja a kimenő hálózati címfordítást (NAT) a háttér-készlet által azonosított összes virtuális géphez vagy példányhoz.

Az alapszintű Load Balancer nem támogatja a kimenő szabályokat.
![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)

## <a name="next-steps"></a>További lépések

- A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozását](quickstart-load-balancer-standard-public-portal.md) ismertető témakört.
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók a [nyilvános IP-címekről](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- További információ a [magánhálózati IP-címekről](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- További információ [a TCP alaphelyzetbe állításáról üresjáratban](load-balancer-tcp-reset.md).
- További tudnivalók a HA-portok terheléselosztási [szabályaival standard Load Balancer](load-balancer-ha-ports-overview.md).
- Ismerje meg, hogyan használhatja [a Load Balancert több előtér-felülettel](load-balancer-multivip-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
- További tudnivalók a mintavételi [típusokról](load-balancer-custom-probe-overview.md#types).
- További információ a [Load Balancer korlátairól](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Tudnivalók a [portok továbbításának](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)használatáról.
- További információ a terheléselosztó [kimenő szabályairól](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview).
