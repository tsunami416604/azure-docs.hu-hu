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
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "29717030"
---
A következő táblázat felsorolja a kvóták, és korlátozza adott [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Az Event Hubs árazással kapcsolatos információkért lásd: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

| Korlát | Hatókör | Megjegyzések | Érték |
| --- | --- | --- | --- | --- |
| Az event hubs névtér másodpercenkénti száma |Névtér |A program elutasítja későbbi kérelmeket egy új eseményközpont létrehozásához. |10 |
| Az event hubs egy partíciók száma |Entitás |- |32 |
| Az event hubs egy fogyasztói csoportok száma |Entitás |- |20 |
| Névtér AMQP-kapcsolatok száma |Névtér |További kapcsolatokat későbbi kérelmek vissza kell utasítani, és kivételt megkapta a hívó kód. |5000 |
| Az Event Hubs esemény maximális mérete|Entitás |- |256 KB |
| Az eseményközpont neveként maximális mérete |Entitás |- |50 karakter hosszú lehet |
| Nem epoch fogadók egyes fogyasztói csoportok száma |Entitás |- |5 |
| Az eseményadatok maximális megőrzési időtartam |Entitás |- |1-7 nap |
| Kapacitásegységek maximális száma |Névtér |Az átviteli egység túllépése okozza az adatokat, hogy lehet halmozódni és állít elő, egy  **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Átviteli egységek a szokásos nagyobb számú kérhet bejelentés réteghez egy [támogatási kérelem](/azure/azure-supportability/how-to-create-azure-support-request). [További átviteli egységek](../articles/event-hubs/event-hubs-auto-inflate.md) véglegesített beszerzési alapon 20 blokkok érhetők el. |20 |
| Egy névtér engedélyezési szabályok száma |Névtér|A program elutasítja az engedélyezési szabályok létrehozása későbbi kérelmeket.|12 |
