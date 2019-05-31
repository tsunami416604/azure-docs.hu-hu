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
ms.openlocfilehash: 3f3b60c3744ce9dea61054b3fa0aaccfea27d784
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238320"
---
A következő táblázat felsorolja a kvóták, és korlátozza az adott [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). További információ az Event Hubs-díjszabás: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

| Korlát | Scope | Megjegyzések | Érték |
| --- | --- | --- | --- |
| Előfizetésenként az Event Hubs-névterek száma |Előfizetés |- |100 |
| Az event hubs-névterenként száma |Névtér |A rendszer elutasítja a további kérések, egy új eseményközpont létrehozásához. |10 |
| A partíciók száma az eseményközpont száma |Entitás |- |32 |
| Egy eseményközpont fogyasztói csoportok száma |Entitás |- |20 |
| Az AMQP-kapcsolatok száma névterenként száma |Névtér |További kapcsolatok későbbi kérelmeket a rendszer elutasítja, és a egy kivételt a hívó kód által fogadott. |5,000 |
| Az Event Hubs esemény maximális mérete|Entitás |- |1 MB |
| Maximális mérete egy eseményközpont neve |Entitás |- |50 karakter hosszú lehet |
| Fogyasztói csoportok szerinti nem alapidőpont fogadók száma |Entitás |- |5 |
| Az esemény adatok maximális megőrzési időtartam |Entitás |- |1 – 7 nap |
| Kapacitásegységek maximális száma |Névtér |Az átviteli egység túllépő esetén az szabályozottan az adatok, és létrehoz egy [kiszolgáló foglalt kivétel](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Kérhető az átviteli egységek a Standard szintű, hogy a fájl egy [támogatási kérelem](/azure/azure-supportability/how-to-create-azure-support-request). [További átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) blokkok 20 vállalt vásárlási alapon érhetők el. |20 |
| Az engedélyezési szabályok névterenként száma |Névtér|Engedélyezési szabály létrehozása a későbbi kérelmeket a rendszer elutasítja.|12 |
| A GetRuntimeInformation metódus hívások száma | Entitás | - | 50 / másodperc | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs dedikált - kvóták és korlátozások
Az Event Hubs dedikált ajánlat fix havi díja, legalább 4 óra számoljuk fel. A dedikált szintet kínál összes funkciót, a standard szintű csomag, de a vállalati méretezés és korlátait az erőforrás-igényű számítási feladatok rendelkező ügyfelek számára. 

| Funkció | Limits |
| --- | ---|
| A sávszélesség |  20 kapacitásegység használható |
| Névterek | 50 / Kapacitásegység |
| Event Hubs |  Nincs korlátozva az event hubs-témakörök |
| Belépő események | Tartalmazza |
| Üzenet mérete | 1 millió bájt |
| Partíciók | 2000 / Kapacitásegység |
| Felhasználói csoportok | Nincs korlátozva az event hubs 1000 / Kapacitásegység, |
| Felügyelt kapcsolatok | 100 K csomagban foglalt |
| Üzenetmegőrzés | 90 nap, a 10 TB-ot foglalt / Kapacitásegység |
| Rögzítés | Tartalmazza |
