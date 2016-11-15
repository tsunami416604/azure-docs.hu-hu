---
title: "Bevezetés az Event Hubs használatába C# környezetben | Microsoft Docs"
description: "Ez az oktatóanyag segítséget nyújt az Azure Event Hubs C# környezetben való használatához, valamint az EventProcessorHost használatához."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 2ec2378a-34f7-44c3-b976-cc444c98c338
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/02/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 35a7e4693ef979dfb947714f2fe5ce5599991189


---
# <a name="get-started-with-event-hubs"></a>Bevezetés az Event Hubs használatába
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Bevezetés
Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT).

A jelen oktatóanyag bemutatja, hogyan használhatja a klasszikus Azure portált eseményközpont létrehozásához. Bemutatja azt is, hogyan gyűjtheti az üzeneteket az eseményközpontba egy C# nyelven írt konzolalkalmazással, és hogyan kérheti le azokat párhuzamosan a C# [Event Processor Host][Event Processor Host] tár használatával.

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

* [Microsoft Visual Studio](http://visualstudio.com)
* Aktív Azure-fiók. Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Készen áll arra, hogy futtassa az alkalmazásokat.

1. A Visual Studióban nyissa meg a korábban létrehozott **Fogadó** projektet.
2. Kattintson a jobb gombbal a **Fogadó** megoldásra, kattintson a **Hozzáadás**, majd a **Létező projekt** elemre.
3. Keresse meg a meglévő Sender.csproj fájlt, majd kattintson rá duplán a megoldáshoz való hozzáadásához.
4. Kattintson újra a jobb gombbal a **Fogadó** megoldásra, majd kattintson a **Tulajdonságok** elemre. Megjelenik a **Fogadó** tulajdonságokat tartalmazó lapja.
5. Kattintson a **Kezdőprojekt**, majd a **Több kezdőprojekt** gombra. A **Művelet** mezőt állítsa a **Fogadó** és a **Küldő** projekt esetén egyaránt **Indítás** értékűre.
   
    ![][19]
6. Kattintson a **Projektfüggőségek** lehetőségre. A **Projektek** mezőben kattintson a **Küldő** elemre. Győződjön meg róla, hogy a **Függ:** mezőben a **Fogadó** be van jelölve.
   
    ![][20]
7. Kattintson az **OK** gombra a **Tulajdonságok** párbeszédpanel bezárásához.
8. Nyomja le az F5 billentyűt a **Fogadó** projekt indításához a Visual Studióból, majd várjon, amíg a projekt elindítja a fogadókat minden egyes partícióra vonatkozóan.
   
   ![][21]
9. A **Küldő** projekt automatikusan futni fog. Nyomja le az **Enter** billentyűt a konzolablakban, és láthatja, ahogy az események megjelennek a fogadó ablakban.
   
   ![][22]

Nyomja le a **Ctrl+C** billentyűkombinációt a **Küldő** ablakban a Küldő alkalmazás bezárásához, majd nyomja le az **Enter** billentyűt a Fogadó ablakban ezen alkalmazás bezárásához.

## <a name="next-steps"></a>Következő lépések
Létrehozott egy működő alkalmazást, amely létrehoz egy eseményközpontot, valamint adatokat fogad és küld, így továbbléphet a következő forgatókönyvekre:

* Teljes [Event Hubsot használó mintaalkalmazás][Event Hubsot használó mintaalkalmazás].
* [Horizontális felskálázási eseményfeldolgozás az Event Hubs használatával][Horizontális felskálázási eseményfeldolgozás az Event Hubs használatával] – minta.
* [Event Hubs – áttekintés][Event Hubs – áttekintés]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[klasszikus Azure portál]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs – áttekintés]: event-hubs-overview.md
[Az Event Hubsot használó mintaalkalmazás]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Eseményfeldolgozás horizontális felskálázása az Event Hubs használatával]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[üzenetsor-kezelési megoldás]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md




<!--HONumber=Nov16_HO2-->


