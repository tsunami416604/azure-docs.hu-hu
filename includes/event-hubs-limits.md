---
title: fájlbefoglalás
description: fájlbefoglalás
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012837"
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
|Apache Kafka engedélyezett névtér|Névtér |Event Hubs névteret használó alkalmazásokat a Kafka protokoll használatával. További információ: [Azure Event Hubs használata Apache Kafka alkalmazásokból](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md). |Nem | Igen |
|Rögzítés |Entitás | Ha engedélyezve van, a mikro-kötegek ugyanazon az adatfolyamon. További információ: [események rögzítése az Azure-Event Hubs az Azure-ban blob Storage vagy Azure Data Lake Storage](../articles/event-hubs/event-hubs-capture-overview.md). |Nem |Igen |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedikált Event Hubs – kvóták és korlátok
Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

| Jellemző | Korlátok |
| --- | ---|
| Sávszélesség |  20 ke |
| Névterek | 50/CU |
| Event Hubs |  1000/névtér |
| Bejövő események | Tartalmazza |
| Üzenet mérete | 1 MB |
| Partíciók | 2000/CU |
| Fogyasztói csoportok | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 100 a csomagban foglalt |
| Üzenetek megőrzése | 90 nap, 10 TB tartalmaz/CU |
| Rögzítés | Tartalmazza |
