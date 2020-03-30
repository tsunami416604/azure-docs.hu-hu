---
title: Azure Event Grid Premium és alapszintű csomagok
description: Ez a cikk az Azure Event Grid Premium és az alapszintű szintek közötti különbséget ismerteti, valamint azt, hogy mikor kell használni
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300717"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Az Azure Event Grid prémium és alapszintű szintjei
Az Azure Event Grid két rétegből rendelkezik: **prémium** és **alapszintű.** Az alapszintű szint a felhasználást vagy a használatalapú díjszabást használja. Ez ad ön minden a alap pub/ sub szerszámok ön szükség -hoz használ Esemény Rács részére esemény- vezetett műsor-összeállítás minták. A prémium szint ezt egy lépéssel tovább viszi a vállalatot célzó biztonsági funkciókkal. A prémium szint korai **előzetes verzióban** érhető el, és számos funkciója még fejlesztés alatt áll.

## <a name="overview"></a>Áttekintés
Az Event Grid összes egyéni témaköre és tartománya alapszintű vagy prémium szintű réteghez tartozik. Az `2020-04-01-preview` API-verzióval kezdve kiválaszthatja a kívánt réteget egy témakör vagy tartomány létrehozásának részeként. Az alapértelmezett érték az alapszint. A régebbi API-verziókkal létrehozott témakörök és tartományok alapértelmezés szerint az alapszintű. A témakörök és tartományok tarifacsomagjának módosításáról a [Témakörök és tartományok szintjének frissítése](update-tier.md)című témakörben olvashat.

## <a name="capabilities-and-features"></a>Képességek és funkciók

Az alábbi táblázat a szintek közötti különbségeket ismerteti:

|       &nbsp;                                           | Basic           | Prémium        |
| ------------------------------------------------------ | --------------- | -------------- |
| A be- és biztonságba való be- és visszaszolgáltatás IP-tűzfalszabályai                          | Előzetes verzió  | Előzetes verzió |
| A kimenő forgalom szolgáltatáscímkéi                                | Előzetes verzió  | Előzetes verzió |
| Privát végpont virtuális hálózat integrációja a be- és visszahálózaton          | Nem érhető el   | Előzetes verzió |

## <a name="availability"></a>Rendelkezésre állás
A kezdeti előzetes verzió során a prémium szintű témakörök és a privát végpont-integrációval rendelkező tartományok a következő régiókban érhetők el:

- USA keleti régiója
- USA nyugati régiója, 2.
- USA déli középső régiója

## <a name="pricing-and-quotas"></a>Árképzés és kvóták
Lásd: [Event Grid díjszabás](https://azure.microsoft.com/pricing/details/event-grid/) a díjszabási részleteket az alapszintű réteg használatával. A prémium szintű díjszabás még nincs bejelentve, és ingyenes, amíg az árképzés elérhetővé nem válik.

A meglévő kvóták a témakör és a tartomány száma, és az átviteli díj a prémium szintű erőforrások ra egyaránt vonatkoznak, amíg a prémium szintű díjszabási bejelentette.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- Az alapszintű szintről a prémium szintre való frissítéshez tekintse meg a [Tarifacsomag frissítése](update-tier.md) cikket. 
- Az Event Grid erőforrásHOZ beállíthatja az IP-tűzfalat, hogy korlátozza a hozzáférést a nyilvános interneten keresztül csak az IP-címek vagy IP-címtartományok kiválasztott készletéből. A részletes útmutatásról a [Tűzfal konfigurálása](configure-firewall.md)című témakörben talál.
- A privát végpontok konfigurálhatók úgy, hogy csak a kiválasztott virtuális hálózatokhozzáférését korlátozzák. A privát [végpontok konfigurálása](configure-private-endpoints.md)című témakörben talál részletes útmutatást.