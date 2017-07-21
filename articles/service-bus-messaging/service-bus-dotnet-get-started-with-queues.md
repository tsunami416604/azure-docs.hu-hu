---
title: "Bevezetés az Azure Service Bus által kezelt üzenetsorok használatába | Microsoft Docs"
description: "Írjon egy Service Bus üzenetkezelési sorokat használó C# konzolalkalmazást."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/26/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 02d0ce093bc42cffa4f3993826c61c8aeca4d033
ms.contentlocale: hu-hu
ms.lasthandoff: 07/01/2017

---
# <a name="get-started-with-service-bus-queues"></a>Bevezetés a Service Bus által kezelt üzenetsorok használatába
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>Az oktatóanyag célja
Ez az oktatóanyag a következő lépéseken vezet végig:

1. Service Bus-névtér létrehozása az Azure Portal használatával.
2. Service Bus-üzenetsor létrehozása az Azure Portal használatával.
3. Konzolalkalmazás írása üzenetküldési céllal.
4. Írjon egy konzolalkalmazást az előző lépésben elküldött üzenetek fogadásához.

## <a name="prerequisites"></a>Előfeltételek
1. [Visual Studio 2015 vagy újabb](http://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2017-et használják.
2. Azure-előfizetés.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Névtér létrehozása az Azure Portal használatával
Ha már létrehozta a Service Bus Messaging-névteret, lépjen az [Üzensor létrehozása az Azure Portal használatával](#2-create-a-queue-using-the-azure-portal) szakaszra.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Üzenetsor létrehozása az Azure Portal használatával
Ha a Service Bus-üzenetsort már létrehozta, ugorjon az [Üzenetek küldése az üzenetsorba](#3-send-messages-to-the-queue) szakaszra.

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Üzenetek küldése az üzenetsorba
A Visual Studio használatával C# konzolalkalmazást írunk az üzenetek üzenetsorba való küldéséhez.

### <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

Indítsa el a Visual Studiót, majd hozzon létre egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet.

### <a name="add-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag hozzáadása
1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.
2. Kattintson a **Browse** (Tallózás) fülre, és keressen a **Microsoft Azure Service Bus** kifejezésre, majd válassza ki a **WindowsAzure.ServiceBus** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.
   
    ![NuGet-csomag kiválasztása][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Írjon egy kódrészletet egy üzenet küldéséhez az üzenetsorba
1. Adja hozzá az alábbi `using` utasítást a Program.cs fájl elejéhez.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adja hozzá a következő kódot a `Main` metódushoz: A `connectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati karakterláncot, `queueName` értékének pedig az üzenetsor létrehozásakor használt üzenetsornevet.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");

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

    namespace qsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var queueName = "<your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Futtassa a programot, és ellenőrizze az Azure Portalon: kattintson az üzenetsor nevére a névtér **Áttekintés** paneljén. Megjelenik az üzenetsor **Essentials** (Alapok) panelje. Vegye figyelembe, hogy az **Aktív üzenetek száma** értékének ekkor 1-nek kell lennie. Minden alkalommal, amikor a küldő alkalmazást futtatja, és nem kapja meg az üzeneteket, ez az érték 1-el nő. Azt is vegye figyelembe, hogy az üzenetsor jelenlegi mérete minden alkalommal növekszik, amikor az alkalmazás egy üzenetet ad hozzá az üzenetsorhoz.
   
      ![Üzenet mérete][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Üzenet fogadása az üzenetsorból

1. Az imént elküldött üzenetek fogadásához hozzon létre egy új konzolalkalmazást, majd vegyen fel egy hivatkozást a Service Bus NuGet-csomagjába, hasonlóan az előző küldő alkalmazáshoz.
2. Adja hozzá az alábbi `using` utasítást a Program.cs fájl elejéhez.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adja hozzá a következő kódot a `Main` metódushoz: A `connectionString` változó értékének állítsa be a névtér létrehozásakor kapott kapcsolati karakterláncot, a `queueName` értékének pedig az üzenetsor létrehozásakor használt üzenetsornevet.
   
    ```csharp
    var connectionString = "<your connection string>";
    var queueName = "<your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
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
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var queueName = "<your queue name>";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
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
4. Futtassa a programot, majd ellenőrizze ismét a portálon. Figyelje meg, hogy az **Active Message Count** (Aktív üzenetek száma) és a **Current** (Jelenlegi) értéke most 0.
   
    ![Várólista hossza][queue-message-receive]

Gratulálunk! Ezzel létrehozott egy üzenetsort, illetve küldött és fogadott is üzenetet.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [GitHub-tárunkat, ahol további példákat talál](https://github.com/Azure/azure-service-bus/tree/master/samples), amelyek a Service Bus üzenetkezelési szolgáltatásának néhány speciális funkcióját mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

