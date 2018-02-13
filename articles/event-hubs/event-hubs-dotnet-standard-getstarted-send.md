---
title: "Események küldése az Azure Event Hubsba a .NET Standard használatával | Microsoft Docs"
description: "Események küldése az Event Hubsba a .NET Standardban – első lépések"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2018
ms.author: sethm
ms.openlocfilehash: f59f88d47bfcb3e761f509a3d87c6d068f44e0db
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Üzenetek küldése az Azure Event Hubsba a .NET Standardban – első lépések

> [!NOTE]
> Ez a minta elérhető a [GitHubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender).

Ez az oktatóanyag bemutatja, hogyan írhat olyan .NET Core-konzolalkalmazást, amely egy üzenetkészletet küld el egy eseményközpontba. A [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)-megoldást futtathatja a jelenlegi állapotában, ha lecseréli az `EhConnectionString` és `EhEntityPath` karakterláncot az eseményközpontja értékeire. Vagy létrehozhatja a saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

* [Microsoft Visual Studio 2015 vagy 2017](http://www.visualstudio.com). Az oktatóanyag példái a Visual Studio 2017-et használják, de a Visual Studio 2015 is támogatott.
* [.NET Core Visual Studio 2015- vagy 2017-eszközök](http://www.microsoft.com/net/core).
* Azure-előfizetés.
* Eseményközpont-névtér.

Ez az oktatóanyag a Visual Studióval ír egy C#-konzolalkalmazást az üzenetek eseményközpontba való küldéséhez.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása

Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy eseményközpont típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és az eseményközpont létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md) látható eljárást, majd folytassa a következő lépésekkel.

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre. Hozzon létre egy .NET Core-konzolalkalmazást.

![Új projekt][1]

## <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

A következő lépésekkel adja hozzá a [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET Standard kódtár NuGet-csomagot a projekthez: 

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Tallózás** fülre, keressen rá a „Microsoft.Azure.EventHubs” kifejezésre, majd válassza ki a **Microsoft.Azure.EventHubs** csomagot. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Írjon egy kódrészletet az üzenetek eseményközpontba való elküldéséhez

1. Adja hozzá az alábbi `using` utasításokat a Program.cs fájl elejéhez:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adjon állandókat a `Program` osztályhoz az Event Hubs kapcsolati karakterláncával és entitásútvonalával (egyéni eseményközpontnév). Cserélje le a zárójelben lévő helyőrzőket az eseményközpont létrehozásakor beszerzett megfelelő értékekre. Győződjön meg arról, hogy az `{Event Hubs connection string}` a névtérszintű kapcsolati karakterlánc, és nem az eseményközpont karakterlánca. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Adjon hozzá egy új, `MainAsync` nevű metódust a `Program` osztályhoz az alábbiak szerint.

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Adjon hozzá egy új, `SendMessagesToEventHub` nevű metódust a `Program` osztályhoz az alábbiak szerint.

    ```csharp
    // Creates an event hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adja hozzá a következő kódsort a `Main` metódushoz a `Program` osztályban:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   A Program.cs fájlnak így kell kinéznie.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but this simple scenario
                // uses the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Creates an event hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Futtassa a programot, és ellenőrizze, hogy nincsenek-e hibák.

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

## <a name="next-steps"></a>További lépések
Az alábbi hivatkozásokon talál további információt az Event Hubsról:

* [Események fogadása az Event Hubsról](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
