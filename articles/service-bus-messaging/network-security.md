---
title: Az Azure Service Bus hálózati biztonsága
description: Ez a cikk a hálózati biztonsági szolgáltatásokat ismerteti, például a szolgáltatáscímkéket, az IP-tűzfalszabályokat, a szolgáltatásvégpontokat és a privát végpontokat.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479279"
---
# <a name="network-security-for-azure-service-bus"></a>Az Azure Service Bus hálózati biztonsága 
Ez a cikk a következő biztonsági funkciók azure Service Bus használatával ismertetjük: 

- Szolgáltatáscímkék
- IP tűzfal szabályai
- Hálózati szolgáltatás végpontjai
- Privát végpontok (előzetes verzió)


## <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatáscímke egy adott Azure-szolgáltatás IP-címelőtagjainak csoportját jelöli. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásaként, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. A szolgáltatáscímkékről a [Szolgáltatáscímkék – áttekintés című témakörben olvashat bővebben.](../virtual-network/service-tags-overview.md)

A szolgáltatáscímkék segítségével hálózati hozzáférés-vezérlést határozhat meg [a hálózati biztonsági csoportokon](../virtual-network/security-overview.md#security-rules) vagy az [Azure Firewall-en.](../firewall/service-tags.md) Biztonsági szabályok létrehozásakor a szolgáltatáscímkéket használjon adott IP-címek helyett. A szolgáltatáscímke nevének (például **ServiceBus)** megadásával a szabály megfelelő *forrás-* vagy *célmezőjében* engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

| Szolgáltatáscímke | Cél | Használhatja a bejövő vagy kimenő? | Lehet regionális? | Használhatja az Azure Tűzfal? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | A prémium szolgáltatási szintet használó Azure Service Bus-forgalom. | Kimenő | Igen | Igen |


## <a name="ip-firewall"></a>IP-tűzfal 
Alapértelmezés szerint a Service Bus névterek érhetők el az internetről, amíg a kérelem érvényes hitelesítéssel és engedélyezéssel érkezik. Az IP-tűzfal lal tovább korlátozhatja a [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölésben lévő IPv4-címek vagy IPv4-címtartományok készletére.

Ez a funkció olyan esetekben hasznos, amelyekben az Azure Service Bus csak bizonyos jól ismert helyekről érhető el. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekről származó forgalom fogadására. Ha például a Service Bus szolgáltatást [Azure Express Route][express-route] használatával használja, létrehozhat egy **tűzfalszabályt,** amely csak a helyszíni infrastruktúra IP-címeiből vagy egy vállalati NAT-átjáró címéről engedélyezi a forgalmat. 

Az IP-tűzfal szabályok a Service Bus névtér szintjén kerülnek alkalmazásra. Ezért a szabályok a támogatott protokollt használó ügyfelek összes kapcsolatára vonatkoznak. A Service Bus névterén engedélyezett IP-szabálynak nem megfelelő IP-címről érkező csatlakozási kísérlet nem engedélyezett. A válasz nem említi az IP-szabályt. Az IP-szűrőszabályok sorrendben kerülnek alkalmazásra, és az IP-címnek megfelelő első szabály határozza meg az elfogadási vagy elutasítási műveletet.

További információ: [Ip-tűzfal konfigurálása Service Bus-névtérhez](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Hálózati szolgáltatás végpontjai
A Service Bus [virtuális hálózati (VNet) szolgáltatásvégpontokkal](service-bus-service-endpoints.md) való integrációja lehetővé teszi a virtuális hálózatokhoz kötött számítási feladatokból, például a virtuális hálózatokhoz kötött virtuális gépekről származó üzenetküldési képességek biztonságos elérését, és a hálózati forgalom elérési útja mindkét végén biztonságos.

Ha úgy van beállítva, hogy legalább egy virtuális hálózati alhálózati szolgáltatás végponthoz legyen kötve, a megfelelő Service Bus-névtér már nem fogadja el a forgalmat bárhonnan, csak az engedélyezett virtuális hálózat(ok)hoz. A virtuális hálózati perspektíva, a Service Bus névtér és a szolgáltatás végpontja konfigurálja egy elszigetelt hálózati alagút a virtuális hálózati alhálózat az üzenetküldő szolgáltatás.

Az eredmény egy privát és elkülönített kapcsolat az alhálózathoz és a megfelelő Service Bus-névtérhez kötött munkaterhelések között, annak ellenére, hogy az üzenetküldési szolgáltatás végpontjának megfigyelhető hálózati címe nyilvános IP-tartományban van.

> [!IMPORTANT]
> A virtuális hálózatok csak [a Prémium szintű](service-bus-premium-messaging.md) Service Bus-névterekben támogatottak.
> 
> VNet szolgáltatásvégpontok használatakor a Service Bus, nem szabad engedélyezni ezeket a végpontokat az alkalmazásokban, amelyek keverik a standard és a prémium szintű Service Bus névterek. Mivel a standard szint nem támogatja a virtuális hálózatokat. A végpont csak a prémium szintű névterekre korlátozódik.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A virtuális hálózat integrációja által engedélyezett speciális biztonsági forgatókönyvek 

Olyan megoldások, amelyek szigorú és széttagolt biztonságot igényelnek, és ahol a virtuális hálózati alhálózatok biztosítják a széttagolt szolgáltatások közötti szegmentálást, általában továbbra is szükség van az ezekben a rekeszekben található szolgáltatások közötti kommunikációs útvonalakra.

A rekeszek közötti bármely közvetlen IP-útvonal, beleértve a TCP/IP-n keresztül https-t hordozókat is, magában hordozza a biztonsági rések kihasználásának kockázatát a hálózati rétegből felfelé. Az üzenetküldő szolgáltatások teljesen szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek et még a lemezekre is írják, amint a felek között áttérnek. Két különböző virtuális hálózat, amelyek ugyanahhoz a Service Bus-példányhoz vannak kötve, hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a megfelelő hálózati elkülönítési határ integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból érzékeny felhőalapú megoldásai nem csak az Azure iparágvezető megbízható és skálázható aszinkron üzenetkezelési képességeihez férnek hozzá, hanem mostantól az üzenetküldés segítségével kommunikációs útvonalakat hozhatnak létre a biztonságos megoldási rekeszek között, amelyek eredendően biztonságosabbak, mint bármely peer-to-peer kommunikációs mód, beleértve a HTTPS-t és más TLS-vel védett szoftvercsatorna protokollokat.

### <a name="bind-service-bus-to-virtual-networks"></a>Service Bus kötése virtuális hálózatokhoz

*A virtuális hálózati szabályok* a tűzfal biztonsági szolgáltatása, amely azt szabályozza, hogy az Azure Service Bus-kiszolgáló fogadja-e a kapcsolatot egy adott virtuális hálózati alhálózatból.

A Service Bus-névtér virtuális hálózathoz kötése kétlépésből áll. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** egy virtuális hálózat alhálózaton, és engedélyeznie kell azt a **Microsoft.ServiceBus** számára a [szolgáltatás végpontjának áttekintése szerint.](service-bus-service-endpoints.md) Miután hozzáadta a szolgáltatásvégpontot, a Service Bus névterét **egy virtuális hálózati szabállyal**köti hozzá.

A virtuális hálózati szabály a Service Bus névtér és a virtuális hálózati alhálózat társítása. Amíg a szabály létezik, az alhálózathoz kötött összes számítási feladat hozzáférést kap a Service Bus névtérhez. A Service Bus maga soha nem hoz létre kimenő kapcsolatokat, nem kell hozzáférnie, és ezért soha nem kap hozzáférést az alhálózathoz, ha engedélyezi ezt a szabályt.

További információ: [Virtuális hálózati szolgáltatásvégpontok konfigurálása service bus-névtérhez](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Privát végpontok

Az Azure Private Link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Service Bus, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését a virtuális hálózat **egy privát végpontján** keresztül.

A privát végpont egy hálózati adapter, amely privát és biztonságos kapcsolatot biztosít az Azure Private Link által működtetett szolgáltatással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatáshoz irányuló összes forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforrás egy példányához, így a legmagasabb szintű részletességet biztosíthatja a hozzáférés-vezérlésben.

További információ: [Mi az Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Ezt a funkciót az Azure Service Bus **prémium** szintje támogatja. A prémium szintről további információt a Service Bus Premium és a [Standard üzenetkezelési csomagok](service-bus-premium-messaging.md) cikkben talál.
>
> Ez a funkció jelenleg **előzetes verzióban érhető el.** 


További információ: [Privát végpontok konfigurálása service bus-névtérhez](private-link-service.md)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Az IP-tűzfal konfigurálása service bus-névtérhez](service-bus-ip-filtering.md)
- [Virtuális hálózati szolgáltatás végpontjainak konfigurálása service bus-névtérhez](service-bus-service-endpoints.md)
- [Privát végpontok konfigurálása service bus-névtérhez](private-link-service.md)
