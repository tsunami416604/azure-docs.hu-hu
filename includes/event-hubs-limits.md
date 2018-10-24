---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: a49ce4e997a21e0db707c851f5ea46817bcb642e
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49960303"
---
A következő táblázat felsorolja a kvóták, és korlátozza az adott [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). További információ az Event Hubs-díjszabás: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

| Korlát | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- | --- |
| Előfizetésenként az Event Hubs-névterek száma |Előfizetés |- |1000 |
| Az event hubs-névterenként száma |Névtér |Egy új eseményközpont létrehozása a későbbi kérelmeket a rendszer elutasítja. |10 |
| A partíciók száma az eseményközpont száma |Entitás |- |32 |
| Egy eseményközpont fogyasztói csoportok száma |Entitás |- |20 |
| Az AMQP-kapcsolatok száma névterenként száma |Névtér |További kapcsolatok későbbi kérelmeket a rendszer elutasítja, és a egy kivételt a hívó kód által fogadott. |5000 |
| Az Event Hubs esemény maximális mérete|Entitás |- |256 KB |
| Maximális mérete egy eseményközpont neve |Entitás |- |50 karakter hosszú lehet |
| Fogyasztói csoportok szerinti nem alapidőpont fogadók száma |Entitás |- |5 |
| Az esemény adatok maximális megőrzési időtartam |Entitás |- |1 – 7 nap |
| Kapacitásegységek maximális száma |Névtér |Az átviteli egység túllépő esetén az szabályozottan az adatok, és létrehoz egy  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Több standard kapacitásegység is kérhető bejelentés réteghez egy [támogatási kérelem](/azure/azure-supportability/how-to-create-azure-support-request). [További átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) blokkok 20 vállalt vásárlási alapon érhetők el. |20 |
| Az engedélyezési szabályok névterenként száma |Névtér|Engedélyezési szabály létrehozása a későbbi kérelmeket a rendszer elutasítja.|12 |
