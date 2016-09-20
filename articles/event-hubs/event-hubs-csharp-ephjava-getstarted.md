<properties
    pageTitle="Bevezetés az Event Hubs használatába C# környezetben | Microsoft Azure"
    description="Ez az oktatóanyag segítséget nyújt az Azure Event Hubs használatának a megkezdéséhez, az események C# környezetben való elküldéséhez, és a Java környezetben, az EventProcessorHost használatával történő fogadásukhoz."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="06/16/2016"
    ms.author="jotaub"/>

# Bevezetés az Event Hubs használatába

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Bevezetés

Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT).

A jelen oktatóanyag bemutatja, hogyan használhatja a klasszikus Azure portált eseményközpont létrehozásához. Bemutatja azt is, hogyan gyűjtheti az üzeneteket az eseményközpontba egy C# nyelven írt konzolalkalmazással, és hogyan kérheti le azokat párhuzamosan a Java Event Processor Host tár használatával.

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Aktív Azure-fiók. <br/>Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## Az alkalmazások futtatása

Készen áll arra, hogy futtassa az alkalmazásokat.

1.  Futtassa a **Fogadó** projektet.

    ![][21]

2.  Futtassa a **Küldő** projektet.

    ![][22]

## Következő lépések

Létrehozott egy működő alkalmazást, amely létrehoz egy eseményközpontot, valamint adatokat fogad és küld, így továbbléphet a következő forgatókönyvekre:

- [Az Event Hubsot használó teljes mintaalkalmazás][].
- Az [eseményfeldolgozás horizontális felskálázása az Event Hubs használatával][] – példa.
- [Üzenetsor-kezelési megoldás][] a Service Bus által kezelt üzenetsorok használatával.
- [Event Hubs – áttekintés][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[klasszikus Azure portálra]: https://manage.windowsazure.com/
[Event Hubs – áttekintés]: event-hubs-overview.md
[Az Event Hubsot használó teljes mintaalkalmazás]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[eseményfeldolgozás horizontális felskálázása az Event Hubs használatával]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Üzenetsor-kezelési megoldás]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=sep16_HO1-->


