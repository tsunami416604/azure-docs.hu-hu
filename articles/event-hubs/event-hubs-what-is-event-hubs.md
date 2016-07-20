<properties
    pageTitle="Mi az Azure Event Hubs? | Microsoft Azure"
    description="Az Azure Event Hubs áttekintése és ismertetése"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/12/2016"
    ms.author="sethm;nberdy"/>

# Mi az Azure Event Hubs?

Az Azure Event Hubs egy kiválóan méretezhető adatbefogadási szolgáltatás, amely másodpercenként több millió esemény fogadására képes, így a csatlakoztatott eszközök és alkalmazások által létrehozott nagy mennyiségű adatot egyszerűen feldolgozhatja és elemezheti. Az Event Hubs a „bejárati ajtó” funkcióját látja el egy eseményfolyamat számára, az adatoknak egy eseményközpontba való összegyűjtését követően az adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs elválasztja az eseménystreamek létrehozását azok felhasználásától, így az események felhasználói a saját ütemezésüknek megfelelően férhetnek hozzá az eseményekhez.

## Az Event Hubs képességei

Az Event Hubs egy eseményfeldolgozási szolgáltatás, amely a felhőbe irányuló, nagy léptékű esemény- és telemetriabevitelt biztosít alacsony késéssel és nagy megbízhatósággal. Ez a szolgáltatás különösen hasznos az alábbi esetekben:

- alkalmazások kialakítása,
- felhasználói élmények vagy munkafolyamatok feldolgozása,
- az eszközök internetes hálózatát (IoT) érintő forgatókönyvek.

Az Event Hubs további fontos képességei közé tartozik például a viselkedéskövetés a mobilalkalmazásokban, a forgalmi információk gyűjtése a webfarmokról, a játékbeli események rögzítése a konzolos játékokban, vagy az ipari gépekről vagy csatlakoztatott járművekről gyűjtött telemetriaadatok.

A [Service Bus-üzenetsorokkal és témakörökkel](../service-bus/service-bus-messaging-overview.md) ellentétben az Event Hubs arra összpontosít, hogy nagy léptékben kínáljon üzenetstream-kezelést. Az Event Hubs képességei eltérnek a Service Bus-témaköröktől, például abban, hogy kimondottan a nagy mennyiségre és eseményfeldolgozási forgatókönyvekre vannak optimalizálva. Az Event Hubs így nem valósít meg bizonyos üzenetkezelési képességeket, amelyek a [témakörök](../service-bus/service-bus-fundamentals-hybrid-solutions.md#topics) esetén elérhetők. Amennyiben szüksége van ezekre a képességekre, továbbra is a témakörök használata az optimális választás.

## További lépések

Az Event Hubs szolgáltatásról az alábbi témakörökben talál részletes információkat.

- [Event Hubs – áttekintés](event-hubs-overview.md)
- [Event Hubs programozási útmutató](event-hubs-programming-guide.md)
- [Event Hubs availability and support FAQ (Az Event Hubs elérhetősége és támogatása – GYIK)](event-hubs-availability-and-support-faq.md)
- Bevezetés az [Event Hubs használatába oktatóanyag][]
- [Az Event Hubsot használó teljes mintaalkalmazás][]

[Event Hubs használatába oktatóanyag]: event-hubs-csharp-ephcs-getstarted.md
[Az Event Hubsot használó teljes mintaalkalmazás]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097


<!--HONumber=Jun16_HO2-->


