---
title: Az Apache Kafka feldolgozni az Azure Stream analytics használatával az Event Hubs-események |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan dolgozza fel a Kafka, amely vannak töltve az event hubs használatával az Azure Stream Analytics használatával
services: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: spelluru
ms.openlocfilehash: a066d2a55f6949eea316eaf0a2956500667a996f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43340271"
---
# <a name="process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Apache Kafka for Event Hubs-események feldolgozása Stream Analytics használatával 
Ez a cikk bemutatja, hogyan adatok streamelése az Event Hubs Kafka-kompatibilis, és az Azure Stream Analyticsszel dolgozza fel. Végigvezeti az alábbi lépéseket: 

1. Hozzon létre egy Kafka engedélyezve van az Event Hubs-névtér.
2. Hozzon létre, amely üzeneteket küld az event hubs, Kafka ügyfél.
3. Hozzon létre egy Stream Analytics-feladatot, amely adatokat másol az event hubs egy Azure blob storage-bA. 

Nem kell módosítani a protokollt használó ügyfelek vagy a saját fürtök futtatni a Kafka-végpontot egy eseményközpont által elérhetővé tett használatakor. Az Azure Event Hubs az [Apache Kafka 1.0-s verzióját](https://kafka.apache.org/10/documentation.html) támogatja. vagy újabb verzió. 


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), mielőtt hozzákezd.
* [Java fejlesztői készlet (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Egy [letöltött](http://maven.apache.org/download.cgi) és [telepített](http://maven.apache.org/install.html) Maven bináris archívum.
* [Git](https://www.git-scm.com/)
* Egy **Azure Storage-fiók**. Ha nem rendelkezik ilyennel, [hozzon létre egyet](../storage/common/storage-create-storage-account.md#create-a-storage-account) a folytatás előtt. Ez az útmutató a Stream Analytics-feladat kimeneti adatokat egy Azure blob Storage tárolja. 


## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Kafka-kompatibilis Event Hubs-névtér létrehozása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és kattintson a **erőforrás létrehozása** , a képernyő bal felső.
2. Keresse meg **az Event Hubs** , és válassza ki a beállításokat itt látható:
    
    ![Event Hubs keresése a portálon](./media/event-hubs-kafka-stream-analytics/select-event-hubs.png) 
3. Az a **az Event Hubs** lapon jelölje be **létrehozás**.
4. Az a **létrehozása Namespace** lapon, tegye a következőket: 
    1. Adjon meg egy egyedi **neve** a névtérhez. 
    2. Válassza ki a **tarifacsomag**. 
    3. Válassza ki **Kafka engedélyezése**. Ez a lépés egy **fontos** . lépés. 
    4. Válassza ki a **előfizetés** található, amely szeretné létrehozni az eseményközpont-névtér. 
    5. Hozzon létre egy új **erőforráscsoport** , vagy válasszon ki egy meglévő erőforráscsoportot. 
    6. Válassza ki a **hely**. 
    7. Kattintson a **Create** (Létrehozás) gombra.
    
        ![Névtér létrehozása](./media/event-hubs-kafka-stream-analytics/create-event-hub-namespace-page.png) 
4. Az a **értesítési üzenet**, jelölje be a **erőforráscsoport-név**. 

    ![Névtér létrehozása](./media/event-hubs-kafka-stream-analytics/creation-station-message.png)
1. Válassza ki a **eseményközpont-névtér** az erőforráscsoportban. 
2. A névtér létrehozása után válassza ki a **megosztott elérési házirendek** alatt **beállítások**.

    ![Kattintás a Megosztott elérési szabályzatok elemre](./media/event-hubs-kafka-stream-analytics/shared-access-policies.png)
5. Választhatja az alapértelmezett **RootManageSharedAccessKey** szabályzatot, vagy hozzáadhat egy újat is. Kattintson a házirend nevét és a példány a **kapcsolati karakterlánc**. A kapcsolati karakterlánc használatával a Kafka-ügyfél konfigurálásához. 
    
    ![Szabályzat kiválasztása](./media/event-hubs-kafka-stream-analytics/connection-string.png)  

Így már streamelheti az eseményeket az Event Hubsba a Kafka-protokollt használó alkalmazásaiból.

## <a name="send-messages-with-kafka-in-event-hubs"></a>A Kafka üzenetek küldése az Event Hubs

1. Klónozás a [Azure Event Hubs-tárház](https://github.com/Azure/azure-event-hubs) a gépre.
2. Lépjen abba a mappába: `azure-event-hubs/samples/kafka/quickstart/producer`. 
4. A konfigurációs adatok frissítése a gyártó `src/main/resources/producer.config`. Adja meg a **neve** és **kapcsolati karakterlánc** számára a **eseményközpont-névtér**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigáljon a `azure-event-hubs/samples/kafka/quickstart/producer/src/main/java/com/example/app`, és nyissa meg a **TestDataReporter.java** fájlt egy tetszőleges szerkesztőben. 
6. Tegye megjegyzésbe a következő kódsort:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Adja hozzá a következő kódsort a megjegyzésekkel kód helyett: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Ez a kód elküldi az eseményadatokat **JSON** formátumban. Amikor konfigurál egy Stream Analytics-feladat bemenete, akkor adja meg JSON a bemeneti adatok formátumát. 
7. **Futtassa az előállítói** és a stream Kafka-kompatibilis Event hubsba. A Windows gépen használatakor egy **Node.js parancssort**, váltson át a `azure-event-hubs/samples/kafka/quickstart/producer` mappa ezek a parancsok futtatása előtt. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Győződjön meg arról, hogy eseményközpont kap az adatok

1. Válassza ki **az Event Hubs** alatt **entitások**. Győződjön meg arról, hogy megjelenik-e egy eseményközpontba nevű **tesztelése**. 

    ![Event hub - tesztelése](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Győződjön meg arról, hogy megjelenik-e az eseményközpontból érkező üzeneteket. 

    ![Event hub - üzenetek](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Folyamat esemény adatok használatával egy Stream Analytics-feladat
Ebben a szakaszban egy Azure Stream Analytics-feladatot hoz létre. A Kafka-ügyfél az eseményközpontba küldi az eseményeket. Létrehozhat egy Stream Analytics-feladat, amely eseményadatokat bemenetként tart, és kiírja azt egy Azure blob Storage-tárolóba. Ha nem rendelkezik egy **Azure Storage-fiók**, [hozzon létre egyet](../storage/common/storage-create-storage-account.md#create-a-storage-account).

A lekérdezést a Stream Analytics-feladat az áthalad az adatokat bármely elemzések végrehajtása nélkül. Létrehozhat egy lekérdezést, amely a bemeneti adatok más formátumban vagy szerzett insights kimeneti adatok előállításához.  

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása 

1. Válassza ki **+ erőforrás létrehozása** a a [az Azure portal](https://portal.azure.com).
2. Válassza ki **Analytics** a a **Azure Marketplace-en** menüben, és válassza a **Stream Analytics-feladat**. 
3. Az a **új Stream Analytics** lapon, tegye a következőket: 
    1. Adjon meg egy **neve** a feladathoz. 
    2. Válassza ki a **előfizetés**.
    3. Válassza ki **új létrehozása** számára a **erőforráscsoport** , és adja meg a nevét. Emellett **egy meglévő** erőforráscsoportot. 
    4. Válassza ki a **hely** a feladathoz.
    5. Válassza ki **létrehozás** létrehozni a feladatot. 

        ![Új Stream Analytics-feladat](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Feladatbemenet konfigurálása

1. Válassza ki az értesítési üzenet ** Ugrás erőforrás ** megtekintéséhez a **Stream Analytics-feladat** lapot. 
2. Válassza ki **bemenetek** a a **FELADATTOPOLÓGIA** szakaszban a bal oldali menüben.
3. Válassza ki **streambemenet hozzáadása**, majd válassza ki **Eseményközpont**. 

    ![Eseményközpont felvétele bemenetként](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Az a **Event Hub-bemenet** konfiguráció lapon, tegye a következőket: 

    1. Adjon meg egy **alias** bemenethez. 
    2. Válassza ki az **Azure-előfizetését**.
    3. Válassza ki a **eseményközpont-névtér** a létrehozott korábban. 
    4. Válassza ki **tesztelése** számára a **eseményközpont**. 
    5. Kattintson a **Mentés** gombra. 

        ![Event hub bemeneti konfiguráció](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Feladatkimenet konfigurálása 

1. Válassza ki **kimenetek** a a **FELADATTOPOLÓGIA** szakaszban kattintson a menü. 
2. Válassza ki **+ Hozzáadás** az eszköztáron, majd válassza ki a **Blob storage-ban**
3. A Blob storage kimeneti beállítások lapon tegye a következőket: 
    1. Adjon meg egy **alias** a kimenethez. 
    2. Jelölje ki az Azure-**előfizetést**. 
    3. Válassza ki a **Azure Storage-fiók**. 
    4. Adjon meg egy **a tároló neve** , amely a Stream Analytics-lekérdezés a kimeneti adatokat tárolja.
    5. Kattintson a **Mentés** gombra.

        ![A BLOB Storage kimeneti konfiguráció](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Lekérdezés definiálása
Miután sikeresen beállította a Stream Analytics-feladatot a beérkező adatfolyam olvasására, a következő lépés egy átalakítás létrehozása, amely valós időben elemzi az adatokat. Az átalakítási lekérdezés definiálásához használja a [Stream Analytics lekérdezési nyelvet](https://msdn.microsoft.com/library/dn834998.aspx). Ez a forgatókönyv megadhat egy lekérdezést, amely áthalad az adatokat, bármely átalakítás végrehajtása nélkül.

1. Válassza ki **lekérdezés**.
2. A lekérdezési ablakban cserélje le a `[YourOutputAlias]` a korábban létrehozott kimeneti alias.
3. Cserélje le `[YourInputAlias]` a korábban létrehozott a bemeneti alias. 
4. Válassza az eszköztár **Save** (Mentés) elemét. 

    ![Lekérdezés](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

1. Válassza ki **áttekintése** a bal oldali menüben. 
2. Válassza ki **Start**. 

    ![Start menü](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Az a **indítási feladat** lapon jelölje be **Start**. 

    ![Indítsa el a feladat oldalát](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Várjon, amíg a feladat állapota megváltozik a **kezdő** való **futó**. 

    ![Feladat állapota – futó](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése
1. Futtassa a **Kafka producer** újra az események küldése az event hubs. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Győződjön meg arról, hogy **kimeneti adatok** jön létre a **az Azure blob storage-bA**. Egy JSON-fájlt a tárolóhoz, az a következő példa sorokkal hasonló 100 sor jelenik meg: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    Az Azure Stream Analytics-feladat bemeneti adatokat az eseményközpontból fogadott, és azt ebben a forgatókönyvben az Azure blob storage tárolja. 



## <a name="next-steps"></a>További lépések
Ebben a cikkben bemutattuk, hogyan streamelhet Kafka-kompatibilis Event Hubsba anélkül, hogy módosítaná a protokollügyfeleket vagy saját fürtöket futtatna. További információért folytassa az alábbi oktatóanyaggal:

> [!div class="nextstepaction"]
> [Kafka MirrorMaker használata az Event Hubsszal](event-hubs-kafka-mirror-maker-tutorial.md)
