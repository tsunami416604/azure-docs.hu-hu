---
title: "Események küldése az Azure Event Hubsba a .NET-keretrendszer használatával | Microsoft Docs"
description: "Bevezetés az események Event Hubsba való küldésébe a .NET-keretrendszer használatával"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 16da4e1732445b2480daf18130ea74935c6e6c49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Események küldése az Azure Event Hubsba a .NET-keretrendszer használatával

## <a name="introduction"></a>Introduction (Bevezetés)

Az Event Hubs szolgáltatás a csatlakoztatott eszközökről és alkalmazásokból származó nagy mennyiségű eseményadatot dolgoz fel (telemetria). Miután összegyűjtötte az adatokat az Event Hubsban, az adatok egy tárolási fürt használatával tárolhatja, vagy átalakíthatja egy valós idejű elemzési szolgáltató segítségével. Ez az átfogó eseménygyűjtési és -feldolgozási képesség kulcsfontosságú alkotóeleme a modern alkalmazásarchitektúráknak, beleértve az eszközök internetes hálózatát (IoT).

A jelen oktatóanyag bemutatja, hogyan használhatja az [Azure Portalt](https://portal.azure.com) egy eseményközpont létrehozásához. Azt is ismerteti, hogyan küldhet eseményeket egy eseményközpontba egy C# nyelven írt konzolalkalmazással a .NET-keretrendszer használatával. Az események a .NET-keretrendszerrel való fogadásáról lásd a [Események fogadása a .NET-keretrendszerrel](event-hubs-dotnet-framework-getstarted-receive-eph.md) című cikket, vagy kattintson a megfelelő fogadónyelvre a bal oldali tartalomjegyzékben.

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [Microsoft Visual Studio 2015 vagy újabb](http://visualstudio.com). A jelen oktatóanyag képernyőképei a Visual Studio 2017-et használják.
* Aktív Azure-fiók. Ha még nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free/).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása

Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az eseményközpont létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md) olvasható eljárást, majd folytassa a jelen oktatóanyag további lépéseivel.

## <a name="create-a-sender-console-application"></a>Küldő konzolalkalmazás létrehozása

Ebben a szakaszban egy Windows konzolalkalmazást fog írni, amely elküldi az eseményeket az eseményközpontjába.

1. Hozzon létre egy új Visual C# asztalialkalmazás-projektet a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek a **Sender** (Küldő) nevet.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)
2. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **Sender** (Küldő) projektre, majd kattintson a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése megoldáshoz) parancsra. 
3. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `WindowsAzure.ServiceBus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket. 
   
    ![](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    A Visual Studio letölti és telepíti az [Azure Service Bus library NuGet package](https://www.nuget.org/packages/WindowsAzure.ServiceBus) (Azure szolgáltatásbusz-könyvtár NuGet-csomag) elemet, és hozzáad egy rá mutató hivatkozást is.
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
  ```csharp
  using System.Threading;
  using Microsoft.ServiceBus.Messaging;
  ```
5. Adja hozzá a következő mezőket a **Program** osztályhoz, lecserélve a helyőrző értékeket az előző szakaszban létrehozott eseményközpont nevével, valamint a korábban elmentett névtérszintű kapcsolati karakterlánccal.
   
  ```csharp
  static string eventHubName = "{Event Hub name}";
  static string connectionString = "{send connection string}";
  ```
6. Adja hozzá a **Program** osztályhoz a következő módszert:
   
  ```csharp
  static void SendingRandomMessages()
  {
      var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
      while (true)
      {
          try
          {
              var message = Guid.NewGuid().ToString();
              Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
              eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
          }
          catch (Exception exception)
          {
              Console.ForegroundColor = ConsoleColor.Red;
              Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
              Console.ResetColor();
          }
   
          Thread.Sleep(200);
      }
  }
  ```
   
  Ez a metódus folyamatosan küldi az eseményeket az eseményközpontjának, 200 ezredmásodperces késleltetéssel.
7. Végül adja a következő sorokat a **Main** metódushoz:
   
  ```csharp
  Console.WriteLine("Press Ctrl-C to stop the sender process");
  Console.WriteLine("Press Enter to start now");
  Console.ReadLine();
  SendingRandomMessages();
  ```
8. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.
  
Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>Következő lépések
Most, hogy létrehozott egy működő alkalmazást, amely létrehoz egy eseményközpontot és adatokat is küld, továbbléphet a következő forgatókönyvekre:

* [Események fogadása az Event Processor Host használatával](event-hubs-dotnet-framework-getstarted-receive-eph.md)
* [Event Processor Host-referencia](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)
* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

