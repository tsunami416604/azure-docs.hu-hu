---
title: Események a .NET-keretrendszer – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk egy útmutató, amely elküldi az eseményeket az Azure Event Hubsba a .NET Fraemwork alkalmazás létrehozásához.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 062dc707dea99ed6e5e04905a13572c234f0c172
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091159"
---
# <a name="send-events-to-azure-event-hubs-using-the-net-framework"></a>Események küldése az Azure Event Hubsba a .NET-keretrendszer használatával
Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan küldhet eseményeket egy eseményközpontba egy C# nyelven írt konzolalkalmazással a .NET-keretrendszer használatával. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* [A Microsoft Visual Studio 2017-es vagy újabb](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása
Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az eseményközpont létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md) olvasható eljárást, majd folytassa a jelen oktatóanyag további lépéseivel.

A kapcsolati sztring lekérése az eseményközpont-névtér kövesse a cikkben szereplő: [kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Az oktatóanyag későbbi részében használja a kapcsolati karakterláncot.

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Hozzon létre egy új Visual C# asztalialkalmazás-projektet a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek a **Sender** (Küldő) nevet.
   
![Konzolalkalmazás létrehozása](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a **Sender** (Küldő) projektre, majd kattintson a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése megoldáshoz) parancsra. 
2. Kattintson a **Browse** (Tallózás) lapra, és keressen a következőre: `WindowsAzure.ServiceBus`. Kattintson az **Install** (Telepítés) gombra, és fogadja el a használati feltételeket. 
   
    ![Service Bus NuGet-csomag telepítése](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    A Visual Studio letölti és telepíti az [Azure Service Bus library NuGet package](https://www.nuget.org/packages/WindowsAzure.ServiceBus) (Azure szolgáltatásbusz-könyvtár NuGet-csomag) elemet, és hozzáad egy rá mutató hivatkozást is.

## <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adja hozzá a következő mezőket a **Program** osztályhoz, lecserélve a helyőrző értékeket az előző szakaszban létrehozott eseményközpont nevével, valamint a korábban elmentett névtérszintű kapcsolati sztringgel. Az eseményközpont a kapcsolati karakterlánc másolhatja **kapcsolati karakterlánc – elsődleges** kulcsot **RootManageSharedAccessKey** az Event Hub oldalon az Azure Portalon. Részletes lépéseiért lásd: [kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Adja hozzá a **Program** osztályhoz a következő módszert:
   
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
4. Végül adja a következő sorokat a **Main** metódushoz:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.
  
Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>További lépések
Ez a rövid útmutatóban elküldött üzeneteket egy eseményközpontba, .NET-keretrendszer használatával. Események fogadása az event hubs .NET-keretrendszer használatával kapcsolatban lásd: [események fogadása az event hubs – .NET-keretrendszer](event-hubs-dotnet-framework-getstarted-receive-eph.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

