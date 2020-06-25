---
title: Azure Event Hubs-beli események küldése vagy fogadása a Javával (legújabb)
description: Ez a cikk bemutatja, hogyan hozhat létre olyan Java-alkalmazást, amely az Azure-Event Hubs legújabb Azure-Messaging-eventhubs csomag használatával küld/fogad eseményeket.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 69c994eed0907c8bb46f47016769e2078432ee99
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85320069"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Az Azure Event Hubs (Azure-Messaging-eventhubs) eseményeinek küldése vagy fogadása a Java használatával
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket az Event hub eseményeiről és fogadhat az **Azure-Messaging-eventhubs Java-** csomag használatával.

> [!IMPORTANT]
> Ez a rövid útmutató az új **Azure-Messaging-eventhubs** csomagot használja. A régi **Azure-eventhubs** és **Azure-eventhubs-EF** csomagokat használó gyors útmutató: [események küldése és fogadása az Azure-eventhubs és az Azure-eventhubs-EF használatával](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Előfeltételek
Ha még nem ismeri az Azure Event Hubsét, a rövid útmutató elvégzése előtt tekintse meg a [Event Hubs áttekintése](event-hubs-about.md) című témakört. 

A rövid útmutató elvégzéséhez a következő előfeltételek szükségesek:

- **Microsoft Azure előfizetés**. Az Azure-szolgáltatások, például az Azure Event Hubs használatához előfizetésre van szükség.  Ha még nem rendelkezik Azure-fiókkal, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/free/) , vagy a [fiók létrehozásakor](https://azure.microsoft.com)használhatja az MSDN-előfizetői előnyeit.
- Java-fejlesztési környezet. Ez a rövid útmutató az [Eclipse](https://www.eclipse.org/)-t használja. Szükség van a Java Development Kit (JDK) 8-as vagy újabb verziójára. 
- **Hozzon létre egy Event Hubs névteret és egy Event hubot**. Első lépésként a [Azure Portal](https://portal.azure.com) használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be azokat a felügyeleti hitelesítő adatokat, amelyekre az alkalmazásnak szüksége van az Event hub-vel való kommunikációhoz. A névtér és az Event hub létrehozásához kövesse az [ebben a cikkben](event-hubs-create.md)ismertetett eljárást. Ezután szerezze be a **Event Hubs névtérhez tartozó kapcsolatok karakterláncot** a cikk utasításait követve: a [kapcsolatok karakterláncának beolvasása](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A rövid útmutató későbbi részében használja a kapcsolatok karakterláncát.

## <a name="send-events"></a>Események küldése 
Ebből a szakaszból megtudhatja, hogyan hozhat létre egy Java-alkalmazást az Event hub eseményeinek elküldéséhez. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Hivatkozás hozzáadása az Azure Event Hubs Library-hoz

A Event Hubs Java-ügyféloldali könyvtára a [Maven Central adattárában](https://search.maven.org/search?q=a:azure-messaging-eventhubs)érhető el. Ezt a kódtárat a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Vegyen fel egy nevű osztályt `Sender` , és adja hozzá a következő kódot a osztályhoz:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Kapcsolati sztring és Event hub
Ez a kód a kapcsolati karakterláncot használja a Event Hubs névtérhez és az Event hub nevéhez Event Hubs ügyfél létrehozásához. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Event Hubs gyártói ügyfél létrehozása 
Ez a kód létrehoz egy termelői ügyféltanúsítványt, amely az Event hub-hoz tartozó események előállítására és küldésére szolgál. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Események kötegének előkészítése
Ez a kód előkészíti az események kötegét. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Események kötegének elküldése az Event hub-nak
Ez a kód az előző lépésben az Event hub-ba előkészített események kötegét küldi el. A küldési művelet következő kódrészletei. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Bezárás és törlés
Ez a kód lezárja a gyártót. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Az események küldésére szolgáló kód végrehajtása
Itt látható a teljes kód, amely az Event hub eseményeinek küldésére szolgál. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Hozza létre a programot, és ellenőrizze, hogy nincsenek-e hibák. Ezt a programot a fogadó program futtatása után fogja futtatni. 

## <a name="receive-events"></a>Események fogadása
Az oktatóanyagban szereplő kód a [githubon található EventProcessorClient-mintán](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java)alapul, amelyet megvizsgálva megtekintheti a teljes működő alkalmazást.

> [!NOTE]
> Ha Azure Stack hub-on fut, akkor a platform a Storage blob SDK egy másik verzióját is támogatja, mint az Azure-ban általában elérhető. Ha például [Azure stack Hub 2002-es verzióján](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)fut, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2017-11-09-es verzió. Ebben az esetben a szakasz következő lépésein kívül kódot is hozzá kell adnia a Storage szolgáltatás API 2017-11-09-es verziójának célzásához. Az adott tárolási API-verzió célzásának példáját a [githubon](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)tekintheti meg. Az Azure Stack hub által támogatott Azure Storage szolgáltatás verziójával kapcsolatos további információkért tekintse meg [Azure stack hub Storage: különbségek és megfontolások](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)című témakört.

### <a name="create-a-java-project"></a>Java-projekt létrehozása

A Event Hubs Java-ügyféloldali könyvtára a Maven [központi tárházból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)származó Maven-projektekben használható, és a következő függőségi deklarációval hivatkozhat a Maven-projektfájl használatával: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. A következő kóddal hozzon létre egy `Receiver` nevű új osztályt. Cserélje le a helyőrzőket az Event hub és a Storage-fiók létrehozásakor használt értékekre:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new SampleCheckpointStore())
                     .buildEventProcessorClient();
    
             System.out.println("Starting event processor");
             eventProcessorClient.start();
    
             System.out.println("Press enter to stop.");
             System.in.read();
    
             System.out.println("Stopping event processor");
             eventProcessorClient.stop();
             System.out.println("Event processor stopped.");
    
             System.out.println("Exiting process");
         }
     }
    ```
    
2. Töltse le a **SampleCheckpointStore. Java** fájlt a [githubról](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java), és adja hozzá a projekthez. 
3. Hozza létre a programot, és ellenőrizze, hogy nincsenek-e hibák. 

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Először futtassa a **fogadó** alkalmazást.
1. Ezután futtassa a **küldő** alkalmazást. 
1. A **fogadó** alkalmazás ablakban ellenőrizze, hogy láthatók-e a küldő alkalmazás által közzétett események.
1. Az alkalmazás leállításához nyomja le az **ENTER** billentyűt a fogadó alkalmazás ablakban. 

## <a name="next-steps"></a>További lépések
Tekintse [meg a Java SDK-mintákat a githubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

