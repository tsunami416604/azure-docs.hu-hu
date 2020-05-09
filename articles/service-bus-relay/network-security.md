---
title: Azure Relay hálózati biztonsága
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hozzáférés a privát végpontokról
services: service-bus-relay
author: spelluru
ms.service: service-bus-relay
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: spelluru
ms.openlocfilehash: d0a0d03680877ae78f5af939f8d47e4e426abadb
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984771"
---
# <a name="network-security-for-azure-relay"></a>Azure Relay hálózati biztonsága 
Ez a cikk azt ismerteti, hogyan használhatók a következő biztonsági szolgáltatások a Azure Relay használatával: 

- IP-tűzfalszabályok (előzetes verzió)
- Privát végpontok (előzetes verzió)

> [!NOTE]
> A Azure Relay nem támogatja a hálózati szolgáltatás végpontját. 


## <a name="ip-firewall"></a>IP-tűzfal 
Alapértelmezés szerint a továbbítási névterek elérhetők az internetről, feltéve, hogy a kérelem érvényes hitelesítést és engedélyezést tartalmaz. Az IP-tűzfallal továbbra is korlátozhatja, hogy csak IPv4-címek vagy IPv4-címtartományok legyenek a [CIDR (osztály nélküli tartományok közötti útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelöléssel.

Ez a funkció olyan helyzetekben hasznos, amikor a Azure Relay csak bizonyos jól ismert helyekről lehet elérhető. A tűzfalszabályok lehetővé teszik a szabályok konfigurálását az adott IPv4-címekből származó forgalom fogadásához. Ha például az [Azure Express Route](/azure/expressroute/expressroute-faqs#supported-services)használatával használ továbbítót, létrehozhat egy **tűzfalszabályet** , amely lehetővé teszi, hogy csak a helyszíni infrastruktúra IP-címeiről érkező forgalmat engedélyezze. 

Az IP-tűzfalszabályok a továbbítási névtér szintjén lesznek alkalmazva. Ezért a szabályok az ügyfelek összes kapcsolatára érvényesek bármely támogatott protokoll használatával. A továbbítási névtérben lévő engedélyezett IP-szabálynak nem megfelelő IP-címről érkező kapcsolódási kísérleteket a rendszer nem engedélyezettként fogadja el. A válasz nem említi az IP-szabályt. Az IP-szűrési szabályok sorrendben lesznek alkalmazva, és az IP-címnek megfelelő első szabály határozza meg az elfogadás vagy az elutasítás műveletet.

További információ: az [IP-tűzfal konfigurálása továbbító névtérhez](ip-firewall-virtual-networks.md)

## <a name="private-endpoints"></a>Privát végpontok

Az Azure **Private link Service** lehetővé teszi az Azure-szolgáltatások (például a Azure Relay, a Azure Service Bus, az Azure Event Hubs, az Azure Storage és a Azure Cosmos db) és az Azure által üzemeltetett ügyfél-partner szolgáltatások elérését a virtuális hálózat privát végpontján keresztül. További információ: [Mi az az Azure Private link (előzetes verzió)?](../private-link/private-link-overview.md)

A **privát végpont** egy olyan hálózati adapter, amely lehetővé teszi, hogy a virtuális hálózaton futó munkaterhelések privát és biztonságos módon kapcsolódjanak egy olyan szolgáltatáshoz, amelynek van **privát kapcsolati erőforrása** (például egy továbbító névtér). A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute, VPN-kapcsolatra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom áthalad a Microsoft gerinc hálózatán, és így megszűnik a nyilvános internetről való kitettség. Megadhatja a hozzáférés-vezérlés részletességi szintjét azáltal, hogy engedélyezi a kapcsolódást az adott Azure Relay névterekhez.

> [!NOTE]
> Ez a funkció jelenleg **előzetes**verzióban érhető el. 

További információ: [privát végpontok konfigurálása](private-link-service.md)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Az IP-tűzfal konfigurálása](ip-firewall-virtual-networks.md)
- [Privát végpontok konfigurálása](private-link-service.md)