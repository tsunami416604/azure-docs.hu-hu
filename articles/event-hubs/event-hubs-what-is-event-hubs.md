---
title: Mi az az Azure Event Hubs, és mire használható? | Microsoft Docs
description: Az Azure Event Hubs áttekintése és bemutatása – Felhőméretű telemetriaadat-feldolgozás webhelyekről, alkalmazásokból és eszközökről származó adatok esetén
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: sethm
ms.openlocfilehash: 08b78f59dc758f12556f332546ccb6043bef2812
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="what-is-event-hubs"></a>Mi az Event Hubs?

Az Azure Event Hubs egy kiválóan méretezhető adatstreamelési platform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az alacsony késésű és nagy méretű [közzétételi-feliratkozási képességeket](/biztalk/core/publish-and-subscribe-architecture) biztosító Event Hubs az „első lépcsőfok” a big data jellegű adatmennyiségek kezelése irányában.

Az Event Hubs segítségével lehetségessé válik például a viselkedéskövetés a mobilalkalmazásokban, a forgalmi információk gyűjtése a webfarmokról, a játékbeli események rögzítése a konzolos játékokban, vagy telemetriaadatok gyűjtése az ipari gépekről, csatlakoztatott járművekről vagy más eszközökről.

## <a name="azure-event-hubs-overview"></a>Azure Event Hubs – áttekintés

Az Event Hubs gyakran tölti be az eseményfolyamatok „bejárati ajtajának” a szerepét a megoldásarchitektúrákban, mely szerepet gyakran nevezik *eseménybetöltőnek*. Az eseménybetöltő egy olyan összetevő vagy szolgáltatás, amely az esemény-közzétevők és az eseményfelhasználók közé ékelődve elkülöníti az eseménystream létrehozását az események felhasználásától. A következő ábra ezt az architektúrát ábrázolja:

![Event Hubs](./media/event-hubs-what-is-event-hubs/event_hubs_full_pipeline.png)

Az Event Hubs üzenetstream-kezelési képességet is biztosít, de olyan tulajdonságokkal rendelkezik, amelyek eltérnek a hagyományos vállalati üzenetkezelés jellemzőitől. Az Event Hubs képességei kimondottan a nagy mennyiségre és eseményfeldolgozási forgatókönyvekre vannak optimalizálva. Az Event Hubs különbözik az [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)-üzenetkezeléstől, és nem valósít meg bizonyos képességeket, amelyek a különböző [Service Bus-üzenetküldési](/azure/service-bus-messaging/) entitások (például a témakörök) esetén elérhetőek.

## <a name="event-hubs-features"></a>Event Hubs-szolgáltatások

Az Event Hubs az alábbi fő elemeket kínálja:

- [**Esemény-közzétevő**](event-hubs-features.md#event-publishers): minden entitás, amely adatokat küld egy eseményközpontnak. Az esemény közzététele AMQP 1.0-n vagy HTTPS-en keresztül történik.
- [**Rögzítés**](event-hubs-features.md#capture): Event Hubs-alapú streamelt adatokat rögzíthet, amelyeket egy Azure Blob Storage-fiókban tárolhat.
- [**Partíciók**](event-hubs-features.md#partitions): beállítható, hogy a felhasználó csak az eseménystream egy meghatározott részhalmazát, azaz partícióját olvashassa.
- [**SAS-tokenek**](event-hubs-features.md#sas-tokens): meghatározzák és hitelesítik az esemény-közzétevőt.
- [**Eseményfelhasználó**](event-hubs-features.md#event-consumers): minden entitás, amely eseményadatokat olvas egy eseményközpontból. Az eseményfelhasználó az AMQP 1.0-n keresztül csatlakozik. 
- [**Felhasználói csoportok**](event-hubs-features.md#consumer-groups): minden egyes több forrást felhasználó alkalmazás az eseménystream külön nézetével rendelkezik, így ezek a felhasználók a többitől függetlenül tevékenykedhetnek.
- [**Átviteli egységek**](event-hubs-features.md#capacity): előre megvásárolt kapacitásegységek. Egy partíció legfeljebb egy átviteli egységgel rendelkezhet.

További technikai részletek ezekről és más Event Hubs-szolgáltatásokról: [Event Hubs features overview](event-hubs-features.md) (Event Hubs-szolgáltatások – Áttekintés). 

## <a name="next-steps"></a>További lépések

Részletes információk az Event Hubs díjszabásáról: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
 
 

