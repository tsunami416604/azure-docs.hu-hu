---
title: Prémium és alapszintű Azure Event Grid
description: Ez a cikk a prémium és az alapszintű csomagok közötti különbséget, valamint az egyes funkciók használatát ismerteti Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79300717"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Prémium és alapszintű Azure Event Grid
Azure Event Grid két szinttel rendelkezik: **prémium** és **alapszintű**. Az alapszintű csomag a használatot vagy az utólagos elszámolású díjszabást használja. Minden olyan alapszintű pub/sub eszközt biztosít, amelyet az eseményvezérelt programozási modellekhez szükséges Event Grid használatához. A prémium szint ezt a lépést a vállalatot célzó biztonsági funkciókkal bővíti. A prémium szint jelenleg **előzetes** verzióban érhető el, és számos funkciója még fejlesztés alatt áll.

## <a name="overview"></a>Áttekintés
Event Grid összes egyéni témaköre vagy tartománya alapszintű vagy prémium szintű csomaghoz tartozik. Az `2020-04-01-preview` API-verziótól kezdődően kiválaszthatja a kívánt szintet egy témakör vagy tartomány létrehozása részeként. Az alapértelmezett érték az alapszintű csomag. A régebbi API-verziók használatával létrehozott témakörök és tartományok az alapszintű csomaghoz képest alapértelmezettek. A témakörök és tartományok díjszabási szintjeinek módosításához lásd: a [szintek frissítése témakörökhöz és tartományokhoz](update-tier.md).

## <a name="capabilities-and-features"></a>Képességek és szolgáltatások

Az alábbi táblázat a rétegek közötti különbségeket ismerteti:

|       &nbsp;                                           | Basic           | Prémium        |
| ------------------------------------------------------ | --------------- | -------------- |
| IP-tűzfalszabályok bejövő forgalomra vonatkozó szabályai                          | Előzetes verzió  | Előzetes verzió |
| Szolgáltatás-címkék a kimenő forgalomhoz                                | Előzetes verzió  | Előzetes verzió |
| Magánhálózati végpontok VNet integrációja a bejövő forgalomban          | Nem érhető el   | Előzetes verzió |

## <a name="availability"></a>Rendelkezésre állás
A kezdeti előzetes verzióban a prémium szintű témakörök és a privát végpont-integrációs tartományok a következő régiókban érhetők el:

- USA keleti régiója
- USA nyugati régiója, 2.
- USA déli középső régiója

## <a name="pricing-and-quotas"></a>Díjszabás és kvóták
Tekintse meg az alapszintű csomag díjszabási részleteit [Event Grid díjszabását](https://azure.microsoft.com/pricing/details/event-grid/) . A prémium szint díjszabása még nem jelent meg, és ingyenes, amíg a díjszabás elérhetővé válik.

A témakör és a tartomány meglévő kvótái, valamint az átviteli sebesség a prémium és az alapszintű csomagra is érvényes, amíg be nem jelent a prémium szint díjszabása.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- Az alapszintű és a prémium szintű csomagra való frissítéshez tekintse meg a [frissítés díjszabási szintjét](update-tier.md) ismertető cikket. 
- A Event Grid erőforrás IP-tűzfalát úgy is beállíthatja, hogy a nyilvános interneten keresztül csak az IP-címek és az IP-címtartományok egyetlen kiválasztott készletével korlátozza a hozzáférést. Részletes útmutatásért lásd: a [tűzfal konfigurálása](configure-firewall.md).
- A privát végpontokat úgy is beállíthatja, hogy csak a kiválasztott virtuális hálózatokról korlátozza a hozzáférést. Részletes útmutatásért lásd: [privát végpontok konfigurálása](configure-private-endpoints.md).