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
ms.openlocfilehash: 9d6b54027adcf2b12c6ca4081a11208a31f620e8
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58919255"
---
A következő táblázat felsorolja a kvóták, és korlátozza az adott [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). További információ az Event Hubs-díjszabás: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

| Korlát | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- |
| Előfizetésenként az Event Hubs-névterek száma |Előfizetés |- |100 |
| Az event hubs-névterenként száma |Névtér |A rendszer elutasítja a további kérések, egy új eseményközpont létrehozásához. |10 |
| A partíciók száma az eseményközpont száma |Entitás |- |32 |
| Egy eseményközpont fogyasztói csoportok száma |Entitás |- |20 |
| Az AMQP-kapcsolatok száma névterenként száma |Névtér |További kapcsolatok későbbi kérelmeket a rendszer elutasítja, és a egy kivételt a hívó kód által fogadott. |5000 |
| Az Event Hubs esemény maximális mérete|Entitás |- |1 MB |
| Maximális mérete egy eseményközpont neve |Entitás |- |50 karakter hosszú lehet |
| Fogyasztói csoportok szerinti nem alapidőpont fogadók száma |Entitás |- |5 |
| Az esemény adatok maximális megőrzési időtartam |Entitás |- |1 – 7 nap |
| Kapacitásegységek maximális száma |Névtér |Az átviteli egység túllépő esetén az szabályozottan az adatok, és létrehoz egy [kiszolgáló foglalt kivétel](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Kérhető az átviteli egységek a Standard szintű, hogy a fájl egy [támogatási kérelem](/azure/azure-supportability/how-to-create-azure-support-request). [További átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) blokkok 20 vállalt vásárlási alapon érhetők el. |20 |
| Az engedélyezési szabályok névterenként száma |Névtér|Engedélyezési szabály létrehozása a későbbi kérelmeket a rendszer elutasítja.|12 |
