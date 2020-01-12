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
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901650"
---
Az alábbi táblázatok az [Azure Event Hubsra](https://azure.microsoft.com/services/event-hubs/)vonatkozó kvótákat és korlátokat biztosítanak. További információ a Event Hubs díjszabásáról: [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/).

Az alábbi korlátok az alapszintű, a standard és a dedikált csomagok esetében gyakoriak. 

| Korlát | Hatókör | Megjegyzések | Value (Díj) |
| --- | --- | --- | --- |
| Event Hubs névterek száma/előfizetés |Előfizetés |- |100 |
| Az Event hubok száma névtérben |Névtér |Az új Event hub létrehozására vonatkozó további kérelmeket a rendszer elutasítja. |10 |
| Partíciók száma az Event hub-ban |Jogi személy |- |32 |
| Event hub-név maximális mérete |Jogi személy |- |50 karakter |
| Nem EPOCH-fogadók száma fogyasztói csoportonként |Jogi személy |- |5 |
| Kapacitásegységek maximális száma |Névtér |Az átviteli egység korlátja meghaladja az adatok szabályozását, és létrehoz egy [kiszolgáló által foglalt kivételt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Ha nagyobb számú átviteli egységet szeretne igényelni egy standard szintű csomaghoz, a [támogatási kérést](/azure/azure-portal/supportability/how-to-create-azure-support-request). A [további átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) 20 blokkban érhetők el egy véglegesített vásárlás alapján. |20 |
| Engedélyezési szabályok száma névtérben |Névtér|Az engedélyezési szabályok létrehozására vonatkozó további kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódushoz tartozó hívások száma | Jogi személy | - | 50 másodpercenként | 
| A virtuális hálózat (VNet) és az IP-konfigurációs szabályok száma | Jogi személy | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Alapszintű és standard – kvóták és korlátozások Event Hubs
| Korlát | Hatókör | Megjegyzések | Basic | Standard |
| --- | --- | --- | -- | --- |
| Event Hubs esemény maximális mérete|Jogi személy | &nbsp; | 256 KB | 1 MB |
| Fogyasztói csoportok száma az Event hub-ban |Jogi személy | &nbsp; |1 |20 |
| AMQP-kapcsolatok száma névtérben |Névtér |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívási kód kivételt kap. |100 |5000|
| Esemény-adatok maximális megőrzési ideje |Jogi személy | &nbsp; |1 nap |1-7 nap |
|Apache Kafka engedélyezett névtér|Névtér |Event Hubs névteret használó alkalmazások a Kafka protokoll használatával |Nem | Igen |
|Rögzítés |Jogi személy | Ha engedélyezve van, a mikro-kötegek ugyanazon az adatfolyamon |Nem |Igen |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedikált Event Hubs – kvóták és korlátok
Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

| Szolgáltatás | Korlátozások |
| --- | ---|
| Bandwidth |  20 ke |
| Névterek | 50/CU |
| Azure Event Hubs-eseményközpontok |  1000/névtér |
| Belépő események | Alap |
| Üzenet mérete | 1 MB |
| Partíciók | 2000/CU |
| Fogyasztói csoportok | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 100 a csomagban foglalt |
| Üzenetmegőrzés | 90 nap, 10 TB tartalmaz/CU |
| Rögzítés | Alap |
