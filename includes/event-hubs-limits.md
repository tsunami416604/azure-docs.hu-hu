---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2aca4f2c236112b80e9fc985cf80ccad6d82bde3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75901650"
---
Az alábbi táblázatok az Azure Event Hubs-ra jellemző [kvótákat és korlátozásokat biztosítanak.](https://azure.microsoft.com/services/event-hubs/) Az Event Hubs díjszabásáról az [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/)című témakörben talál további információt.

A következő korlátok gyakoriak az alapszintű, a standard és a dedikált szinteken. 

| Korlát | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- |
| Az Event Hubs névtereinek száma előfizetésenként |Előfizetés |- |100 |
| Eseményközpontok száma névtérenként |Névtér |Az új eseményközpont létrehozására vonatkozó további kérelmek et a rendszer elutasítja. |10 |
| Partíciók száma eseményközpontonként |Entitás |- |32 |
| Az eseményközpont nevének maximális mérete |Entitás |- |50 karakter |
| A nem korszakfogadók száma fogyasztói csoportonként |Entitás |- |5 |
| Maximális átviteli egységek |Névtér |Az átviteli egység korlátjának túllépése az adatok szabályozását eredményezi, és [kiszolgálófoglalt kivételt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)hoz létre. Ha nagyobb számú átviteli egységet szeretne kérni egy standard szinthez, nyújtson [be támogatási kérelmet.](/azure/azure-portal/supportability/how-to-create-azure-support-request) [További átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) állnak rendelkezésre a 20 blokkban, lekötött vásárlás alapján. |20 |
| Engedélyezési szabályok száma névtérenként |Névtér|Az engedélyezési szabály létrehozására vonatkozó további kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódus hívásainak száma | Entitás | - | 50 másodpercenként | 
| Virtuális hálózati (VNet) és IP konfigurációs szabályok száma | Entitás | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs Alapszintű és Standard – kvóták és korlátok
| Korlát | Hatókör | Megjegyzések | Basic | Standard |
| --- | --- | --- | -- | --- |
| Az Event Hubs esemény eseményének maximális mérete|Entitás | &nbsp; | 256 KB | 1 MB |
| Fogyasztói csoportok száma eseményközpontonként |Entitás | &nbsp; |1 |20 |
| AMQP-kapcsolatok száma névtérenként |Névtér |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívókód kivételt fogad. |100 |5000|
| Az eseményadatok maximális megőrzési ideje |Entitás | &nbsp; |1 nap |1-7 nap |
|Apache Kafka engedélyezett névtér|Névtér |Az Event Hubs névtér a Kafka protokoll használatával streameli az alkalmazásokat |Nem | Igen |
|Rögzítés |Entitás | Ha engedélyezve van, a mikrokötegek ugyanazon az adatfolyamon |Nem |Igen |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedikált eseményközpontok – kvóták és korlátok
Az Event Hubs dedikált ajánlat számlázása rögzített havi díj, legalább 4 óra használat. A dedikált szint a Standard csomag összes funkcióját kínálja, de vállalati méretezési kapacitással és korlátokkal az igényes számítási feladatokat igénylő ügyfelek számára. 

| Szolgáltatás | Korlátok |
| --- | ---|
| Sávszélesség |  20 cus |
| Névterek | 50 CU-nként |
| Event Hubs |  1000 névtérenként |
| Be- és be- és visszakozóesemények | Tartalmazza |
| Üzenet mérete | 1 MB |
| Partíciók | 2000 CU-nként |
| Felhasználói csoportok | Nincs korlátozás CU-nként, 1000 eseményközpontonként |
| Közvetített kapcsolatok | 100 K-val |
| Üzenetek megőrzése | 90 nap, 10 TB cu-nként |
| Rögzítés | Tartalmazza |
