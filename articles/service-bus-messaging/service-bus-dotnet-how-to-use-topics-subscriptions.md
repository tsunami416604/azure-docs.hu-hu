---
title: "Ismerkedés az Azure Service Bus-üzenettémákkal és előfizetésekkel | Microsoft Docs"
description: "Írjon Service Bus-üzenettémákat és előfizetéseket használó C# konzolalkalmazást."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/10/2017
ms.author: sethm
ms.openlocfilehash: 3646d14be662af0fdf80790cb53ddc581b33a146
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-service-bus-topics"></a>Bevezetés a Service Bus-üzenettémák használatába

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez az oktatóanyag a következő lépéseken vezet végig:

1. Service Bus-névtér létrehozása az Azure Portal használatával.
2. Service Bus-üzenettéma létrehozása az Azure Portal használatával.
3. Service Bus-előfizetés létrehozása az üzenettémához az Azure Portal használatával.
4. Az üzenettémához üzenetet küldő konzolalkalmazás megírása.
5. Az előfizetéstől üzenetet fogadó konzolalkalmazás megírása.

## <a name="prerequisites"></a>Előfeltételek

1. [Visual Studio 2015 vagy újabb](http://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
2. Azure-előfizetés.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Névtér létrehozása az Azure Portal használatával

Ha már létrehozta a Service Bus Messaging-névteret, lépjen a [Üzenettéma létrehozása az Azure Portal használatával](#2-create-a-topic-using-the-azure-portal) szakaszra.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Üzenettéma létrehozása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra][azure-portal].
2. A portál bal oldali navigációs panelén kattintson a **Service Bus** elemre (ha nem lát **Service Bus** elemet, kattintson a **További szolgáltatások** lehetőségre).
3. Kattintson a névtérre, amelyben az üzenettémát létre kívánja hozni. Megjelenik a névtér áttekintő panelje:
   
    ![Üzenettémakör létrehozása][createtopic1]
4. A **Service Bus-névtér** panelen kattintson az **Üzenettémák**, majd az **Üzenettéma hozzáadása** elemre.
   
    ![Üzenettéma kiválasztása][createtopic2]
5. Adjon meg egy nevet a témához, és törölje a jelet a **Particionálás engedélyezése** lehetőség mellől. A többi beállítást hagyja az alapértelmezett értékükön.
   
    ![Új kiválasztása][createtopic3]
6. Kattintson a panel alján található **Létrehozás** gombra.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Előfizetés létrehozása az üzenettémához

1. A portál-erőforrások panelen kattintson az 1. lépésben létrehozott névtérre majd a 2. lépésben létrehozott üzenettéma nevére.
2. Ha egy előfizetést szeretne hozzáadni ehhez a témakörhöz, kattintson az áttekintő panel tetején az **Előfizetés** melletti plusz jelre.

    ![Előfizetés létrehozása][createtopic4]

3. Adjon egy nevet az előfizetésnek. A többi beállítást hagyja az alapértelmezett értékükön.

## <a name="4-send-messages-to-the-topic"></a>4. Üzenet küldése az üzenettémához

A Visual Studio használatával C# konzolalkalmazást írunk az üzeneteknek az üzenettémához való küldéséhez.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót, majd hozzon létre egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet.

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Browse** (Tallózás) fülre, keressen rá a **WindowsAzure.ServiceBus** kifejezésre, majd válassza ki a **WindowsAzure.ServiceBus** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
   
    ![NuGet-csomag kiválasztása][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-topic"></a>Írjon egy kódrészletet egy üzenet küldéséhez az üzenettémához

1. Adja hozzá az alábbi `using` utasítást a Program.cs fájl elejéhez.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adja hozzá a következő kódot a(z) `Main` metódushoz. A(z) `connectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati karakterláncot, `topicName` értékének pedig az üzenettéma létrehozásakor használt nevet.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    A Program.cs fájlnak így kell kinéznie.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Futtassa a programot, és ellenőrizze az Azure Portalon: kattintson az üzenettéma nevére a névtér **Áttekintés** paneljén. Megjelenik az üzenettéma **Essentials** (Alapok) panelje. Arra figyeljen, hogy a panel alsó részén megjelenő előfizetés-lista minden tagjának **Message Count** (Üzenetek száma) értéke elvileg már 1. Valahányszor a küldő alkalmazást az üzenetek (a következő szakaszban leírt módon történő) fogadása nélkül futtatja, ez az érték egyel növekszik. Azt is megfigyelheti, hogy a téma jelenlegi méretét tükröző **Current** (Jelenlegi) érték az **Essentials** panelen mindig növekszik, amikor az alkalmazás újabb üzenetet ad hozzá a témához/előfizetéshez.
   
      ![Üzenet mérete][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Üzenet fogadása az előfizetéstől

1. Az imént elküldött üzenet vagy üzenetek fogadásához hozzon létre egy új konzolalkalmazást, majd vegyen fel egy hivatkozást a Service Bus NuGet-csomagjára, hasonlóan az előző küldő alkalmazáshoz.
2. Adja hozzá az alábbi `using` utasítást a Program.cs fájl elejéhez.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adja hozzá a következő kódot a(z) `Main` metódushoz. A(z) `connectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati karakterláncot, `topicName` értékének pedig az üzenettéma létrehozásakor használt nevet. Győződjön meg továbbá arról, hogy a `<your subscription name>` helyett a 3. lépésben létrehozott előfizetés nevét adja meg. 
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    A Program.cs fájlnak így kell kinéznie:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Futtassa a programot, majd ellenőrizze ismét a portálon. Figyelje meg, hogy az **Message Count** (Üzenetek száma) és a **Current** (Jelenlegi) értéke most 0.
   
    ![A témakör hossza][topic-message-receive]

Gratulálunk! Létrehozott egy üzenettémát és egy előfizetést, elküldött egy üzenetet, és fogadta is azt.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [GitHub-tárunkat, ahol további példákat talál](https://github.com/Azure/azure-service-bus/tree/master/samples), amelyek a Service Bus üzenetkezelési szolgáltatásának néhány speciális funkcióját mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
