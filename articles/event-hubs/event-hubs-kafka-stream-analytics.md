---
title: Azure Event Hubs – Apache Kafka események feldolgozása
description: 'Oktatóanyag: Ez a cikk bemutatja, hogyan dolgozhatja fel az Event hub-n keresztül betöltött Kafka-eseményeket Azure Stream Analytics használatával'
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: d7b060a2b35ca41bf87b69be706284174d7b1012
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587158"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Oktatóanyag: Event Hubs-események Apache Kafkaának feldolgozása a stream Analytics használatával 
Ez a cikk bemutatja, hogyan adatok streamelése az Event Hubs Kafka-kompatibilis, és az Azure Stream Analyticsszel dolgozza fel. Végigvezeti az alábbi lépéseket: 

1. Hozzon létre egy Kafka engedélyezve van az Event Hubs-névtér.
2. Hozzon létre, amely üzeneteket küld az event hubs, Kafka ügyfél.
3. Hozzon létre egy Stream Analytics-feladatot, amely adatokat másol az event hubs egy Azure blob storage-bA. 

Nem kell módosítani a protokollt használó ügyfelek vagy a saját fürtök futtatni a Kafka-végpontot egy eseményközpont által elérhetővé tett használatakor. Az Azure Event Hubs az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja. vagy újabb verzió. 


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks).
* Egy [letöltött](https://maven.apache.org/download.cgi) és [telepített](https://maven.apache.org/install.html) Maven bináris archívum.
* [Git](https://www.git-scm.com/)
* Egy **Azure Storage-fiók**. Ha még nem rendelkezik ilyennel, a folytatás előtt [hozzon létre egyet](../storage/common/storage-account-create.md) . Ez az útmutató a Stream Analytics-feladat kimeneti adatokat egy Azure blob Storage tárolja. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka-kompatibilis Event Hubs-névtér létrehozása
A **standard** szintű Event Hubs névtér létrehozásakor a rendszer automatikusan engedélyezi a névtérhez tartozó Kafka-végpontot. A Kafka protokollt használó alkalmazásokból olyan eseményeket lehet továbbítani, amelyek standard szintű Event Hubs. A **standard** szintű Event Hubs névtér létrehozásához kövesse az [Event hub létrehozása a Azure Portal használatával](event-hubs-create.md) című témakör részletes utasításait. 

> [!NOTE]
> A Kafka-Event Hubs csak a **standard** és a **dedikált** szinteken érhető el. Az alapszintű **csomag** nem támogatja a Kafka használatát Event Hubson.

## <a name="send-messages-with-kafka-in-event-hubs"></a>A Kafka üzenetek küldése az Event Hubs

1. A [Kafka-tárházhoz tartozó Azure-Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka) klónozása a gépre.
2. Navigáljon a következő mappához: `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Frissítse a `src/main/resources/producer.config`gyártójának konfigurációs részleteit. Adja meg az **Event hub-névtér** **nevét** és **kapcsolati karakterláncát** . 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigáljon `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/com/example/app`, és nyissa meg a **TestDataReporter. Java** fájlt egy tetszőleges szerkesztőben. 
6. Tegye megjegyzésbe a következő kódsort:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adja hozzá a következő kódsort a megjegyzésekkel kód helyett: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ez a kód **JSON** formátumban küldi el az eseményre vonatkozó adatokat. Amikor konfigurál egy Stream Analytics-feladat bemenete, akkor adja meg JSON a bemeneti adatok formátumát. 
7. **Futtassa a gyártót és a** streamet a Kafka-kompatibilis Event Hubsba. Windows rendszerű gépen, ha **Node. js-parancssort**használ, váltson a `azure-event-hubs-for-kafka/quickstart/java/producer` mappára a parancsok futtatása előtt. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Győződjön meg arról, hogy eseményközpont kap az adatok

1. Az **entitások**területen válassza a **Event Hubs** lehetőséget. Ellenőrizze, hogy megjelenik-e egy **teszt**nevű Event hub. 

    ![Event hub - tesztelése](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Győződjön meg arról, hogy megjelenik-e az eseményközpontból érkező üzeneteket. 

    ![Event hub - üzenetek](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Folyamat esemény adatok használatával egy Stream Analytics-feladat
Ebben a szakaszban egy Azure Stream Analytics-feladatot hoz létre. A Kafka-ügyfél az eseményközpontba küldi az eseményeket. Létrehozhat egy Stream Analytics-feladat, amely eseményadatokat bemenetként tart, és kiírja azt egy Azure blob Storage-tárolóba. Ha nem rendelkezik **Azure Storage-fiókkal**, [hozzon létre egyet](../storage/common/storage-account-create.md).

A lekérdezést a Stream Analytics-feladat az áthalad az adatokat bármely elemzések végrehajtása nélkül. Létrehozhat egy lekérdezést, amely a bemeneti adatok más formátumban vagy szerzett insights kimeneti adatok előállításához.  

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása 

1. Válassza az **+ erőforrás létrehozása** lehetőséget a [Azure Portalban](https://portal.azure.com).
2. Válassza az **elemzés** lehetőséget az **Azure Marketplace** menüjében, majd válassza ki **stream Analytics feladatot**. 
3. Az **új stream Analytics** oldalon hajtsa végre a következő műveleteket: 
    1. Adja meg a feladattípus **nevét** . 
    2. Válassza ki az **előfizetését**.
    3. Válassza az **új létrehozása** az **erőforráscsoport** számára lehetőséget, és adja meg a nevet. **Használhat meglévő** erőforráscsoportot is. 
    4. Válassza ki a feladatokhoz tartozó **helyet** .
    5. Válassza a **Létrehozás** lehetőséget a feladatok létrehozásához. 

        ![Új Stream Analytics-feladat](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Az értesítési üzenetben válassza az **Ugrás az erőforráshoz** lehetőséget a **stream Analytics feladatok** lap megjelenítéséhez. 
2. A bal oldali menüben a **feladatok topológiája** szakaszban válassza a **bemenetek** lehetőséget.
3. Válassza az **adatfolyam hozzáadása bemenet**lehetőséget, majd válassza az **Event hub**elemet. 

    ![Eseményközpont felvétele bemenetként](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Az **Event hub bemeneti** konfigurációja lapon hajtsa végre a következő műveleteket: 

    1. Adja meg a bemenet **aliasát** . 
    2. Válassza ki az **Azure-előfizetését**.
    3. Válassza ki a korábban létrehozott **Event hub-névteret** . 
    4. Válassza ki az **Event hub** **tesztjét** . 
    5. Kattintson a **Mentés** gombra. 

        ![Event hub bemeneti konfiguráció](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Feladatkimenet konfigurálása 

1. Válassza a **kimenetek** lehetőséget a menü **feladatok topológiája** szakaszában. 
2. Válassza a **+ Hozzáadás** lehetőséget az eszköztáron, és válassza a **blob Storage** elemet.
3. A Blob storage kimeneti beállítások lapon tegye a következőket: 
    1. Adja meg a kimenet **aliasát** . 
    2. Jelölje ki az Azure-**előfizetést**. 
    3. Válassza ki az **Azure Storage-fiókját**. 
    4. Adja meg annak a **tárolónak a nevét** , amely a stream Analytics lekérdezésből származó kimeneti adatokat tárolja.
    5. Kattintson a **Mentés** gombra.

        ![A BLOB Storage kimeneti konfiguráció](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Lekérdezés definiálása
Miután sikeresen beállította a Stream Analytics-feladatot a beérkező adatfolyam olvasására, a következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Ez a forgatókönyv megadhat egy lekérdezést, amely áthalad az adatokat, bármely átalakítás végrehajtása nélkül.

1. Válassza a **lekérdezés**lehetőséget.
2. A lekérdezési ablakban cserélje le a `[YourOutputAlias]`t a korábban létrehozott kimeneti aliasra.
3. Cserélje le a `[YourInputAlias]`t a korábban létrehozott bemeneti aliasra. 
4. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Lekérdezés](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. Válassza az **Áttekintés** lehetőséget a bal oldali menüben. 
2. Válassza az **Indítás**lehetőséget. 

    ![Start menü](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. A **kezdési feladatok** lapon válassza az **Indítás**lehetőséget. 

    ![Indítsa el a feladat oldalát](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Várjon, amíg a feladatok állapota nem változik a **futtatástól** **kezdve** . 

    ![Feladat állapota – futó](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése
1. Futtassa újra a **Kafka gyártóját** , hogy eseményeket küldjön az Event hub-nak. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Győződjön meg arról, hogy a **kimeneti adatokat** az **Azure Blob Storage**-ban hozza létre a rendszer. Egy JSON-fájlt a tárolóhoz, az a következő példa sorokkal hasonló 100 sor jelenik meg: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Az Azure Stream Analytics-feladat bemeneti adatokat az eseményközpontból fogadott, és azt ebben a forgatókönyvben az Azure blob storage tárolja. 



## <a name="next-steps"></a>Következő lépések
Ebben a cikkben bemutattuk, hogyan streamelhet Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. Az Event Hubsszal és a Kafkához készült Event Hubsszal kapcsolatos további információkért tekintse át a következő témaköröket:  

- [Ismerkedés az Event Hubs szolgáltatással](event-hubs-what-is-event-hubs.md)
- [Az Apache Kafkához készült Event Hubs](event-hubs-for-kafka-ecosystem-overview.md)
- [Kafka-kompatibilis eseményközpont létrehozása](event-hubs-create-kafka-enabled.md)
- [Streamelés az Event Hubsba a Kafka-alkalmazásokból](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Kafka-közvetítő tükrözése egy Kafka-kompatibilis eseményközpontba](event-hubs-kafka-mirror-maker-tutorial.md)
- [Az Apache Spark csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-spark-tutorial.md)
- [Az Apache Flink csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-flink-tutorial.md)
- [A Kafka Connect integrálása egy Kafka-kompatibilis eseményközponttal](event-hubs-kafka-connect-tutorial.md)
- [Az Akka Streams csatlakoztatása egy Kafka-kompatibilis eseményközponthoz](event-hubs-kafka-akka-streams-tutorial.md)
- [További példák a GitHubon](https://github.com/Azure/azure-event-hubs-for-kafka) 
