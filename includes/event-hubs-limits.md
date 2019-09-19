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
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "68912389"
---
Az alábbi táblázat az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)-ra vonatkozó kvótákat és korlátozásokat sorolja fel. További információ a Event Hubs díjszabásáról: [Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/).

| Korlát | Scope | Megjegyzések | Value |
| --- | --- | --- | --- |
| Event Hubs névterek száma/előfizetés |Subscription |- |100 |
| Az Event hubok száma névtérben |Névtér |Az új Event hub létrehozására vonatkozó további kérelmeket a rendszer elutasítja. |10 |
| Partíciók száma az Event hub-ban |Entitás |- |32 |
| Event Hubs esemény maximális mérete|Entitás |- |1 MB |
| Event hub-név maximális mérete |Entitás |- |50 karakter |
| Nem EPOCH-fogadók száma fogyasztói csoportonként |Entitás |- |5 |
| Kapacitásegységek maximális száma |Névtér |Az átviteli egység korlátja meghaladja az adatok szabályozását, és létrehoz egy [kiszolgáló által foglalt kivételt](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Ha nagyobb számú átviteli egységet szeretne igényelni egy standard szintű csomaghoz, a [támogatási kérést](/azure/azure-supportability/how-to-create-azure-support-request). A [további átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) 20 blokkban érhetők el egy véglegesített vásárlás alapján. |20 |
| Engedélyezési szabályok száma névtérben |Névtér|Az engedélyezési szabályok létrehozására vonatkozó további kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódushoz tartozó hívások száma | Entitás | - | 50 másodpercenként | 
| A virtuális hálózat (VNet) és az IP-konfigurációs szabályok száma | Entitás | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Alapszintű és standard – kvóták és korlátozások Event Hubs
| Korlát | Scope | Megjegyzések | Alapszintű | Standard |
| --- | --- | --- | -- | --- |
| Fogyasztói csoportok száma az Event hub-ban |Entitás | - |1 |20 |
| AMQP-kapcsolatok száma névtérben |Névtér |A további kapcsolatokra vonatkozó további kérelmeket a rendszer elutasítja, és a hívási kód kivételt kap. |100 |5,000|
| Esemény-adatok maximális megőrzési ideje |Entitás | - |1 nap |1-7 nap |
|Apache Kafka engedélyezett névtér|Névtér |Event Hubs névteret használó alkalmazások a Kafka protokoll használatával |Nem | Igen |
|Rögzítés |Entitás | Ha engedélyezve van, a mikro-kötegek ugyanazon az adatfolyamon |Nem |Igen |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedikált Event Hubs – kvóták és korlátok
Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

| Funkció | Korlátok |
| --- | ---|
| A sávszélesség |  20 ke |
| Névterek | 50/CU |
| Event Hubs |  1000/névtér |
| Belépő események | Tartalmazza |
| Üzenet mérete | 1 000 000 bájt |
| Partíciók | 2000/CU |
| Felhasználói csoportok | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 100 K csomagban foglalt |
| Üzenetmegőrzés | Legfeljebb 7 nap (a 90 napos megőrzés hamarosan elérhető), 10 TB-ot tartalmaz |
| Rögzítés | Tartalmazza |
