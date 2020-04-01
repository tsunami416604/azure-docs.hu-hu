---
title: Az Azure Event Hubs hálózati biztonsága
description: Ez a cikk a privát végpontok hozzáférésének konfigurálását ismerteti
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422769"
---
# <a name="network-security-for-azure-event-hubs"></a>Az Azure Event Hubs hálózati biztonsága 
Ez a cikk a következő biztonsági funkciók azure Event Hubs használatával ismertetjük: 

- Szolgáltatáscímkék
- IP tűzfal szabályai
- Hálózati szolgáltatás végpontjai
- Privát végpontok (előzetes verzió)


## <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatáscímke egy adott Azure-szolgáltatás IP-címelőtagjainak csoportját jelöli. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásaként, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. A szolgáltatáscímkékről a [Szolgáltatáscímkék – áttekintés című témakörben olvashat bővebben.](../virtual-network/service-tags-overview.md)

A szolgáltatáscímkék segítségével hálózati hozzáférés-vezérlést határozhat meg [a hálózati biztonsági csoportokon](../virtual-network/security-overview.md#security-rules) vagy az [Azure Firewall-en.](../firewall/service-tags.md) Biztonsági szabályok létrehozásakor a szolgáltatáscímkéket használjon adott IP-címek helyett. A szolgáltatáscímke nevének (például **az EventHub)** megadásával a szabály megfelelő *forrás-* vagy *célmezőjében* engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

| Szolgáltatáscímke | Cél | Használhatja a bejövő vagy kimenő? | Lehet regionális? | Használhatja az Azure Tűzfal? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure Event Hubs. | Kimenő | Igen | Igen |


## <a name="ip-firewall"></a>IP-tűzfal 
Alapértelmezés szerint az Event Hubs névterek elérhetők az internetről, amíg a kérelem érvényes hitelesítéssel és engedélyezéssel érkezik. Az IP-tűzfal lal tovább korlátozhatja a [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölésben lévő IPv4-címek vagy IPv4-címtartományok készletére.

Ez a funkció olyan esetekben hasznos, amelyekben az Azure Event Hubs csak bizonyos jól ismert helyekről érhető el. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekről származó forgalom fogadására. Ha például az Event Hubs szolgáltatást az [Azure Express Route-szal](/azure/expressroute/expressroute-faqs#supported-services)használja, létrehozhat egy **tűzfalszabályt,** amely csak a helyszíni infrastruktúra IP-címeiből engedélyezi a forgalmat. 

Az IP-tűzfal szabályok az Event Hubs névtér szintjén kerülnek alkalmazásra. Ezért a szabályok a támogatott protokollt használó ügyfelek összes kapcsolatára vonatkoznak. Az Eseményközpontok névterében engedélyezett IP-szabálynak nem megfelelő IP-címről érkező csatlakozási kísérlet jogosulatlanként elutasításra kerül. A válasz nem említi az IP-szabályt. Az IP-szűrőszabályok sorrendben kerülnek alkalmazásra, és az IP-címnek megfelelő első szabály határozza meg az elfogadási vagy elutasítási műveletet.

További információ: [Ip-tűzfal konfigurálása egy eseményközponthoz](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Hálózati szolgáltatás végpontjai
Az Event Hubs és a [Virtuális hálózat szolgáltatásvégpontok](../virtual-network/virtual-network-service-endpoints-overview.md) integrációja lehetővé teszi a virtuális hálózatokhoz kötött számítási feladatok, például a virtuális hálózatokhoz kötött virtuális gépek üzenetkezelési képességeinek biztonságos elérését, és a hálózati forgalom elérési útja mindkét végén biztonságos.

Miután úgy konfigurálták, hogy legalább egy virtuális hálózati alhálózati szolgáltatás végponthoz kötődjön, a megfelelő Event Hubs névtér már nem fogadja el a forgalmat bárhonnan, csak a virtuális hálózatok engedélyezett alhálózatai. A virtuális hálózati perspektíva, az Event Hubs névtér és a szolgáltatás végpontja konfigurálja egy elszigetelt hálózati alagút a virtuális hálózati alhálózat az üzenetküldő szolgáltatás. 

Az eredmény egy privát és elkülönített kapcsolat az alhálózathoz és a megfelelő Event Hubs névtérhez kötött munkaterhelések között, annak ellenére, hogy az üzenetküldő szolgáltatás végpontjának megfigyelhető hálózati címe nyilvános IP-tartományban van. Ez alól a viselkedés alól kivétel van. A szolgáltatásvégpont engedélyezése alapértelmezés szerint engedélyezi `denyall` a virtuális hálózathoz társított [IP-tűzfalszabályát.](event-hubs-ip-filtering.md) Adott IP-címeket adhat hozzá az IP-tűzfalhoz, hogy engedélyezze a hozzáférést az Event Hub nyilvános végpontjához. 

> [!IMPORTANT]
> A virtuális hálózatokat az Event Hubs **szabványos** és **dedikált** szintjei támogatják. Nem támogatott az **alapszinten.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>A virtuális hálózat integrációja által engedélyezett speciális biztonsági forgatókönyvek 

A szűk és széttagolt biztonságot igénylő megoldások, amelyeken a virtuális hálózati alhálózatok biztosítják a széttagolt szolgáltatások közötti szegmentálást, továbbra is szükség van az ezekben a rekeszekben található szolgáltatások közötti kommunikációs útvonalakra.

A rekeszek közötti bármely közvetlen IP-útvonal, beleértve a TCP/IP-n keresztül https-t hordozókat is, magában hordozza a biztonsági rések kihasználásának kockázatát a hálózati rétegből felfelé. Az üzenetküldő szolgáltatások szigetelt kommunikációs útvonalakat biztosítanak, ahol az üzenetek et még a felek közötti átmenet során is lemezre írják. Két különböző virtuális hálózat munkaterhelései, amelyek ugyanahhoz az Event Hubs-példányhoz vannak kötve, hatékonyan és megbízhatóan kommunikálhatnak az üzeneteken keresztül, miközben a megfelelő hálózati elkülönítési határ integritása megmarad.
 
Ez azt jelenti, hogy a biztonsági szempontból érzékeny felhőalapú megoldásai nem csak az Azure iparágvezető megbízható és skálázható aszinkron üzenetkezelési képességeihez férnek hozzá, hanem mostantól az üzenetküldés segítségével kommunikációs útvonalakat hozhatnak létre a biztonságos megoldási rekeszek között, amelyek természetüknél fogva biztonságosabbak, mint bármely egyenrangú kommunikációs mód, beleértve a HTTPS-t és más TLS-védelemmel rendelkező szoftvercsatorna-protokollokat.

### <a name="bind-event-hubs-to-virtual-networks"></a>Eseményközpontok kötése virtuális hálózatokhoz

**A virtuális hálózati szabályok** a tűzfal biztonsági szolgáltatás, amely szabályozza, hogy az Azure Event Hubs névtér fogadja-e a kapcsolatokat egy adott virtuális hálózati alhálózat.

Az Event Hubs névtér virtuális hálózathoz kötése két lépésből áll. Először létre kell hoznia egy **virtuális hálózati szolgáltatás végpontját** a virtuális hálózat alhálózatán, és engedélyeznie kell azt a **Microsoft.EventHub** számára a [szolgáltatásvégpont áttekintése](../virtual-network/virtual-network-service-endpoints-overview.md) című cikkben leírtak szerint. Miután hozzáadta a szolgáltatásvégpontot, az Event Hubs névteret **egy virtuális hálózati szabállyal**köti hozzá.

A virtuális hálózati szabály az Event Hubs névtér és a virtuális hálózati alhálózat társítása. Amíg a szabály létezik, az alhálózathoz kötött összes számítási feladat hozzáférést kap az Event Hubs névtérhez. Az Event Hubs maga soha nem hoz létre kimenő kapcsolatokat, nem kell hozzáférnie, és ezért soha nem kap hozzáférést az alhálózathoz a szabály engedélyezésével.

További információ: [Virtuális hálózati szolgáltatás végpontjainak konfigurálása egy eseményközponthoz](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Privát végpontok

[Az Azure Private Link szolgáltatás](../private-link/private-link-overview.md) lehetővé teszi az Azure-szolgáltatások (például az Azure Event Hubs, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését a virtuális hálózat egy privát **végpontján** keresztül.

A privát végpont egy hálózati adapter, amely privát és biztonságos kapcsolatot biztosít az Azure Private Link által működtetett szolgáltatással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatáshoz irányuló összes forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforrás egy példányához, így a legmagasabb szintű részletességet biztosíthatja a hozzáférés-vezérlésben.

> [!NOTE]
> Ez a funkció csak a **dedikált** szint támogatott. A dedikált szinttel kapcsolatos további információkért [lásd: Dedikált eseményközpontok áttekintése.](event-hubs-dedicated-overview.md) 
>
> Ez a funkció jelenleg **előzetes verzióban érhető el.** 


További információ: [Privát végpontok konfigurálása egy eseményközponthoz](private-link-service.md)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Az IP-tűzfal beállítása eseményközponthoz](event-hubs-ip-filtering.md)
- [A virtuális hálózati szolgáltatás végpontjainak konfigurálása egy eseményközponthoz](event-hubs-service-endpoints.md)
- [Saját végpontok konfigurálása egy eseményközponthoz](private-link-service.md)