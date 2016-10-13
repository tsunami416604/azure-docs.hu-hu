<properties
    pageTitle="Bevezetés a Service Bus által kezelt üzenetsorok használatába | Microsoft Azure"
    description="C# konzolalkalmazás létrehozása Service Bus üzenetkezelés számára"
    services="service-bus-messaging"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/23/2016"
    ms.author="jotaub;sethm"/>


# Bevezetés a Service Bus által kezelt üzenetsorok használatába

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## Az oktatóanyag célja

A jelen oktatóanyagban a következő műveleteket fogjuk végrehajtani:

1. Service Bus-névtér létrehozása az Azure Portal használatával.

2. Service Bus-üzenetsor létrehozása az Azure Portal használatával.

3. Konzolalkalmazás írása üzenetküldési céllal.

4. Konzolalkalmazás írása üzenetfogadási céllal.

## Előfeltételek

1. [Visual Studio 2013 vagy Visual Studio 2015](http://www.visualstudio.com). A jelen oktatóanyag példái a Visual Studio 2015-öt használják.

2. Azure-előfizetés.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1. Névtér létrehozása az Azure Portal használatával

Ha a Service Bus-névteret már létrehozta, ugorjon az [Üzenetsor létrehozása az Azure Portal használatával](#2-create-a-queue-using-the-azure-portal) szakaszra.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2. Üzenetsor létrehozása az Azure Portal használatával

Ha a Service Bus-üzenetsort már létrehozta, ugorjon az [Üzenetek küldése az üzenetsorba](#3-send-messages-to-the-queue) szakaszra.

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## 3. Üzenetek küldése az üzenetsorba

A Visual Studio használatával C# konzolalkalmazást írunk az üzenetek üzenetsorba való küldéséhez.

### Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studiót, majd hozzon létre egy új konzolalkalmazást.

### A Service Bus NuGet-csomag hozzáadása

1. Kattintson a jobb gombbal az újonnan létrehozott projektre, és válassza a **Manage Nuget Packages** (NuGet-csomagok kezelése) lehetőséget.

2. Kattintson a **Browse** (Tallózás) fülre, és keressen a „Microsoft Azure Service Bus” kifejezésre, majd válassza ki a **Microsoft Azure Service Bus** elemet. Kattintson a **Telepítés** gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt.

    ![NuGet-csomag kiválasztása][nuget-pkg]

### Írjon egy kódrészletet egy üzenet küldéséhez az üzenetsorba

1. Adja hozzá az alábbi using utasítást a Program.cs fájl elejéhez.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Vegye fel az alábbi kódot a `Main` metódusba, a **connectionString** változó legyen a névtér létrehozásakor beszerzett kapcsolati karakterlánc, a **queueName** pedig legyen az üzenetsor létrehozásakor használt üzenetsornév.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```

    A Program.cs fájlnak így kell kinéznie.

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;

    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";

                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");

                client.Send(message);
            }
        }
    }
    ```
  
3. Futtassa a programot, majd ellenőrizze azt az Azure Portalon. A névtér **Áttekintés** paneljén kattintson az üzenetsor nevére. Vegye figyelembe, hogy az **Aktív üzenetek száma** értékének ekkor 1-nek kell lennie.
    
      ![Üzenetek száma][queue-message]
    
## 4. Üzenet fogadása az üzenetsorból

1. Hozzon létre egy új konzolalkalmazást, majd vegyen fel egy hivatkozást a Service Bus NuGet-csomagjára, hasonlóan az előző küldési alkalmazáshoz.

2. Adja hozzá az alábbi `using` utasítást a Program.cs fájl elejéhez.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Vegye fel az alábbi kódot a `Main` metódusba, a **connectionString** változó legyen a névtér létrehozásakor beszerzett kapcsolati karakterlánc, a **queueName** pedig legyen az üzenetsor létrehozásakor használt üzenetsornév.

    ```
    var connectionString = "";
    var queueName = "samplequeue";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
  
    Console.ReadLine();
    ```

    A Program.cs fájlnak így kell kinéznie:

    ```
    using System;
    using Microsoft.ServiceBus.Messaging;
  
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
  
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
  
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
  
          Console.ReadLine();
        }
      }
    }
    ```
  
4. Futtassa a programot, majd ellenőrizze azt a portálon. Vegye figyelembe, hogy a **Várólista hossza** értékének ekkor 0-nak kell lennie.

    ![Várólista hossza][queue-message-receive]
  
Gratulálunk! Ezzel létrehozott egy üzenetsort, illetve küldött és fogadott is üzenetet.

## Következő lépések

Tekintse meg a [GitHub-tárunkat, ahol további példákat talál](https://github.com/Azure-Samples/azure-servicebus-messaging-samples), amelyek az Azure Service Bus üzenetkezelési szolgáltatásának speciális funkcióit mutatják be.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->

[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples


<!--HONumber=Sep16_HO5-->


