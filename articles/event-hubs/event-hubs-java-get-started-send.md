---
title: Java – Azure Event Hubs használatával események küldéséhez és fogadásához |} A Microsoft Docs
description: Ez a cikk egy Java-alkalmazás, amely elküldi az eseményeket az Azure Event Hubs létrehozásának részletes ismertetése.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 0487cac6a0cf7d37befdf0d7cfab33ad6a62cf7f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679638"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-java"></a>Események küldése vagy események fogadása az Azure Event Hubs Java használatával

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Az oktatóanyag bemutatja, hogyan küldhet eseményeket vagy események fogadása az event hubs Java-alkalmazások létrehozásához. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Vagy létrehozhatja saját megoldását is az oktatóanyag lépései alapján.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

- Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a feladatok megkezdése előtt.
- A Java fejlesztési környezet. Ebben az oktatóanyagban [Eclipse](https://www.eclipse.org/).
- **Event Hubs-névtér és eseményközpont létrehozása**. Első lépésként az [Azure Portalon](https://portal.azure.com) hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazása és az eseményközpont közötti kommunikációhoz szükséges felügyeleti hitelesítő adatokat. A névtér és eseményközpont létrehozásához hajtsa végre az eljárást a [Ez a cikk](event-hubs-create.md). Az eseményközpont hozzáférési kulcs értékét, majd kövesse a cikkben szereplő lekéréséhez: [Kapcsolati sztring lekérése](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A hozzáférési kulcsot a kód írása az oktatóanyag későbbi részében fogja használni. Az alapértelmezett nevet, majd: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre egy Java-alkalmazás eseményeket küld egy eseményközpontnak. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Az Azure Event Hubs-erőforrástárhoz hivatkozás hozzáadása

A Java ügyféloldali kódtár, az Event Hubs szolgáltatás a Maven-projektekhez érhető el a [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Ebben a könyvtárban a következő függőségi nyilatkozat használata a Maven-projektfájlból belül lehet hivatkozni:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

A különböző típusú összeállítási környezetekhez explicit módon szerezheti be a legutóbb kiadott JAR-fájlokat a [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Egy egyszerű esemény-közzétevő importálja a *com.microsoft.azure.eventhubs* az Event Hubs ügyfélosztályok csomag és a *com.microsoft.azure.servicebus* például szolgáló segédeszközosztályok csomag gyakori kivételek, amelyek az Azure Service Bus üzenetkezelési ügyfél vannak megosztva. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Adjon hozzá egy osztályt `SimpleSend`, és adja hozzá a következő kódot az osztály:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Kapcsolati karakterlánc létrehozása

A ConnectionStringBuilder osztály használatával hozhat létre egy kapcsolati karakterlánc értékét az Event Hubs-ügyfél példány számára. A helyőrzőket cserélje le a névtér és az eseményközpont létrehozásakor beszerzett:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("speventhubns") 
                .setEventHubName("speventhub")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("2+WMsyyy1XmUtEnRsfOmTTyGasfJgsVjGAOIN20J1Y8=");
```

### <a name="write-code-to-send-events"></a>Események küldése kód írása

Hozzon létre egy egyes számú esemény átalakításával keletkező egy karakterlánc, az UTF-8 bájtos kódolást. Ezután hozzon létre új Event Hubs-ügyfél példány a kapcsolati karakterláncból, és az üzenet elküldéséhez:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Hozhat létre és futtassa a programot, és győződjön meg arról, hogy nincsenek-e hibák.

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>A függelék: Hogyan legyenek átirányítva az üzeneteket az EventHub-partíciók

Üzenetek fogyasztó megkaptuk, mielőtt közzétenni a partíciók először a kiadók által rendelkeznek. Üzeneteket az eseményközpontba szinkron módon történik a metódussal sendSync() a com.microsoft.azure.eventhubs.EventHubClient objektum közzétételét követően sikerült-e az összes rendelkezésre álló partíciókra Ciklikus időszeleteléses módon elosztott vagy egy adott partícióra küldött üzenet attól függően, hogy a partíciós kulcs van megadva, vagy nem.

Ha egy karakterlánc, amely a partíciókulcs meg van adva, a kulcs lesz kivonatolása határozza meg az eseményt, hogy melyik partíciót.

Ha nincs beállítva a partíciókulcsot, ciklikus-robined az összes rendelkezésre álló partíció lesz-e majd üzenetek

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Események fogadása
A jelen oktatóanyagban szereplő kód alapján a [EventProcessorSample kódja a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), amely a teljes megtekintéséhez tekintse meg működő alkalmazást.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Üzenetek fogadása az EventProcessorHost használatával Javában

**EventProcessorHost** egy Java-osztály, amely leegyszerűsíti az események fogadását az Event hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az adott Event hubs Eseményközpontokból. EventProcessorHost használatával, akkor is feloszthatja az eseményeket több fogadóra, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az EventProcessorHost egyetlen fogadóhoz.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az EventProcessorHost használatához rendelkeznie [Azure Storage-fiók] [Azure Storage-fiók]:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és kattintson a **+ erőforrás létrehozása** a képernyő bal oldalán.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre. Az a **storage-fiók létrehozása** ablakban írja be a tárfiók nevét. Fejezze be a mezőket, válassza ki a kívánt régiót, és kattintson **létrehozás**.
   
    ![Storage-fiók létrehozása](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Kattintson az újonnan létrehozott tárfiókra, és kattintson **Tárelérési kulcsok**:
   
    ![Hozzáférési kulcsok lekérése](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    1. kulcs értékét egy ideiglenes helyre másolja. Az oktatóanyag későbbi részében használni fogja.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Java-projekt létrehozása az EventProcessor Hosttal

A Java ügyféloldali kódtár, az Event Hubs szolgáltatás a Maven-projektekhez érhető el a [Maven központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22), és lehet rá hivatkozni a Maven-projektfájlból belül a következő függőségi nyilatkozat használatával: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

A különböző típusú összeállítási környezetekhez explicit módon szerezhet be a legutóbb kiadott JAR-fájlokat a [Maven központi tárházból] [https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22].  

1. A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály neve `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. A következő kóddal hozzon létre egy `EventProcessorSample` nevű új osztályt. A helyőrzőket cserélje le az event hubot és a storage-fiók létrehozásakor használt értékek:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Hozzon létre egy további osztályt nevű `EventProcessor`, a következő kód használatával:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Ez az oktatóprogram az EventProcessorHost egyetlen példányát használja. Átviteli sebesség növelése érdekében azt javasoljuk, hogy Ön több példányának futtatása EventProcessorHost, lehetőleg külön gépeken található.  Redundanciát is biztosít. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló neveket adni az EventProcessorHost példányoknak.

### <a name="publishing-messages-to-eventhub"></a>Az EventHub közzétételi üzenetek

Üzenetek fogyasztó megkaptuk, mielőtt közzétenni a partíciók először a kiadók által rendelkeznek. Érdemes megjegyezni, hogy üzeneteket az eseményközpontba szinkron módon történik a metódussal sendSync() a com.microsoft.azure.eventhubs.EventHubClient objektum közzétételét, az üzenet volt küldhetnek egy megadott partícióra vagy ossza el az összes rendelkezésre álló partíciók Ciklikus időszeleteléses módon attól függően, hogy a partíciós kulcs van megadva, vagy sem.

Ha egy karakterlánc, amely a partíciókulcs meg van adva, a kulcs kivonatolt, hogy az eseményt, hogy melyik partíciót határozza meg.

Ha nincs beállítva a partíciókulcsot, ciklikus-robined az összes rendelkezésre álló partíciók-e majd üzenetek

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Egy egyéni CheckpointManager megvalósítása az EventProcessorHost (EPH)

Az API lehetővé teszi a egyéni ellenőrzőpont-kezelő, ahol az alapértelmezett implementációja, nem kompatibilis a használati forgatókönyvek megvalósításához.

Az alapértelmezett ellenőrzőpont-kezelő használja a blob storage-ba, de ha felülbírálja a saját implementációjához EPH által használt ellenőrzőpont-kezelő, bármely store szeretne készíteni az ellenőrzőpont manager végrehajtására használható.

Hozzon létre egy osztályt, amely megvalósítja a felület com.microsoft.azure.eventprocessorhost.ICheckpointManager

Az ellenőrzőpont-kezelő (com.microsoft.azure.eventprocessorhost.ICheckpointManager) egyéni megvalósítását használni

A megvalósítás belül felülbírálják az alapértelmezett ellenőrzőpont-kezelési mechanizmust, és saját alapuló a saját data store (például az SQL Server, a cosmos DB és az Azure Cache redis) ellenőrzőpontokkal megvalósítása. Azt javasoljuk, hogy a tároló az ellenőrzőpont manager megvalósítási biztonsági érhető-e feldolgozni az eseményeket a felhasználói csoport EPH-példányok.

Használhat bármilyen a környezetében elérhető adattárolót.

A com.microsoft.azure.eventprocessorhost.EventProcessorHost osztály két konstruktorral, amelyek lehetővé teszik az EventProcessorHost az ellenőrzőpont-kezelő felülírását is biztosít.


## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkciók és az Azure Event Hubs terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)

