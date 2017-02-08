---
title: Mi az Azure Event Hubs? | Microsoft Docs
description: "Az Azure Event Hubs áttekintése és ismertetése"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 05ca343cfdfc602759eb3ea30a7186a0bb47bd74
ms.openlocfilehash: eddc3e7c4914936a8a83a042dc0f7d528b91f059


---
# <a name="what-is-azure-event-hubs"></a>Mi az Azure Event Hubs?
Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Az Event Hubs a „bejárati ajtó” funkcióját látja el egy eseményfolyamat számára, az adatoknak egy eseményközpontba való összegyűjtését követően az adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez. További információk és műszaki részletek: [Event Hubs – áttekintés](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Az Event Hubs képességei
Az Event Hubs egy eseményfeldolgozási szolgáltatás, amely nagy léptékű esemény- és telemetriafeldolgozást biztosít alacsony késéssel és nagy megbízhatósággal. Ez a szolgáltatás különösen hasznos az alábbi esetekben:

* alkalmazások kialakítása,
* felhasználói élmények vagy munkafolyamatok feldolgozása,
* az eszközök internetes hálózatát (IoT) érintő forgatókönyvek.

Az Event Hubs további fontos képességei közé tartozik például a viselkedéskövetés a mobilalkalmazásokban, a forgalmi információk gyűjtése a webfarmokról, a játékbeli események rögzítése a konzolos játékokban, vagy az ipari gépekről vagy csatlakoztatott járművekről gyűjtött telemetriaadatok.

## <a name="next-steps"></a>Következő lépések
Az Event Hubs szolgáltatásról az alábbi témakörökben talál részletes információkat.

* [Event Hubs – áttekintés](event-hubs-overview.md)
* [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
* [Event Hubs availability and support FAQ (Az Event Hubs elérhetősége és támogatása – GYIK)](event-hubs-availability-and-support-faq.md)
* Bevezetés az [Event Hubs használatába oktatóanyag][Event Hubs tutorial]
* [Az Event Hubsot használó teljes mintaalkalmazás][sample application that uses Event Hubs]

[Event Hubs tutorial]: event-hubs-csharp-ephcs-getstarted.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Dec16_HO1-->


