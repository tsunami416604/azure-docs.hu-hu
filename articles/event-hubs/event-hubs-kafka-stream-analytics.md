---
title: Azure Event Hubs – Apache Kafka-események feldolgozása
description: 'Oktatóanyag: Ez a cikk bemutatja, hogyan lehet feldolgozni az eseményközpontokon keresztül az Azure Stream Analytics használatával bevitt Kafka-eseményeket.'
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
ms.date: 04/02/2020
ms.author: spelluru
ms.openlocfilehash: 9c678a91b88b87acb438311b4968be4cae46733b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632800"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Oktatóanyag: Az Apache Kafka feldolgozása az Event Hubs-eseményekhez a Stream analytics használatával 
Ez a cikk bemutatja, hogyan továbbítsa az adatokat az Event Hubs-ba, és hogyan dolgozza fel azokat az Azure Stream Analytics szolgáltatással. Végigvezeti a következő lépéseken: 

1. Event Hubs-névtér létrehozása.
2. Hozzon létre egy Kafka-ügyfelet, amely üzeneteket küld az eseményközpontnak.
3. Hozzon létre egy Stream Analytics-feladatot, amely adatokat másol az eseményközpontból egy Azure blob-tárolóba. 

Nem kell módosítania a protokollügyfeleket, és nem kell futtatnia a saját fürtjeit, ha egy eseményközpont által elérhetővé tett Kafka-végpontot használ. Az Azure Event Hubs az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja. és felette. 


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](https://aka.ms/azure-jdks).
* [Töltsön le](https://maven.apache.org/download.cgi) és [telepítsen](https://maven.apache.org/install.html) egy Maven bináris archívumot.
* [Git](https://www.git-scm.com/)
* Egy **Azure Storage-fiók.** Ha még nem rendelkezik ilyen, [hozzon létre egyet,](../storage/common/storage-account-create.md) mielőtt továbbhaladna. A streamanalytics-feladat ebben a forgatókönyvben tárolja a kimeneti adatokat egy Azure blob storage.The Stream Analytics job in this walkthrough stores the output data in an Azure blob storage. 


## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása
Szabványos **szintű** Event Hubs névtér létrehozásakor a névtér Kafka végpontja automatikusan engedélyezve lesz. A Kafka protokollt használó alkalmazások eseményeit streamelheti standard szintű Event Hubs-ba. Kövesse a lépésenkénti utasításokat a [Hozzon létre egy eseményközpontot](event-hubs-create.md) az Azure Portal használatával egy **szabványos** szintű Event Hubs névtér létrehozásához. 

> [!NOTE]
> A Kafka eseményközpontjai csak **normál** és **dedikált** szinteken érhetők el. Az **alapszintű** szint nem támogatja a Kafka az Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Üzenetek küldése a Kafkával az Event Hubs-ban

1. Klónozza az [Azure Event Hubs a Kafka tárház](https://github.com/Azure/azure-event-hubs-for-kafka) a gépre.
2. Keresse meg a `azure-event-hubs-for-kafka/quickstart/java/producer`mappát: . 
4. A gyártó konfigurációs adatainak frissítése a alkalmazásban. `src/main/resources/producer.config` Adja meg az **eseményközpont névterének** **nevét** és **nevét.** 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Keresse `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/`meg a t, és nyissa meg **a TestDataReporter.java** fájlt egy ön által választott szerkesztőben. 
6. Fűzze ki a következő kódsort:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. A megjegyzéskód helyett a következő kódsort kell megadni: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ez a kód **JSON** formátumban küldi az eseményadatokat. Amikor egy Stream Analytics-feladat bemenetét konfigurálja, a JSON-t adja meg a bemeneti adatok formátumaként. 
7. **Futtassa a gyártót,** és streamelje az Eseményközpontokba. Windows rendszerben a **Node.js parancssor**használatakor váltson át a mappára, mielőtt futtatja ezeket a `azure-event-hubs-for-kafka/quickstart/java/producer` parancsokat. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Annak ellenőrzése, hogy az eseményközpont fogadja-e az adatokat

1. Válassza **az Eseményközpontok lehetőséget az ENTITÁSok** **csoportban.** Ellenőrizze, hogy lát-e **egy teszt**nevű eseményközpontot. 

    ![Eseményközpont - teszt](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Ellenőrizze, hogy az eseményközpontba érkező üzenetek jelennek-e meg. 

    ![Eseményközpont - üzenetek](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Eseményadatok feldolgozása Stream Analytics-feladattal
Ebben a szakaszban hozzon létre egy Azure Stream Analytics-feladatot. A Kafka-ügyfél eseményeket küld az eseményközpontba. Hozzon létre egy Stream Analytics-feladatot, amely az eseményadatokat bemenetként veszi fel, és egy Azure blobstorage-ba adja ki. Ha nem rendelkezik **Azure Storage-fiókkal,** [hozzon létre egyet.](../storage/common/storage-account-create.md)

A Stream Analytics-feladat lekérdezése elemzés nélkül halad át az adatokon. Létrehozhat egy lekérdezést, amely átalakítja a bemeneti adatokat, hogy a kimeneti adatok at egy másik formátumban vagy szerzett insights.  

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása 

1. Válassza **a + Erőforrás létrehozása** az Azure [Portalon](https://portal.azure.com)lehetőséget.
2. Válassza az **Analytics** lehetőséget az **Azure Marketplace** menüben, majd a **Stream Analytics-feladat**lehetőséget. 
3. Az **Új streamanalytics** lapon tegye a következő műveleteket: 
    1. Adja meg a feladat **nevét.** 
    2. Válassza ki az **előfizetést.**
    3. Válassza **az Új létrehozása** az **erőforráscsoporthoz** lehetőséget, és írja be a nevet. Meglévő erőforráscsoportot is **használhat.** 
    4. Adja meg a feladat **helyét.**
    5. A feladat létrehozásához válassza a **Létrehozás** gombot. 

        ![Új Stream Analytics-feladat](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Az értesítési üzenetben válassza az **Ugrás az erőforráshoz** lehetőséget a **Stream Analytics feladatoldal** megtekintéséhez. 
2. A bal oldali menü **JOB TOPOLÓGIA** szakaszában válassza a **Bemenetek** lehetőséget.
3. Válassza **az Adatfolyam-bevitel hozzáadása**lehetőséget, majd az **Eseményközpont**lehetőséget. 

    ![Eseményközpont hozzáadása bemenetként](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Az **Event Hub bemeneti konfigurációs** lapján tegye a következő műveleteket: 

    1. Adja meg a bemenet **aliasát.** 
    2. Válassza ki az **Azure-előfizetését**.
    3. Válassza ki a korábban létrehozott **eseményközpont-névteret.** 
    4. Válassza ki az **eseményközpont tesztjét.** **test** 
    5. Kattintson a **Mentés** gombra. 

        ![Az eseményközpont bemeneti konfigurációja](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Feladatkimenet konfigurálása 

1. A menü **JOB TOPOLÓGIA** szakaszában válassza a **Kimenetek** lehetőséget. 
2. Válassza a **+ Hozzáadás** lehetőséget az eszköztáron, és válassza a **Blob storage (Blob storage)** lehetőséget.
3. A Blob storage kimeneti beállításai lapon tegye a következő műveleteket: 
    1. Adja meg a kimenet **aliasát.** 
    2. Válassza ki **Azure-előfizetését.** 
    3. Válassza ki az **Azure Storage-fiókot.** 
    4. Adja meg a Stream Analytics-lekérdezés kimeneti adatait tároló **tároló nevét.**
    5. Kattintson a **Mentés** gombra.

        ![A Blob Storage kimeneti konfigurációja](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Lekérdezés definiálása
Miután sikeresen beállította a Stream Analytics-feladatot a beérkező adatfolyam olvasására, a következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Ebben a forgatókönyvben definiálhat egy lekérdezést, amely átalakítás nélkül halad át az adatokon.

1. Válassza a **Lekérdezés**lehetőséget.
2. A lekérdezési ablakban cserélje le `[YourOutputAlias]` a korábban létrehozott kimeneti aliasra.
3. Cserélje `[YourInputAlias]` le a korábban létrehozott bemeneti aliasra. 
4. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Lekérdezés](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. Válassza a bal oldali menü **Áttekintés parancsát.** 
2. Válassza az **Indítás** elemet. 

    ![Start menü](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. A **Kezdő feladat** lapon válassza a **Start**gombot. 

    ![A feladat kezdete lap](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Várjon, amíg a feladat állapota **indításról futtatásra** **változik.** 

    ![Feladat állapota - futás](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése
1. Futtassa újra a **Kafka-producert,** hogy eseményeket küldjön az eseményközpontba. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Győződjön meg arról, hogy **a kimeneti adatok** az **Azure blob storage-ban**jönlétre. A tárolóban egy JSON-fájl látható, amely 100 sort tartalmaz, amelyek a következő mintasorokhoz hasonlóan néznek ki: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Az Azure Stream Analytics-feladat bemeneti adatokat kapott az eseményközpontból, és ebben a forgatókönyvben az Azure blob storage-ban tárolta azokat. 



## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan streamelhet az Event Hubs-ba a protokollügyfelek módosítása vagy a saját fürtök futtatása nélkül. Ha többet szeretne megtudni az Apache Kafka Eseményközpontokról, olvassa el [az Apache Kafka fejlesztői útmutatóját az Azure Event Hubs szolgáltatáshoz.](apache-kafka-developer-guide.md) 