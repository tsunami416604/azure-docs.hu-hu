---
title: "Események küldése az Azure Event Hubs használatával a .NET-szabvány |} Microsoft Docs"
description: "Események küldése az Event Hubs .NET-szabvány az első lépések"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: sethm
ms.openlocfilehash: 8af9d70965c1c9ad8c49b7d2bb04244fc207058d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-sending-messages-to-azure-event-hubs-in-net-standard"></a>Ismerkedés az Azure Event Hubs a .NET-szabvány üzenetküldésre

> [!NOTE]
> Ez a minta érhető el a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender).

Ez az oktatóanyag bemutatja, hogyan írhat egy olyan konzolalkalmazást a .NET Core által küldött üzenetek készleteit az eseményközpontba. Futtathatja a [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) megoldás,-, váltja fel a `EhConnectionString` és `EhEntityPath` a event hub értékű karakterláncokat. Vagy a lépésekkel ebben az oktatóanyagban saját.

## <a name="prerequisites"></a>Előfeltételek

* [A Microsoft Visual Studio 2015-öt vagy 2017](http://www.visualstudio.com). A példák a Visual Studio 2017 oktatóanyag használja, de a Visual Studio 2015-öt is támogatott.
* [A .NET core Visual Studio 2015-öt vagy 2017 eszközök](http://www.microsoft.com/net/core).
* Azure-előfizetés.
* Az event hub névtér.

Üzenetek küldése egy eseményközpontot, a írása C# konzolalkalmazást a Visual Studio használjuk.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs-névtér és eseményközpont létrehozása

Az első lépés az, hogy használja a [Azure-portálon](https://portal.azure.com) hub eseménytípushoz névtér létrehozása, és szerezze be a felügyeleti hitelesítő adatokat az alkalmazásban az event hubs folytatott kommunikációhoz szükséges. A névtér és az eseményközpontok létrehozásához kövesse a [Ez a cikk](event-hubs-create.md), majd folytassa a következő lépéseket.

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót. Kattintson a **File** (Fájl) menüben a **New** (Új), majd a **Project** (Projekt) elemre. A .NET Core Konzolalkalmazás létrehozása.

![Új projekt][1]

## <a name="add-the-event-hubs-nuget-package"></a>Az Event Hubs NuGet-csomag hozzáadása

Adja hozzá a [ `Microsoft.Azure.EventHubs` ](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .NET-szabvány library NuGet-csomagot a projekthez az alábbiak szerint: 

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Tallózás** fülre, majd keresse meg a "Microsoft.Azure.EventHubs", és válassza ki a **Microsoft.Azure.EventHubs** csomag. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Kódírást az üzenetek küldése az event hubs

1. Adja hozzá az alábbi `using` utasításokat a Program.cs fájl elejéhez.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Adja hozzá a állandók a `Program` osztály az Event Hubs kapcsolati karakterlánc és egyéb entitások elérési utat (egyéni esemény központ neve). Cserélje le a helyőrzőket zárójelek közé a megfelelő értékeket az eseményközpont létrehozásakor beszerzett.

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Nevű új módszer `MainAsync` számára a `Program` osztály, az alábbiak szerint:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
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

4. Nevű új módszer `SendMessagesToEventHub` számára a `Program` osztály, az alábbiak szerint:

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

5. Adja hozzá a következő kódot a `Main` metódust a `Program` osztály.

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
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
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

## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Események fogadása az Event Hubs](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
