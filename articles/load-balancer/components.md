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
ms.date: 06/04/2020
ms.author: allensu
ms.openlocfilehash: b696cdf2d54c42d3967041c5d10b1bd9bb5a3065
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84448682"
---
# <a name="azure-load-balancer-components"></a>Összetevők Azure Load Balancer

A Azure Load Balancer néhány kulcsfontosságú összetevőből áll. Ezek az előfizetésben Azure Portal, Azure CLI, Azure PowerShell vagy sablonok használatával konfigurálhatók.

## <a name="frontend-ip-configuration"></a>Előtér-IP-konfiguráció<a name = "frontend-ip-configurations"></a>

A Azure Load Balancer IP-címe. Ez a kapcsolódási pont az ügyfelek számára. Ezek az IP-címek a következők lehetnek:

- **Nyilvános IP-cím**
- **Magánhálózati IP-cím**

Az IP-cím természete határozza meg, hogy milyen **típusú** terheléselosztó lett létrehozva. A magánhálózati IP-címek kiválasztása létrehoz egy belső terheléselosztó. A nyilvános IP-cím kiválasztása nyilvános Load balancert hoz létre.

|  | Nyilvános terheléselosztó  | Belső terheléselosztó |
| ---------- | ---------- | ---------- |
| Előtér-IP-konfiguráció| Nyilvános IP-cím | Magánhálózati IP-cím|
| Description | A nyilvános terheléselosztó a nyilvános IP-címet és a bejövő forgalom portját a virtuális gép magánhálózati IP-címére és portjára képezi le. A Load Balancer a virtuális gépről érkező válasz felé irányuló forgalmat is leképezi. A terheléselosztási szabályok alkalmazásával a különböző típusú adatforgalmat több virtuális gépre vagy szolgáltatásra is terjesztheti. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.| A belső terheléselosztó a virtuális hálózaton belüli erőforrásokra osztja el a forgalmat. Az Azure korlátozza a hozzáférést egy elosztott virtuális hálózat előtér-IP-címeihez. Az előtér-IP-címeket és a virtuális hálózatokat a rendszer soha nem teszi elérhetővé közvetlenül az internetes végpontok számára. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el. |
| Támogatott SKU-i | Alapszintű, standard | Alapszintű, standard |

![Rétegű terheléselosztó – példa](./media/load-balancer-overview/load-balancer.png)

Load Balancer több előtér-IP-címmel rendelkezhet. További információ a [több előtérbeli felületről](load-balancer-multivip-overview.md).

## <a name="backend-pool"></a>A háttérkészlet

Egy virtuálisgép-méretezési csoportban lévő virtuális gépek vagy példányok csoportja, amely a beérkező kérést szolgálja. Ahhoz, hogy a költséghatékonyan megfeleljen a nagy mennyiségű bejövő forgalomnak, a számítástechnikai irányelvek általában azt ajánlják, hogy további példányokat vegyen fel a háttér-készletbe.

A Load Balancer azonnal újrakonfigurálja magát az automatikus újrakonfigurálással, ha feljebb vagy lejjebb méretezi a példányokat. Ha virtuális gépeket ad hozzá vagy távolít el a háttér-készletből, a terheléselosztó további műveletek nélkül újrakonfigurálja a terheléselosztó-t. A háttér-készlet hatóköre a virtuális hálózatban található bármely virtuális gép.

A háttérbeli készlet kialakításának megtervezése során tervezze meg a minimális számú egyéni háttér-készlet erőforrást a felügyeleti műveletek hosszának optimalizálása érdekében. Nincs különbség az adatsíkok teljesítményében vagy méretezésében.

## <a name="health-probes"></a>Állapotminták

Az állapot-mintavétel a háttér-készletben lévő példányok állapotának meghatározására szolgál. Load Balancer létrehozásakor be kell állítania egy állapot-mintavételt, amelyet a Load Balancer használhat annak megállapítására, hogy a példány állapota Kifogástalan-e, és irányítsa-e a forgalmat.

Megadhatja az állapot-mintavételek nem megfelelő állapotát. Ha egy mintavétel nem válaszol, Load Balancer leállítja az új kapcsolatok küldését a nem kifogástalan állapotú példányokra. A mintavételi hiba nem érinti A meglévő kapcsolatokat. A kapcsolatok addig folytatódnak, amíg az alkalmazás:

- A folyamat vége
- Tétlen időtúllépés történik
- A virtuális gép leáll

A Load Balancer különböző állapot-mintavételi típusokat biztosít a végpontokhoz: TCP, HTTP és HTTPS. [További információ a Load Balancer Health](load-balancer-custom-probe-overview.md)-mintavételről.

Az alapszintű Load Balancer nem támogatja a HTTPS-próbákat. Az alapszintű Load Balancer lezárja az összes TCP-kapcsolatot (beleértve a létesített kapcsolatokat is).

## <a name="load-balancing-rules"></a>Terheléselosztási szabályok

Load Balancer szabály segítségével határozható meg, hogy a rendszer hogyan ossza el a bejövő forgalmat a háttér-készleten belüli **összes** példányra. A terheléselosztási szabály egy adott előtérbeli IP-konfigurációt és portot képez le több háttérbeli IP-cím és port számára.

Ha például azt szeretné, hogy az 80-as porton (vagy egy másik porton) lévő forgalom az összes háttér-példány 80-es portjához legyen átirányítva, akkor ezt a terheléselosztási szabályt kell használnia.

### <a name="high-availability-ports"></a>Magas rendelkezésre állású portok

Egy Load Balancer-szabály, amely a "Protocol-all és a port-0" protokollal van konfigurálva. Ez lehetővé teszi egyetlen szabály betöltését a belső standard Load Balancer összes portjára érkező TCP-és UDP-folyamatok terheléselosztásához. A terheléselosztási döntés egy folyamaton alapul. Ez a művelet a következő öt rekordos kapcsolaton alapul: 
1. 
    forrás IP-címe
  
2. forrásport
3. cél IP-címe
4. célport
5. protokoll

A HA-portok terheléselosztási szabályai segítenek a kritikus forgatókönyvek, például a magas rendelkezésre állás és a virtuális hálózatokon belüli hálózati virtuális berendezések (NVA-EK) méretezése terén. A funkció akkor is segíthet, ha nagy számú portot kell terheléselosztással elválasztani.

További információ: [Ha portok](load-balancer-ha-ports-overview.md).

## <a name="inbound-nat-rules"></a>Bejövő NAT-szabályok

A bejövő NAT-szabály továbbítja a bejövő forgalmat a kiválasztott előtéri IP-cím és port kombinációra egy **adott** virtuális gépre vagy példányra a háttér-készletben. A portok továbbítása a terheléselosztással megegyező kivonatoló alapú eloszlással történik.

Ha például az RDP protokoll (RDP) vagy a Secure Shell (SSH) munkameneteket szeretné elkülöníteni a háttérbeli készletben lévő virtuálisgép-példányokat. Több belső végpont is rendelhető ugyanahhoz a előtér-IP-címhez tartozó portokhoz. Az előtérbeli IP-címek használatával távolról felügyelheti a virtuális gépeket egy további Jump Box nélkül.

A bejövő NAT-szabályok Virtual Machine Scale Sets (VMSS) kontextusában a bejövő NAT-készletek. További információ a [Load Balancer összetevőiről és VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#azure-virtual-machine-scale-sets-with-azure-load-balancer).

## <a name="outbound-rules"></a>Kimenő szabályok

Egy kimenő szabály konfigurálja a kimenő hálózati címfordítást (NAT) a háttér-készlet által azonosított összes virtuális géphez vagy példányhoz. Ez lehetővé teszi, hogy a háttérbeli példányok kommunikáljanak (kimenő) az interneten vagy más végpontokon.

További információ a [kimenő kapcsolatokról és a szabályokról](load-balancer-outbound-connections.md).

Az alapszintű Load Balancer nem támogatja a kimenő szabályokat.

## <a name="next-steps"></a>Következő lépések

- A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozását](quickstart-load-balancer-standard-public-portal.md) ismertető témakört.
- További információ a [Azure Load Balancerról](load-balancer-overview.md).
- Tudnivalók a [nyilvános IP-címekről](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
- További információ a [magánhálózati IP-címekről](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Ismerje meg a [standard Load Balancer diagnosztikát](load-balancer-standard-diagnostics.md).
- További információ [a TCP alaphelyzetbe állításáról üresjáratban](load-balancer-tcp-reset.md).
- További tudnivalók a HA-portok terheléselosztási [szabályaival standard Load Balancer](load-balancer-ha-ports-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
- További információ a [Load Balancer korlátairól](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer).
- Tudnivalók a [portok továbbításának](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)használatáról.
