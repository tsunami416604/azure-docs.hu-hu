---
title: Események küldése vagy fogadása az Azure Event Hubs-tól Java használatával (legújabb)
description: Ez a cikk egy forgatókönyvet egy Java-alkalmazás létrehozásáról, amely az Azure Event Hubs-ba/az Azure Event Hubs-ból küld és fogad eseményeket a legújabb azure-messaging-eventhubs csomag használatával.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 29101d0277ff0fb392917f722bcaec3b7e5983b5
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478252"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>A Java használatával eseményeket küldhet vagy fogadhat az Azure Event Hubs-ból (azure-messaging-eventhubs)
Ez a rövid útmutató bemutatja, hogyan küldhet eseményeket egy eseményközpontból az **azure-messaging-eventhubs** Java csomag használatával.

> [!IMPORTANT]
> Ez a rövid útmutató az új **azure-messaging-eventhubs** csomagot használja. A régi **azure-eventhubs** és az **azure-eventhubs-eph** csomagokat használó rövid útmutatóért tekintse meg az [események küldése és fogadása az azure-eventhubs és az azure-eventhubs-eph használatával](event-hubs-java-get-started-send.md)című témakört. 


## <a name="prerequisites"></a>Előfeltételek
Ha most írja be az Azure Event Hubs szolgáltatást, olvassa el [az Eseményközpontok áttekintése című témakört,](event-hubs-about.md) mielőtt ezt a rövid útmutatót elvégezné. 

A rövid útmutató végrehajtásához a következő előfeltételekre van szükség:

- **Microsoft Azure-előfizetés**. Az Azure-szolgáltatások, köztük az Azure Event Hubs használatához előfizetésre van szüksége.  Ha nem rendelkezik meglévő Azure-fiókkal, regisztrálhat egy [ingyenes próbaverzióra,](https://azure.microsoft.com/free/) vagy használhatja az MSDN-előfizetői előnyöket [a fiók létrehozásakor.](https://azure.microsoft.com)
- A Java fejlesztési környezetben. Ez a rövid útmutató [az Eclipse](https://www.eclipse.org/)-t használja. Java Development Kit (JDK) 8-as vagy újabb verzióval szükséges. 
- **Hozzon létre egy Eseményközpontok névterét és egy eseményközpontot.** Az első lépés [az,](https://portal.azure.com) hogy az Azure Portal használatával hozzon létre egy Event Hubs típusú névteret, és szerezze be az alkalmazás által az eseményközponttal való kommunikációhoz szükséges felügyeleti hitelesítő adatokat. Névtér és eseményközpont létrehozásához kövesse a [cikkben](event-hubs-create.md)található eljárást. Ezután az **Event Hubs névtér kapcsolati karakterláncát a** cikk utasításainak követésével kapja [meg: Kapcsolati karakterlánc beszereznie](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). A kapcsolati karakterláncot később használja ebben a rövid útmutatóban.

## <a name="send-events"></a>Események küldése 
Ez a szakasz bemutatja, hogyan hozhat létre egy Java-alkalmazást egy eseményközpont küldéséhez. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Hivatkozás hozzáadása az Azure Event Hubs-tárhoz

Az Event Hubs Java ügyfélkönyvtára a [Maven központi adattárban](https://search.maven.org/search?q=a:azure-messaging-eventhubs)érhető el. A tárra a Maven projektfájlban található következő függőségi deklaráció használatával hivatkozhat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Kód írása az üzenetek eseményközpontba való küldésére

A következő mintában először hozzon létre egy új Maven-projektet egy konzol/felületalkalmazáshoz a kedvenc Java-fejlesztőkörnyezetében. Adjon hozzá `SimpleSend`egy nevű osztályt, és adja hozzá a következő kódot az osztályhoz:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Kapcsolati karakterlánc és eseményközpont
Ez a kód az Event Hubs névtér kapcsolati karakterláncát és az eseményközpont nevét használja egy Eseményközpont-ügyfél létrehozásához. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Eseményközpontok produceri ügyféljének létrehozása 
Ez a kód létrehoz egy termelői ügyfélobjektumot, amely az események létrehozásához/küldéséhez szolgál az eseményközpontba. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Események kötegének előkészítése
Ez a kód események kötegét készíti elő. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Események kötegének küldése az eseményközpontba
Ez a kód az előző lépésben elkészített események kötegét küldi el az eseményközpontba. A következő kódblokkok a küldési műveletben. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Bezárás és tisztítás
Ez a kód bezárja a gyártót. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Teljes kód az események küldéséhez
Itt van a teljes kódot, hogy az eseményeket az eseményközpontba küld. 

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

Építse fel a programot, és győződjön meg arról, hogy nincsenek hibák. Ezt a programot a vevőprogram futtatása után futtatja. 

## <a name="receive-events"></a>Események fogadása
Az oktatóanyagban található kód a [GitHubon található EventProcessorClient mintán](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java)alapul, amelyet megvizsgálhat a teljes működő alkalmazás megtekintéséhez.

> [!NOTE]
> Ha az Azure Stack Hubon fut, akkor előfordulhat, hogy a storage blob SDK egy másik verzióját támogatja, mint az Azure-ban általában elérhető. Ha például az [Azure Stack Hub 2002-es verzióján](https://docs.microsoft.com/azure-stack/user/event-hubs-overview)fut, a Storage szolgáltatás legmagasabb elérhető verziója a 2017-11-09-es verzió. Ebben az esetben az ebben a szakaszban ismertetett lépések mellett kódot is hozzá kell adnia a Storage service API 2017-11-09-es verziójának célzásához. Egy adott Storage API-verzió célzásával kapcsolatos példát tekintse meg [ezt a mintát a GitHubon.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java) Az Azure Stack Hubon támogatott Azure Storage-szolgáltatásverzióiról az [Azure Stack Hub storage: Különbségek és szempontok](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)című további információkért tekintse meg.

### <a name="create-a-java-project"></a>Java-projekt létrehozása

Az Event Hubs Java ügyfélkönyvtára a [Maven központi adattárból](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)használható Maven-projektekben, és a Maven projektfájlban található következő függőségi deklaráció használatával hivatkozhat: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. A következő kóddal hozzon létre egy `Receiver` nevű új osztályt. Cserélje le a helyőrzőket az eseményközpont és a tárfiók létrehozásakor használt értékekre:
   
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
                     .checkpointStore(new InMemoryCheckpointStore())
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
    
2. Töltse le az **InMemoryCheckpointStore.java** fájlt a [GitHubról](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/InMemoryCheckpointStore.java), és adja hozzá a projekthez. 
3. Építse fel a programot, és győződjön meg arról, hogy nincsenek hibák. 

## <a name="run-the-applications"></a>Az alkalmazások futtatása
1. Először futtassa a **fogadóalkalmazást.**
1. Ezután futtassa a **feladóalkalmazást.** 
1. A **fogadó** alkalmazás ablakban ellenőrizze, hogy a feladó által közzétett események jelennek-e meg.
1. Az alkalmazás leállításához nyomja le az **ENTER billentyűt** a vevőalkalmazás ablakában. 

## <a name="next-steps"></a>További lépések
Tekintse meg [a Java SDK-mintákat a GitHubon](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

