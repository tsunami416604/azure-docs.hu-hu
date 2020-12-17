---
title: Azure Service Bus Queues használata Javával (Azure-Messaging-servicebus)
description: Ebből az oktatóanyagból megtudhatja, hogyan használható a Java egy Azure Service Bus üzenetsor üzeneteinek üzenetküldésére és fogadására. Használja az új Azure-Messaging-servicebus csomagot.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d95c96e76a3463a77cc64234a909cc1e3d093837
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630224"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Üzenetek küldése és fogadása Azure Service Bus várólistákból (Java)
Ebben a rövid útmutatóban egy Java-alkalmazást fog létrehozni, amely üzeneteket küld egy Azure Service Bus üzenetsor üzeneteit, és fogadja az üzeneteket. 

> [!IMPORTANT]
> Ez a rövid útmutató az új Azure-Messaging-servicebus csomagot használja. A régi Azure-servicebus csomagot használó gyors útmutatóért lásd: [üzenetek küldése és fogadása az Azure-servicebus használatával](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés. Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Aktiválhatja MSDN- [előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) , vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Ha nem rendelkezik várólistával, hogy működjön a szolgáltatással, a várólista létrehozásához kövesse az [Azure Portal használata Service Bus üzenetsor létrehozásához](service-bus-quickstart-portal.md) című cikket. Jegyezze **fel a Service Bus névtér és a létrehozott** **várólista** nevét.
- Telepítse [a Javához készült Azure SDK][Azure SDK for Java]-t. Ha az Eclipse-t használja, akkor telepítheti a Javához készült Azure SDK-t tartalmazó [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] . Ezután hozzáadhatja a **Javához készült Microsoft Azure kódtárakat** a projekthez. Ha a IntelliJ-t használja, tekintse meg [a Azure Toolkit for IntelliJ telepítését](/azure/developer/java/toolkit-for-intellij/installation)ismertető témakört. 


## <a name="send-messages-to-a-queue"></a>Üzenetek küldése egy üzenetsorba
Ebben a szakaszban létre fog hozni egy Java-konzol projektet, és hozzá kell adnia egy kódot, amely üzeneteket küld a korábban létrehozott várólistába. 

### <a name="create-a-java-console-project"></a>Java-konzol projekt létrehozása
Hozzon létre egy Java-projektet az Eclipse vagy egy tetszőleges eszköz használatával. 

### <a name="configure-your-application-to-use-service-bus"></a>Az alkalmazás konfigurálása Service Bus használatára
Azure Service Bus könyvtárra mutató hivatkozás hozzáadása. A Service Bus Java-ügyféloldali könyvtára a [Maven Central adattárában](https://search.maven.org/search?q=a:azure-messaging-servicebus)érhető el. Ezt a kódtárat a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Kód hozzáadása az üzenetek várólistába küldéséhez
1. Adja hozzá a következő `import` utasításokat a Java-fájl témaköréhez. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. A osztályban adja meg a kapcsolatok karakterláncának és a várólista nevének a megtartásához szükséges változókat az alább látható módon: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Cserélje le a `<NAMESPACE CONNECTION STRING>` karakterláncot a Service Bus névtérhez tartozó kapcsolódási sztringre. És cserélje le a `<QUEUE NAME>` nevet a várólista nevére.
3. Adjon hozzá egy nevű metódust `sendMessage` a osztályhoz, hogy egy üzenetet küldjön a várólistára. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Az üzenetek listájának létrehozásához vegyen fel egy nevű metódust `createMessages` a osztályban. Ezeket az üzeneteket általában az alkalmazás különböző részeiről szerezheti be. Itt a mintaadatok listáját fogjuk létrehozni.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Adjon hozzá egy metódus nevű metódust `sendMessageBatch` az üzenetek küldéséhez a létrehozott várólistára. Ez a metódus létrehoz egy- `ServiceBusSenderClient` t a várólistához, meghívja a `createMessages` metódust az üzenetek listájának lekéréséhez, előkészíti egy vagy több köteget, és elküldi a kötegeket a várólistába. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>Üzenetek fogadása egy várólistából
Ebben a szakaszban kódot fog hozzáadni az üzenetek várólistából való lekéréséhez. 

1. Adjon hozzá egy nevű metódust `receiveMessages` az üzenetek fogadásához a várólistából. Ez a metódus létrehoz egy `ServiceBusProcessorClient` -t az üzenetsor számára egy kezelő megadásával az üzenetek feldolgozásához, és egy másikat a hibák kezeléséhez. Ezután elindítja a processzort, megvárja a pár másodpercig, kinyomtatja a fogadott üzeneteket, majd leállítja és bezárja a processzort.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }    
    ```
2. Frissítse a `main` metódust, hogy meghívja `sendMessage` , `sendMessageBatch` , és `receiveMessages` metódusokat, és dobja `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Az alkalmazás futtatása
Az alkalmazás futtatásakor a konzol ablakban a következő üzenetek jelennek meg. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

A Azure Portal Service Bus névterének **Áttekintés** lapján láthatók a **bejövő** és a **kimenő** üzenetek száma. Előfordulhat, hogy várnia kell egy percet, majd frissítenie kell a lapot a legfrissebb értékek megtekintéséhez. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Bejövő és kimenő üzenetek száma" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Válassza ki a várólistát ezen az **áttekintő** lapon, és navigáljon a **Service Bus üzenetsor** lapra. Ezen a lapon a **bejövő** és a **kimenő** üzenetek száma is látható. Más információk is megjelennek, például a várólista **jelenlegi mérete** , a **maximális méret**, az **aktív üzenetek száma** és így tovább. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Üzenetsor részletei" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>További lépések
Tekintse meg a következő dokumentációt és mintákat:

- [A Javához készült ügyféloldali kódtár Azure Service Bus – readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Példák a GitHubon](https://docs.microsoft.com/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Java API-referenciák](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
