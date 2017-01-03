---
title: "Bevezetés az Event Hubs használatába C# környezetben | Microsoft Docs"
description: "Ez az oktatóanyag segítséget nyújt az Azure Event Hubs használatának a megkezdéséhez, az események C# környezetben való elküldéséhez, és a Java környezetben, az EventProcessorHost használatával történő fogadásukhoz."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: c9aa7cebab12eafe71476ee45ef578d3201e69e5
ms.openlocfilehash: e7c38aa5a778d5e8a653e753e5794d7c7da31dc1


---
# <a name="get-started-with-event-hubs"></a>Bevezetés az Event Hubs használatába
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Bevezetés
Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT).

A jelen oktatóanyag bemutatja, hogyan használhatja az Azure Portalt eseményközpont létrehozásához. Bemutatja azt is, hogyan gyűjtheti az üzeneteket az eseményközpontba egy C# nyelven írt konzolalkalmazással, és hogyan kérheti le azokat párhuzamosan a Java Event Processor Host tár használatával.

Az oktatóanyag teljesítéséhez az alábbiakra lesz szüksége:

* [Microsoft Visual Studio](http://visualstudio.com)
* Aktív Azure-fiók. <br/>Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Az alkalmazások futtatása
Készen áll arra, hogy futtassa az alkalmazásokat.

1. Futtassa a **Fogadó** projektet.
   
   ![][21]
2. Futtassa a **Küldő** projektet.
   
   ![][22]

## <a name="next-steps"></a>Következő lépések
Létrehozott egy működő alkalmazást, amely létrehoz egy eseményközpontot, valamint adatokat fogad és küld, így továbbléphet a következő forgatókönyvekre:

* [Az Event Hubsot használó teljes mintaalkalmazás][sample application that uses Event Hubs].
* Az [eseményfeldolgozás horizontális felskálázása az Event Hubs használatával][Scale out Event Processing with Event Hubs] – példa.
* [Event Hubs – áttekintés][Event Hubs overview]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Jan17_HO1-->


