---
title: Oktatóanyag – egy végpontok közötti Async Java SQL API-alkalmazásminta a hírcsatorna módosításával
description: Ez az oktatóanyag végigvezeti egy egyszerű Java SQL API-alkalmazás, amely dokumentumokat illeszt be egy Azure Cosmos DB-tárolóba, miközben a tároló materializált nézetét a változási hírcsatorna használatával.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586700"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Oktatóanyag – egy végpontok közötti Async Java SQL API-alkalmazásminta a hírcsatorna módosításával

Ez az oktatóanyag-útmutató végigvezeti egy egyszerű Java SQL API-alkalmazás, amely dokumentumokat illeszt be egy Azure Cosmos DB-tárolóba, miközben a tároló materializált nézetét a változási hírcsatorna használatával.

## <a name="prerequisites"></a>Előfeltételek

* Személyi számítógép

* Az Azure Cosmos DB-fiókjának URI-ja és kulcsa

* Maven

* Java 8

## <a name="background"></a>Háttér

Az Azure Cosmos DB change feed egy eseményvezérelt felületet biztosít a dokumentumok beszúrására adott válaszként végrehajtott műveletek elindításához. Ennek számos felhasználási célja van. Például az alkalmazások, amelyek egyaránt olvasási és írási nehéz, a fő használata a változás hírcsatorna, hogy hozzon létre egy valós idejű **materializált nézet** egy tároló, mert a dokumentumok betöltése. A materializált nézettároló ugyanazokat az adatokat tárolja, de a hatékony olvasásérdekében particionált, így az alkalmazás olvasási és írási szempontból is hatékony.

A változáshírcsatorna-események kezelésének munkáját nagyrészt az SDK-ba épített Change Feed Processzor könyvtár gondoskodja. Ez a könyvtár elég erős ahhoz, hogy a változáscsatorna-eseményeket több dolgozó között is elossza, ha ez szükséges. Mindössze annyit kell tennie, hogy a Change Feed könyvtár a visszahívás.

Ez az egyszerű példa bemutatja a Csatornaprocesszor-kódtár módosítása című dokumentumot egyetlen dolgozóval, amely dokumentumokat hoz létre és távolít el materializált nézetből.

## <a name="setup"></a>Telepítés

Ha még nem tette meg, klónozza az alkalmazás például tártárját:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

> A Java SDK 4.0 vagy a Java SDK 3.7.0 segítségével szeretné végigcsinálni ezt a rövid útmutatót. **Ha java SDK 3.7.0-t szeretne használni, ```git checkout SDK3.7.0```a terminál típusában. ** Ellenkező esetben maradjon az ```master``` ágon, amely alapértelmezés szerint a Java SDK 4.0.

Nyisson meg egy terminált a tártárkönyvtárában. Az alkalmazás létrehozása a futtatással

```bash
mvn clean package
```

## <a name="walkthrough"></a>Útmutatás

1. Az első ellenőrzés, rendelkeznie kell egy Azure Cosmos DB-fiókkal. Nyissa meg az **Azure Portalt** a böngészőben, nyissa meg az Azure Cosmos DB-fiókját, és a bal oldali ablaktáblában keresse meg az **Adatkezelőt.**

    ![Azure Cosmos DB-fiók](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Futtassa az alkalmazást a terminálon a következő paranccsal:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Nyomja meg az Enter billentyűt, amikor meglátja

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    majd térjen vissza az Azure Portal Data Explorer böngészőjében. Látni fogja, hogy a **GroceryStoreDatabase** adatbázis három üres tárolóval bővült: 

    * **InventoryContainer** - A készletrekord a mi példa ```id``` élelmiszerbolt, particionált elem, amely egy UUID.
    * **InventoryContainer-pktype** - A készletrekord materializált nézete, elemfeletti lekérdezésekre optimalizálva```type```
    * **InventoryContainer-leases** – A bérleti tároló mindig szükség van a változás hírcsatorna; a bérletek nyomon követik az alkalmazás előrehaladását a változási hírcsatorna olvasása terén.


    ![Üres tárolók](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. A terminálon most egy

    ```bash
    Press enter to start creating the materialized view...
    ```

    Nyomja meg az Enter billentyűt. Most a következő kódblokk hajtja végre és inicializálja a Change Feed processzort egy másik szálon: 


    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

    ```"SampleHost_1"```A Hírcsatorna-feldolgozó módosítása munkavégző neve. ```changeFeedProcessorInstance.start()```az, ami ténylegesen elindítja a Change Feed processzort.

    Térjen vissza az Azure Portal Adatkezelőböngészőjéhez a böngészőben. Az **InventoryContainer-leases** tároló alatt kattintson az **elemekre** a tartalmának megtekintéséhez. Látni fogja, hogy a változás-napló processzor a címbérlet-tárolót, ```SampleHost_1``` azaz a processzor hozzárendelte a dolgozó nak egy bérletet az **InventoryContainer**egyes partícióin.

    ![Bérletek](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Nyomja meg ismét az Enter gombot a terminálon. Ezzel 10 dokumentumot kell beszúrni az **InventoryContainer tárolóba.** Minden dokumentum beillesztése jSON-ként jelenik meg a hírcsatorna módosítása kor; a következő visszahívási kód úgy kezeli ezeket az eseményeket, hogy a JSON-dokumentumokat materializált nézetbe tükrözi:

    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

1. Hagyja, hogy a kód 5-10 mp-es futást fusson. Ezután térjen vissza az Azure Portal Adatkezelőjéhez, és keresse meg **az InventoryContainer > az elemeket.** Látnia kell, hogy a cikkek bekerülnek a készlettárolóba; vegye figyelembe a```id```partíciógombot ( ).

    ![Előtolási tároló](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. Most az Adatkezelőben keresse meg az **InventoryContainer-pktype > elemeket.** Ez a materializált nézet - a tárolóban lévő elemek tükrözik **az InventoryContainer-t,** mert programozott módon szúrták be őket a Változási hírcsatorna által. Megjegyzés: a```type```partíciógomb ( ). Így ez a materializált nézet a ```type```lekérdezések szűrésére van optimalizálva, ami nem ```id```lenne hatékony az **InventoryContainer-on,** mert particionálva van.

    ![Tényleges táblán alapuló nézet](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Egy dokumentumot törölünk az **InventoryContainer** és **az InventoryContainer-pktype** rendszerből egyetlen ```upsertItem()``` hívás sal. Először tekintse meg az Azure Portal Adatkezelőt. Töröljük azt a dokumentumot, amelyhez; ```/type == "plums"``` az alábbi piros bekerített

    ![Tényleges táblán alapuló nézet](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Nyomja meg újra a ```deleteDocument()``` függvény hívásához a példakódban. Ez a funkció, az alábbiakban látható, upserts egy új változata a dokumentum ```/ttl == 5```, amely beállítja a dokumentum Time-To-Live (TTL) a 5 sec. 
    
    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

    A módosítási hírcsatorna ```feedPollDelay``` 100 ms-ra van állítva; ezért a Change Feed szinte azonnal válaszol ```updateInventoryTypeMaterializedView()``` erre a frissítésre, és a fenti hívásokat mutatja be. Ez az utolsó függvényhívás az 5 mp-es TTL-lel rendelkező új dokumentumot az **InventoryContainer-pktype -ra növeli.**

    A hatás az, hogy körülbelül 5 másodperc elteltével a dokumentum lejár, és mindkét tárolóból törlődik.

    Erre az eljárásra azért van szükség, mert a Módosítási hírcsatorna csak az elemek beszúrásakor vagy frissítésén történik eseményeket, az elem törlését nem.

1. Nyomja meg még egyszer az Enter billentyűt a program bezárásához és az erőforrások karbantartásához.
