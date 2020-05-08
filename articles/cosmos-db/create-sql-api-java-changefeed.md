---
title: Oktatóanyag – egy végpontok közötti aszinkron Java SQL API-alkalmazási minta a változási hírcsatornával
description: Ez az oktatóanyag végigvezeti egy olyan egyszerű Java SQL API-alkalmazáson, amely a dokumentumokat egy Azure Cosmos DB tárolóba szúrja be, miközben a változási hírcsatorna használatával megtartja a tároló anyagilag látható nézetét.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: c74ec73eb06c43110747d87e6fecd12183527759
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872543"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Oktatóanyag – egy végpontok közötti aszinkron Java SQL API-alkalmazási minta a változási hírcsatornával

Ez az oktatóanyag-útmutató végigvezeti egy olyan egyszerű Java SQL API-alkalmazáson, amely a dokumentumokat egy Azure Cosmos DB tárolóba szúrja be, miközben a változási hírcsatorna használatával megtartja a tároló anyagilag látható nézetét.

## <a name="prerequisites"></a>Előfeltételek

* Személyes számítógép

* A Azure Cosmos DB fiókjának URI-ja és kulcsa

* Maven

* Java 8

## <a name="background"></a>Háttér

A Azure Cosmos DB változási hírcsatorna eseményvezérelt felületet biztosít a dokumentumok beszúrására reagáló műveletek elindításához. Ennek számos felhasználási felhasználása van. Az olvasási és írási nehéz alkalmazások esetében például a változási hírcsatorna fő felhasználási területe a tároló valós idejű, a dokumentumok betöltését eredményező **nézetének** létrehozása. Az anyagot tartalmazó nézet tárolója ugyanazokat az adatokat fogja tárolni, de a hatékony olvasásokra particionálja, így az alkalmazás olvasási és írási hatékonyságot is biztosít.

A Change feed-események kezelésének munkája nagy mértékben gondoskodik az SDK-ba épített Change feed Processor Library-ről. Ez a kódtár elég erős ahhoz, hogy a változási hírcsatornák eseményeit több feldolgozón is el lehessen osztani, ha erre szükség van. Mindössze annyit kell tennie, hogy megadja a Change feed Library a visszahívást.

Ez az egyszerű példa azt mutatja be, hogy a hírcsatorna-feldolgozó függvénytár egyetlen dolgozóval hozza létre és törli a dokumentumokat egy anyagbeli nézetből.

## <a name="setup"></a>Telepítés

Ha még nem tette meg, az alkalmazás klónozása:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> Ebben a rövid útmutatóban a Java SDK 4,0 vagy a Java SDK 3.7.0 használatával dolgozhat. **Ha a Java SDK 3.7.0 szeretné használni, a terminál típusában ```git checkout SDK3.7.0``` **. Ellenkező esetben a Java SDK ```master``` 4,0 alapértelmezésben marad a fiókirodában.

Nyisson meg egy terminált a tárház címtárában. Az alkalmazás összeállítása a futtatásával

```bash
mvn clean package
```

## <a name="walkthrough"></a>Útmutatás

1. Első lépésként Azure Cosmos DB fiókkal kell rendelkeznie. Nyissa meg az **Azure Portalt** a böngészőben, lépjen a Azure Cosmos db-fiókjába, és a bal oldali panelen navigáljon a **adatkezelő**elemre.

    ![Azure Cosmos DB fiók](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Futtassa az alkalmazást a terminálon a következő parancs használatával:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Nyomja le az ENTER billentyűt, amikor megjelenik

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Ezután térjen vissza az Azure Portal Adatkezelő a böngészőben. Látni fogja, hogy egy adatbázis- **GroceryStoreDatabase** három üres tárolóval lett hozzáadva: 

    * **InventoryContainer** – a példaként szolgáló áruház leltározási rekordja, amely egy UUID- ```id``` t tartalmazó elemre van particionálva.
    * **InventoryContainer-pktype** – az elemre irányuló lekérdezésekre optimalizált leltári rekord anyagilag látható nézete```type```
    * **InventoryContainer – bérletek** – a változási hírcsatornára mindig a bérletek tárolója szükséges. a bérletek nyomon követik az alkalmazás előrehaladását a változási hírcsatorna olvasásakor.


    ![Üres tárolók](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. A terminálban ekkor megjelenik egy üzenet

    ```bash
    Press enter to start creating the materialized view...
    ```

    Nyomja le az ENTER billentyűt. Most a következő kódrészlet végrehajtja és inicializálja a változási hírcsatorna processzorát egy másik szálon: 

   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

   # <a name="java-sdk-370"></a>[Java SDK-3.7.0](#tab/v3sdk)

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start    
    ```
   ---

    ```"SampleHost_1"```a a változási csatorna feldolgozó feldolgozójának neve. ```changeFeedProcessorInstance.start()```valójában elindítja a Change feed processzort.

    Térjen vissza az Azure Portal Adatkezelő a böngészőben. A **InventoryContainer-bérletek** tárolóban kattintson az **elemek elemre** a tartalmának megtekintéséhez. Látni fogja, hogy a változási hírcsatorna processzora feltöltte a címbérleti tárolót, azaz a processzor ```SampleHost_1``` a **InventoryContainer**egyes partícióinak bérletét rendelte hozzá a feldolgozóhoz.

    ![Bérletek](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Nyomja le ismét az ENTER billentyűt a terminálon. Ekkor a rendszer 10 dokumentumot szúr be a **InventoryContainerba**. Minden dokumentum beszúrása JSON-ként jelenik meg a változási hírcsatornában. a következő visszahívási kód kezeli ezeket az eseményeket úgy, hogy a JSON-dokumentumokat egy anyagbeli nézetbe tükrözi:

   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

   # <a name="java-sdk-370"></a>[Java SDK-3.7.0](#tab/v3sdk)

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```
   ---

1. 5 10sec futtatásának engedélyezése a kód számára. Ezután térjen vissza az Azure Portalra Adatkezelő és navigáljon a **InventoryContainer > elemekhez**. Látnia kell, hogy a rendszer beszúrja az elemeket a leltári tárolóba; Jegyezze fel a partíciós```id```kulcsot ().

    ![Hírcsatorna-tároló](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Most Adatkezelő navigáljon a **InventoryContainer-pktype > elemekhez**. Ez az anyagbeli nézet – az ebben a tárolóban lévő elemek tükrözött **InventoryContainer** , mert programozott módon szúrták be a hírcsatorna módosításával. Jegyezze fel a partíciós```type```kulcsot (). Így ez az anyagbeli nézet a lekérdezési szűrésre ```type```van optimalizálva, ami nem hatékony a **InventoryContainer** , mert particionálva van ```id```.

    ![Tényleges táblán alapuló nézet](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. A **InventoryContainer** és a **InventoryContainer-pktype** dokumentumból csak egyetlen ```upsertItem()``` hívást fogunk törölni. Először tekintse meg az Azure Portal Adatkezelő. Töröljük a dokumentumot, amelynek ```/type == "plums"```; az alábbi piros színnel van Bekerítve

    ![Tényleges táblán alapuló nézet](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Nyomja meg ismét az ENTER billentyűt ```deleteDocument()``` a függvény meghívásához a példában szereplő kódban. Ez a függvény az alább látható módon upsert a dokumentum egy új verzióját ```/ttl == 5```, amely a dokumentumot élettartam (TTL) értékre állítja a 5Sec. 
    
   # <a name="java-sdk-40"></a>[Java SDK 4,0](#tab/v4sdk)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```
   # <a name="java-sdk-370"></a>[Java SDK-3.7.0](#tab/v3sdk)

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```
   ---

    A módosítási ```feedPollDelay``` hírcsatorna a 100ms értékre van állítva. Ezért a változási csatorna szinte azonnal reagál erre a frissítésre, ```updateInventoryTypeMaterializedView()``` és a fent látható hívásokat kéri. Az utolsó függvény hívása az új dokumentumot a 5Sec TTL-upsert fogja a **InventoryContainer-pktype**.

    A hatás az, hogy körülbelül 5 másodperc elteltével a dokumentum lejár, és mindkét tárolóból törölve lesz.

    Ez az eljárás azért szükséges, mert a csak az elemek beszúrásával vagy frissítésével kapcsolatos eseményeket, nem pedig az elemek törlését kell kiállítani.

1. Nyomja le még egyszer az ENTER billentyűt a program bezárásához és az erőforrások törléséhez.
