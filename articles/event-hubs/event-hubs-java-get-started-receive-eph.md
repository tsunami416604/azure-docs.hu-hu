---
title: Események fogadása az Azure Event Hubs Java használatával |} A Microsoft Docs
description: Fogadása az Event Hubs Java használatával – első lépések
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 08/12/2018
ms.author: shvija
ms.openlocfilehash: 486dca6c4cc98b660e7d824b6f0646c06013011e
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746783"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Események fogadása az Azure Event Hubs Java használatával

Event Hubs szolgáltatás egy kiválóan méretezhető fogadórendszer, amely képes másodpercenként több millió feldolgozására, ezáltal az alkalmazások feldolgozásához, és a csatlakoztatott eszközök és alkalmazások által létrehozott hatalmas adatmennyiségek elemzését. Az adatoknak az Event Hubs szolgáltatásban való összegyűjtését követően, az adatokat átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával.

További információkért lásd: a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag bemutatja, hogyan események fogadása az event hubba a Java nyelven írt konzolalkalmazással.

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elvégzéséhez szüksége van a következő előfeltételek vonatkoznak:

* A Java fejlesztési környezet. Ebben az oktatóanyagban feltételezzük, hogy [Eclipse](https://www.eclipse.org/).
* Aktív Azure-fiók. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot][] a feladatok megkezdése előtt.

A jelen oktatóanyagban szereplő kód alapján a [EventProcessorSample kódja a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), amely a teljes megtekintéséhez tekintse meg működő alkalmazást.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Üzenetek fogadása az EventProcessorHost használatával Javában

**EventProcessorHost** egy Java-osztály, amely leegyszerűsíti az események fogadását az Event hubsból, mivel kezeli az állandó ellenőrzőpontokat és a párhuzamos fogadásokat az adott Event hubs Eseményközpontokból. EventProcessorHost használatával, akkor is feloszthatja az eseményeket több fogadóra, még akkor is, ha ezek különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az EventProcessorHost egyetlen fogadóhoz.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az EventProcessorHost használatához rendelkeznie kell egy [Azure Storage-fiók][Azure Storage account]:

1. Jelentkezzen be a [az Azure portal][Azure portal], és kattintson a **+ erőforrás létrehozása** a képernyő bal oldalán.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre. Az a **storage-fiók létrehozása** ablakban írja be a tárfiók nevét. Fejezze be a mezőket, válassza ki a kívánt régiót, és kattintson **létrehozás**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Kattintson az újonnan létrehozott tárfiókra, és kattintson **Tárelérési kulcsok**:
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Az 1. kulcs-érték másolja egy ideiglenes helyre, az oktatóanyag későbbi részében használni.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Java-projekt létrehozása az EventProcessor Hosttal

A Java ügyféloldali kódtár, az Event Hubs szolgáltatás a Maven-projektekhez érhető el a [Maven központi tárházból][Maven Package], és lehet rá hivatkozni a következő függőségi nyilatkozat belül a Maven használatával soubor projektu. A jelenlegi verzió: 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>1.0.0</version>
</dependency>
```

A különböző típusú összeállítási környezetekhez explicit módon szerezheti be a legutóbb kiadott JAR-fájlokat a [Maven központi tárházból][Maven Package].  

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

Ez az oktatóprogram az EventProcessorHost egyetlen példányát használja. Átviteli sebesség növelése érdekében ajánlott, hogy Ön több példányának futtatása EventProcessorHost, lehetőleg külön gépeken található.  Ez biztosítja a redundanciát is. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló neveket adni az EventProcessorHost példányoknak.

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md)
* [Event Hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[ingyenes fiókot]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio