---
title: "Események fogadása az Azure Event Hubs Java használatával |} Microsoft Docs"
description: "Első lépések fogadását az Event Hubs Java használatával"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 94b56fdd1ad350d861b27644c6bedcc59e1cefb0
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Események fogadása az Azure Event Hubs Java használatával


## <a name="introduction"></a>Bevezetés
Az Event Hubs egy kiválóan méretezhető fogadórendszer, amely is több millió eseményt másodpercenként, az alkalmazás engedélyezése feldolgozni, és elemezze a nagy mennyiségű adatot a csatlakoztatott eszközök és alkalmazások által létrehozott. Az adatoknak az Event Hubs szolgáltatásban való összegyűjtését követően, az adatokat átalakíthatja és tárolhatja bármilyen valós idejű elemzési szolgáltató vagy tárolási fürt használatával.

További információkért lásd: a [Event Hubs – áttekintés][Event Hubs overview].

Ez az oktatóanyag bemutatja, hogyan használja a Java nyelven írt konzolalkalmazással eseményközpontnak események fogadásához.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez szüksége van a következő előfeltételek teljesülését:

* A Java-fejlesztőkörnyezet. Ebben az oktatóanyagban feltételezzük, hogy [Eclipse](https://www.eclipse.org/).
* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes fiókot. További információkért lásd: <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Ingyenes Azure-fiók létrehozása</a>.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Üzenetek fogadása az EventProcessorHost használatával Javában

**EventProcessorHost** egy Java-osztály, amely leegyszerűsíti az események fogadását az Event Hubs kezeli az állandó ellenőrzőpontokat és párhuzamos fogadásokat az adott Event hubs Eseményközpontokból. EventProcessorHost használ, akkor is feloszthatja az eseményeket több fogadóra, akkor is, ha különböző csomópontokon üzemelnek. Ez a példa bemutatja, hogyan használható az EventProcessorHost egyetlen fogadóhoz.

### <a name="create-a-storage-account"></a>Create a storage account
EventProcessorHost használatához rendelkeznie kell egy [Azure Storage-fiók][Azure Storage account]:

1. Jelentkezzen be a [Azure-portálon][Azure portal], és kattintson a **+ új** a képernyő bal oldalán.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre. A **Tárfiókok létrehozása** panelen írja be a tárfiók nevét. Fejezze be a mezőket, válassza ki a kívánt régiót, és kattintson **létrehozása**.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Kattintson az imént létrehozott tárfiókra, majd a **Manage Access Keys** (Elérési kulcsok kezelése) lehetőségre:
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Másolja az elsődleges elérési kulcsot egy ideiglenes helyre, az oktatóanyag későbbi részében használni.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Java-projekt létrehozása az EventProcessor Hosttal
Az Event Hubs Java ügyfélkódtár a Maven-projektek használható a [Maven központi tárházban][Maven Package], és a következő függőségi nyilatkozat az Maven project fájl használatával lehet hivatkozni:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```

A különböző típusú buildkörnyezeteket, explicit módon beszerezheti a legfrissebb kiadott JAR fájlok a [Maven központi tárházban] [ Maven Package] vagy [a kiadási terjesztési pontot a Githubon](https://github.com/Azure/azure-event-hubs/releases).  

1. A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Az osztály `ErrorNotificationHandler`.     
   
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
2. A következő kóddal hozzon létre egy `EventProcessor` nevű új osztályt.
   
    ```java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;
   
    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;
   
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }
   
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
   
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }
   
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
   
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```
3. Hozzon létre egy további osztályt `EventProcessorSample`, az alábbi kód használatával.
   
    ```java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;
   
    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";
   
            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
   
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
   
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
   
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }
   
            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
   
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
   
            System.out.println("End of sample");
        }
    }
    ```
4. Cserélje le a következő mezőket a event hub és a tárolási fiók létrehozásakor használt értékek.
   
    ```java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
   
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
   
    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [!NOTE]
> Ez az oktatóprogram az EventProcessorHost egyetlen példányát használja. Átviteli sebesség növelése érdekében ajánlott, hogy futtatja az EventProcessorHost, több példánya lehetőleg külön gépeken.  Ez biztosítja a redundanciát is. Ilyen esetekben a különböző példányok automatikusan koordinálnak egymással a fogadott események terhelésének kiegyenlítéséhez. Ha több fogadóval szeretné feldolgoztatni az *összes* eseményt, a **ConsumerGroup** szolgáltatást kell használnia. Ha több gépről fogad eseményeket, célszerű lehet az azokat futtató gépeken (vagy szerepkörökön) alapuló neveket adni az EventProcessorHost példányoknak.
> 
> 

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
