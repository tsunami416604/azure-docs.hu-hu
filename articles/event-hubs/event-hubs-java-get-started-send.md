---
title: Események küldése vagy fogadása az Azure Event Hubs-tól Java használatával (örökölt)
description: Ez a cikk egy forgatókönyvet egy Java-alkalmazás létrehozásáról, amely eseményeket küld/fogad az Azure Event Hubs-ba a régi azure-eventhubs csomag használatával.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 2c9baa4c0e048419ece09b954cee1af21b1f0cc1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77158009"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>A Java használatával eseményeket küldhet vagy fogadhat az Azure Event Hubs-ból (azure-eventhubs)

Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontból az **azure-eventhubs** Java-csomag használatával.

> [!WARNING]
> Ez a rövid útmutató a régi **azure-eventhubs** és **az azure-eventhubs-eph** csomagokat használja. A legújabb **azure-messaging-eventhubs** csomagot használó rövid útmutatóért [lásd: Események küldése és fogadása az azure-messaging-eventhubs használatával című témakörben.](get-started-java-send-v2.md) Ha át szeretné helyezni az alkalmazást a régi csomag ról az újra, olvassa el a Útmutató az [azure-eventhubs-ról az azure-messaging-eventhubs-ra való áttéréshez című útmutatót.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md) 


## <a name="prerequisites"></a>Előfeltételek

Ha most írja be az Azure Event Hubs, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót végezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- A Java fejlesztési környezetben. Ez a rövid útmutató [az Eclipse](https://www.eclipse.org/)-t használja.
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az eseményközpont hozzáférési kulcsának értékét a cikk utasításainak követésével kapja meg: [Kapcsolati karakterlánc beírása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató ban a később írt kód hozzáférési kulcsát használhatja. Az alapértelmezett kulcsnév: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre egy Java-alkalmazást egy eseményközpont küldéséhez. 

> [!NOTE]
> A rövid útmutatót mintaként letöltheti a [GitHubról](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Cserélje le az `EventHubConnectionString` és `EventHubName` sztringeket a saját eseményközpontja értékeire, majd futtassa a mintát. Másik lehetőségként kövesse az ebben a rövid útmutatóban leírt lépéseket a saját létrehozásához.

### <a name="add-reference-to-azure-event-hubs-library"></a>Hivatkozás hozzáadása az Azure Event Hubs-tárhoz

Az Event Hubs Java ügyfélkönyvtára a [Maven központi adattárból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)használható a Maven-projektekben. A tárra a Maven projektfájlban található következő függőségi deklaráció használatával hivatkozhat:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

A különböző típusú buildkörnyezetek esetén explicit módon beszerezheti a legújabb megjelent JAR-fájlokat a [Maven központi adattárból.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)  

Egyszerű eseményközzétevő esetén importálja a *com.microsoft.azure.eventhubs* csomagot az Event Hubs ügyfélosztályokhoz és a *com.microsoft.azure.servicebus* csomagot a segédprogram-osztályokhoz, például az Azure Service Bus üzenetküldő ügyféllel megosztott gyakori kivételekhez. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Adjon hozzá `SimpleSend`egy nevű osztályt, és adja hozzá a következő kódot az osztályhoz:

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

A ConnectionStringBuilder osztály segítségével létrehozhat egy kapcsolati karakterlánc-értéket az Event Hubs ügyfélpéldánynak való áthárításhoz. Cserélje le a helyőrzőket a névtér és az eseményközpont létrehozásakor kapott értékekre:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Kód írása események küldéséhez

Hozzon létre egy egyes számú eseményt egy karakterlánc UTF-8 bájt kódolássá alakításával. Ezután hozzon létre egy új Event Hubs ügyfélpéldányt a kapcsolati karakterláncból, és küldje el az üzenetet:   

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

Építse fel és futtassa a programot, és győződjön meg arról, hogy nincsenek hibák.

Gratulálunk! Üzeneteket küldött egy eseményközpontba.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Függelék: Az üzenetek messagehub-partíciókra való átirányítása

Mielőtt a fogyasztók lekérik az üzeneteket, azokat először a közzétevőknek kell közzétenniük a partíciókon. Amikor az üzeneteket a com.microsoft.azure.eventhubs.EventHubClient objektum sendSync() metódusával szinkron módon teszik közzé az eseményközpontban, az üzenet elküldhető egy adott partícióra, vagy az összes elérhető partícióra kerekítéses módon terjeszthető. attól függően, hogy a partíciókulcs meg van-e adva vagy sem.

Ha meg van adva egy partíciókulcsot jelző karakterlánc, a rendszer kivonatolta a kulcsot annak meghatározásához, hogy melyik partícióra küldje az eseményt.

Ha a partíciókulcs nincs beállítva, az üzenetek az összes elérhető partícióra kerekítve jelennek meg

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
Az oktatóanyagban található kód a [GitHubOn található EventProcessorSample kódon](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)alapul, amelyet megvizsgálhat a teljes működő alkalmazás megtekintéséhez.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Üzenetek fogadása az EventProcessorHost használatával Javában

**Az EventProcessorHost** egy Java-osztály, amely leegyszerűsíti az események fogadását az Event Hubs-ból az állandó ellenőrzőpontok és az ilyen eseményközpontoktól érkező párhuzamos fogadások kezelésével. Az EventProcessorHost használatával feloszthatja az eseményeket több fogadóra, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az EventProcessorHost egyetlen fogadóhoz.

### <a name="create-a-storage-account"></a>Create a storage account

Az EventProcessorHost használatához [Azure Storage-fiókkal][Azure Storage-fiókkal] kell rendelkeznie:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és válassza az **erőforrás létrehozása** a képernyő bal oldalán lehetőséget.
2. Válassza a **Storage (Tárolás)** lehetőséget, majd a **Tárfiók lehetőséget.** A **Tárfiók létrehozása** ablakban adja meg a tárfiók nevét. Töltse ki a többi mezőt, jelölje ki a kívánt területet, majd válassza a **Létrehozás gombot.**
   
    ![Tárfiók létrehozása az Azure Portalon](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Jelölje ki az újonnan létrehozott tárfiókot, majd válassza **a Hozzáférési kulcsok**lehetőséget:
   
    ![A hozzáférési kulcsok beszereznie az Azure Portalon](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Másolja a key1 értéket egy ideiglenes helyre. Az oktatóanyag későbbi részében használni fogja.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Java-projekt létrehozása az EventProcessor Hosttal

Az Event Hubs Java ügyfélkönyvtára a [Maven központi adattárból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)használható Maven-projektekben, és a Maven projektfájlban található következő függőségi deklaráció használatával hivatkozhat: 

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

A különböző típusú buildkörnyezetek esetén explicit módon beszerezheti a legújabb megjelent JAR-fájlokat a [Maven központi adattárból.](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)

1. A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály `ErrorNotificationHandler`neve .     
   
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
2. A következő kóddal hozzon létre egy `EventProcessorSample` nevű új osztályt. Cserélje le a helyőrzőket az eseményközpont és a tárfiók létrehozásakor használt értékekre:
   
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
3. Hozzon létre `EventProcessor`még egy osztályt, amelyet a következő kóddal hozzon létre:
   
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

Ez az oktatóprogram az EventProcessorHost egyetlen példányát használja. Az átviteli hang növelése érdekében azt javasoljuk, hogy az EventProcessorHost több példányát futtassa, lehetőleg külön gépeken.  Redundanciát is biztosít. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló neveket adni az EventProcessorHost példányoknak.

### <a name="publishing-messages-to-eventhub"></a>Üzenetek közzététele az EventHubon

Mielőtt a fogyasztók lekérik az üzeneteket, azokat először a közzétevőknek kell közzétenniük a partíciókon. Érdemes megjegyezni, hogy ha az üzeneteket a com.microsoft.azure.eventhubs.EventHubs.EventHubClient objektum sendSync() metódusával szinkron módon teszik közzé az event hubon, az üzenet elküldhető egy adott partícióra, vagy terjeszthető az összes elérhető partícióra. ciklikus multiplexelésa attól függően, hogy a partíciókulcs meg van-e adva vagy sem.

Ha meg van adva egy partíciókulcsot jelző karakterlánc, a rendszer kivonatolta a kulcsot annak meghatározásához, hogy melyik partícióra küldje az eseményt.

Ha a partíciókulcs nincs beállítva, az üzenetek az összes elérhető partícióra kerekítve vannak.

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

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Egyéni CheckpointManager implementálása EventProcessorHost (EPH) számára

Az API egy mechanizmust biztosít az egyéni ellenőrzőpont-kezelő megvalósításához olyan esetekben, amikor az alapértelmezett megvalósítás nem kompatibilis a használati példásokkal.

Az alapértelmezett ellenőrzőpont-kezelő blobstorage-t használ, de ha felülírja az EPH által használt ellenőrzőpont-kezelőt a saját implementációjával, bármelyik tárolót használhatja, amely et az ellenőrzőpont-kezelő implementációjának biztonsági támogatásához szeretné használni.

Hozzon létre egy osztályt, amely megvalósítja a com.microsoft.azure.eventprocessorhost.ICheckpointManager felületet

Az ellenőrzőpont-kezelő egyéni implementációjának használata (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

A megvalósításon belül felülbírálhatja az alapértelmezett ellenőrzőpont-kezelési mechanizmust, és saját adattár (például SQL Server, CosmosDB és Azure Cache for Redis) alapján valósíthatja meg saját ellenőrzőpontjainkat. Azt javasoljuk, hogy az ellenőrzőpont-kezelő implementációjának biztonsági támogatásához használt tároló minden Olyan EPH-példány számára elérhető, amely a fogyasztói csoport eseményeit dolgozza fel.

A környezetében elérhető adattárait használhatja.

A com.microsoft.azure.eventprocessorhost.EventProcessorHost osztály két konstruktort biztosít, amelyek lehetővé teszik az EventProcessorHost ellenőrzőpont-kezelőjének felülbírálásához.


## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Az Azure Event Hubs funkciói és terminológiája](event-hubs-features.md)
- [Event Hubs – gyakori kérdések](event-hubs-faq.md)

