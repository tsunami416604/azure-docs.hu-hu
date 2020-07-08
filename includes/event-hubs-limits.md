---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 333f2317fcc834a10b7336bbda9a43ba16a7ad38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84317423"
---
Az alábbi táblázatok az [Azure Event Hubsra](https://azure.microsoft.com/services/event-hubs/)vonatkozó kvótákat és korlátokat biztosítanak. További információ a Event Hubs díjszabásáról: [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/).

A következő korlátok az alapszintű és a standard szintű csomagokban vannak meghatározva. 

| Korlát | Hatókör | Jegyzetek | Érték |
| --- | --- | --- | --- |
| Event Hubs névterek száma/előfizetés |Előfizetés |- |100 |
| Az Event hubok száma névtérben |Névtér |Az új Event hub létrehozására vonatkozó további kérelmeket a rendszer elutasítja. |10 |
| Partíciók száma az Event hub-ban |Entitás |- |32 |
| Event hub-név maximális mérete |Entitás |- | 256 karakter |
| Fogyasztói csoport nevének maximális mérete |Entitás |- | 256 karakter |
| Nem EPOCH-fogadók száma fogyasztói csoportonként |Entitás |- |5 |
| Maximális átviteli egységek |Névtér |Az átviteli egység korlátja meghaladja az adatok szabályozását, és létrehoz egy [kiszolgáló által foglalt kivételt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Ha nagyobb számú átviteli egységet szeretne igényelni egy standard szintű csomaghoz, a [támogatási kérést](/azure/azure-portal/supportability/how-to-create-azure-support-request). A [további átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) 20 blokkban érhetők el egy véglegesített vásárlás alapján. |20 |
| Engedélyezési szabályok száma névtérben |Névtér|Az engedélyezési szabályok létrehozására vonatkozó további kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódushoz tartozó hívások száma | Entitás | - | 50 másodpercenként | 
| A virtuális hálózat (VNet) és az IP-konfigurációs szabályok száma | Entitás | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Alapszintű és standard – kvóták és korlátozások Event Hubs
| Korlát | Hatókör | Jegyzetek | Alapszintű | Standard |
| --- | --- | --- | -- | --- |
| Event Hubs esemény maximális mérete|Entitás | &nbsp; | 256 KB | 1 MB |
| Fogyasztói csoportok száma az Event hub-ban |Entitás | &nbsp; |1 |20 |
| AMQP-kapcsolatok száma névtérben |Névtér |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívási kód kivételt kap. |100 |5000|
| Esemény-adatok maximális megőrzési ideje |Entitás | &nbsp; |1 nap |1-7 nap |
|Apache Kafka engedélyezett névtér|Névtér |Event Hubs névteret használó alkalmazások a Kafka protokoll használatával |Nem | Igen |
|Rögzítés |Entitás | Ha engedélyezve van, a mikro-kötegek ugyanazon az adatfolyamon |Nem |Igen |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedikált Event Hubs – kvóták és korlátok
Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

| Szolgáltatás | Korlátok |
| --- | ---|
| Sávszélesség |  20 ke |
| Névterek | 50/CU |
| Event Hubs |  1000/névtér |
| Bejövő események | Tartalmazza |
| Üzenet mérete | 1 MB |
| Partíciók | 2000/CU |
| Felhasználói csoportok | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 100 a csomagban foglalt |
| Üzenetek megőrzése | 90 nap, 10 TB tartalmaz/CU |
| Rögzítés | Tartalmazza |
