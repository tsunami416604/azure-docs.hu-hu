---
title: "Azure Table storage-ának Java használatával |} Microsoft Docs"
description: "Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható."
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 45145189-e67f-4ca6-b15d-43af7bfd3f97
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 6862475e05f49c7da823bcfb70f30ee484131d12
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-from-java"></a>Azure Table storage-ának Java használatával
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a szolgáltatást az Azure Table storage szolgáltatást használó általános forgatókönyvhöz. A mintákat a Java és -felhasználási nyelven íródtak a [Azure Storage SDK for Java][Azure Storage SDK for Java]. Az ismertetett forgatókönyvek **létrehozása**, **listázása**, és **törlése** táblák, valamint **beszúrása**, **lekérdezése**, **módosítása**, és **törlése** entitások egy tábla. További tudnivalókat lásd: a [további lépések](#Next-Steps) szakasz.

> [!NOTE]
> Az SDK nem elérhető a fejlesztők számára, akik Azure Storage Android-eszközökön. További információkért lásd: a [Azure Storage SDK for Android][Azure Storage SDK for Android].
>

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
Ez az útmutató a tárolási szolgáltatásokkal, amelyek helyben, vagy a webes szerepkör vagy a feldolgozói szerepkör az Azure-ban futó belül Java-alkalmazások futtatása fogja használni.

Ehhez szüksége lesz a Java fejlesztői készlet (JDK) telepítése, és hozzon létre egy Azure storage-fiókot az Azure-előfizetéshez. Ha ezzel végzett, akkor győződjön meg arról, hogy a fejlesztési rendszer megfelel-e a minimális követelményeit és függőségeit, amelyek szerepelnek a [Azure Storage SDK for Java] [ Azure Storage SDK for Java] GitHub tárházából. Ha a rendszer megfelel ezeknek a követelményeknek, akkor is kövesse letöltése és telepítése az Azure Storage szalagtárak Java ebből a rendszeren. Ezek a feladatok befejezése után lesz ebben a cikkben a példák használó Java-alkalmazás létrehozása.

## <a name="configure-your-application-to-access-table-storage"></a>Állítsa be az alkalmazását, a table storage eléréséhez
Vegye fel a következő importálási utasításokat a felső részén a Java-fájlt, amelyre a Microsoft Azure storage API-kkal táblák eléréséhez használható:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure storage kapcsolati karakterlánc beállítása
Egy Azure storage-ügyfél egy tárolási kapcsolati karakterlánc végpontok és adatok szolgáltatások eléréséhez szükséges hitelesítő adatok tárolására használ. Ha egy ügyfél-alkalmazás fut, meg kell adnia a tárolási kapcsolati karakterlánc a következő formátumban a tárfiók nevével, és a tárfiók elsődleges elérési kulcs szerepel az [Azure-portálon](https://portal.azure.com) a a *AccountName* és *AccountKey* értékek. Ez a példa bemutatja, hogyan deklarálhatnak ahhoz, hogy a kapcsolati karakterlánc statikus mezőben:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

Egy alkalmazás fut, a Microsoft Azure-ban a szerepkörön belüli, az ezt a karakterláncot tárolhatja a konfigurációs fájlban, *ServiceConfiguration.cscfg*, és hívja érhető el a **RoleEnvironment.getConfigurationSettings** metódust. Íme egy példa a kapcsolati karakterlánc beolvasása egy **beállítás** nevű elem *StorageConnectionString* a konfigurációs fájlban:

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

A következő minták azt feltételezik, hogy használt két módszer közül egyik beolvasni a tárolási kapcsolati karakterlánc.

## <a name="how-to-create-a-table"></a>Útmutató: a tábla létrehozása
A **CloudTableClient** objektum lehetővé teszi, hogy a hivatkozás objektum lekérése a táblákat és entitásokat. Az alábbi kód létrehoz egy **CloudTableClient** objektumot, és hozzon létre egy új használja **CloudTable** objektum egy tábla nevű, "személyek". 

> [!NOTE]
> Nincsenek további módon hozhat létre **CloudStorageAccount** objektumok; további információkért lásd: **CloudStorageAccount** a a [Azure Storage ügyfél SDK-dokumentáció].)
>

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create the table if it doesn't exist.
    String tableName = "people";
    CloudTable cloudTable = tableClient.getTableReference(tableName);
    cloudTable.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-list-the-tables"></a>Hogyan: táblák listázása
Ahhoz, hogy a táblák listáját, hívja az **CloudTableClient.listTables()** metódusának segítéségével lekérheti az táblanevek iterable listája.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Loop through the collection of table names.
    for (String table : tableClient.listTables())
    {
        // Output each table name.
        System.out.println(table);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-add-an-entity-to-a-table"></a>Hogyan: vehető fel olyan entitás egy táblához
Entitások leképezése egy egyéni osztály végrehajtási Java objektumok **TableEntity**. Kényelmi célokat szolgál a **TableServiceEntity** osztály megvalósítja **TableEntity** és képezze le a tulajdonságait a Tulajdonságok nevű elérő és beállító metódusok reflexió használatával. Egy entitás egy táblához hozzáadásához először létre kell hoznia egy osztály, amely az entitás tulajdonságait határozza meg. Az alábbi kód meghatároz egy entitásosztályt, amely az ügyfél keresztnevét használja, mint a sorkulcs, vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban azokat a különböző partíciókulcsúak lehet lekérdezni.

```java
public class CustomerEntity extends TableServiceEntity {
    public CustomerEntity(String lastName, String firstName) {
        this.partitionKey = lastName;
        this.rowKey = firstName;
    }

    public CustomerEntity() { }

    String email;
    String phoneNumber;

    public String getEmail() {
        return this.email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return this.phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
}
```

Entitások érintő tábla működéséhez szükségesek egy **TableOperation** objektum. Ezt az objektumot határozza meg a művelet végrehajtását entitás, amely kell végrehajtani, egy **CloudTable** objektum. Az alábbi kód létrehoz egy új példányt a **CustomerEntity** osztály az egyes ügyféladatokat kell tárolni. A következő kód hívások **TableOperation.insertOrReplace** létrehozásához egy **TableOperation** objektum entitás beszúrása egy táblát, és hozzárendeli az új **CustomerEntity** vele. Végezetül a kódja meghívja a **hajtható végre** metódust a a **CloudTable** objektum, megadva a "felhasználók" táblázat és az új **TableOperation**, amely ezután kérést küld a társzolgáltatás az új ügyfél entitás beszúrása a "felhasználók" tábla, vagy cserélje le az entitás, ha már létezik.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insertOrReplace(customer1);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer1);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-a-batch-of-entities"></a>Útmutató: egy teljes entitásköteget beszúrása
Egyetlen írási művelettel a table szolgáltatásba entitásköteget is beszúrhat. Az alábbi kód létrehoz egy **TableBatchOperation** objektumot, majd hozzáadja a három a beszúrási műveletek rá. Minden egyes végrehajtott beszúrási művelet hozzáadott új forrásentitás-objektum létrehozása, annak értékét, és majd hívja a **beszúrása** metódust a **TableBatchOperation** objektum az entitás egy új insert művelethez társítható. Ezután a kód hívások **hajtható végre** a a **CloudTable** objektum, adja meg a "felhasználók" tábla és a **TableBatchOperation** objektum, amely a köteg tábla műveletek küld a társzolgáltatás az egy kérelemhez.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insertOrReplace(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insertOrReplace(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insertOrReplace(customer3);

    // Execute the batch of operations on the "people" table.
    cloudTable.execute(batchOperation);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

A kötegműveletekkel kapcsolatban ügyeljen a következőkre:

* Hajtsa végre az akár 100 insert, törölheti, egyesíteni, cserélje le, Beszúrás vagy egyesítéséhez és beszúrása vagy csereműveletek bármilyen kombinációban egyetlen kötegben.
* A kötegelt művelet lehet a beolvasási műveletet, ha a köteg egyetlen művelete.
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.
* A kötegelt művelet egy 4MB adattartalom korlátozódik.

## <a name="how-to-retrieve-all-entities-in-a-partition"></a>Útmutató: egy partíció összes entitásának lekérése
Ha egy táblából egy partíció entitások, használhatja a **TableQuery**. Hívás **TableQuery.from** egy adott táblán, amely visszaadja a megadott típusú lekérdezés létrehozásához. A következő kódot megad egy szűrőt, ahol a "Smith" a partíciókulcs az entitások. **TableQuery.generateFilterCondition** van egy segédmetódust a lekérdezések-szűrők létrehozásához. Hívás **ahol** által visszaadott hivatkozás a a **TableQuery.from** módszer a szűrő alkalmazása a lekérdezést. Ha a lekérdezés végrehajtása hívja **hajtható végre** a a **CloudTable** objektumot ad vissza egy **iterátor** rendelkező a **CustomerEntity** eredménye a megadott típus. Ezután a **iterátor** vissza egy for-each ciklus felhasználásához, az eredményeket. Ez a kód kiírja a mezőket a konzolba a lekérdezés eredményében.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from(CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : cloudTable.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-range-of-entities-in-a-partition"></a>Útmutató: egy partíció entitástartományának lekérése
Ha nem szeretné egy partíció összes entitást lekérdezni, megadhat egy tartományt a szűrőben lévő összehasonlító operátorok használatával. Az alábbi kód két szűrőket, hogy a "Smith", ahol a sorkulcs (Keresztnév) legfeljebb "E" betűvel kezdődik partíció összes entitásának lekérése az ábécé egyesíti. Majd megjeleníti a lekérdezés eredményeit. Ha használja az entitásokat, adja hozzá a kötegben táblázathoz című szakaszában talál, csak két olyan entitásra visszaadott (Ben és Denise Smith); Jeff Smith nincs megadva.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String TIMESTAMP = "Timestamp";

    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        PARTITION_KEY,
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        ROW_KEY,
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter,
        Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from(CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : cloudTable.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() +
            " " + entity.getRowKey() +
            "\t" + entity.getEmail() +
            "\t" + entity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-retrieve-a-single-entity"></a>Hogyan: egyetlen entitás lekérdezése
Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kódot a hívások **TableOperation.retrieve** partícióból kulcs vagy sor kulcs paramétereket adja meg a "Jeff Smith" létrehozása helyett egy **TableQuery** és hajtsa végre ugyanezt a szűrők használatával. Végrehajtásakor a beolvasási műveletet egy gyűjtemény helyett csak egyetlen entitást ad vissza. A **getResultAsType** metódus árnyékot a hozzárendelés cél típusának eredménye egy **CustomerEntity** objektum. Ha ez a típus nem kompatibilis a lekérdezéshez megadott típus, egy kivételt fog jelezni. Null értékű ad vissza, ha nem entitás egy pontos partíció- és sorkulcsa felel meg. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getEmail() +
            "\t" + specificEntity.getPhoneNumber());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-modify-an-entity"></a>Hogyan: entitás módosítása
Entitás módosítása lekéréséhez a table szolgáltatásból, módosítja a forrásentitás-objektum, és menti a módosításokat vissza az a table szolgáltatás egy áthelyezési vagy merge műveletet. A következő kód egy meglévő ügyfél telefonszámát módosítja. Telefonhívás helyett **TableOperation.insert** beszúrása megszokott azt meghívja-e ezt a kódot **TableOperation.replace**. A **CloudTable.execute** metódus meghívja a table szolgáltatás, és az entitás helyébe, kivéve, ha egy másik alkalmazás módosított azt az időt mivel ez az alkalmazás azt lekérése. Ebben az esetben kivételt vált ki, és az entitás kell beolvasni, módosíthatók, és újra menti. Egyidejű hozzáférések optimista újrapróbálkozási zárolás közös az olyan elosztott tárolórendszer.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff =
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    cloudTable.execute(replaceEntity);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-query-a-subset-of-entity-properties"></a>Útmutató: az Entitástulajdonságok egy részének lekérdezése
A lekérdezés egy táblához pár tulajdonságok kérhetnek le egy entitás. Ez a leképezésnek hívott technika csökkenti a sávszélesség felhasználását, és javítja a lekérdezési teljesítményt, főleg a nagy entitások esetében. A lekérdezés a következő kódrészlet használja a **válasszon** metódus csak az e-mail címek entitások vissza a táblában. Az eredmények gyűjteménye van képezik le **karakterlánc** segítségével. egy **EntityResolver**, amelynek szerepe, hogy a kiszolgáló által visszaadott entitásokat a típuskonverziós. A leképezési kapcsolatos részletesebb [Azure táblák: Introducing Upsert és Query Projection][Azure Tables: Introducing Upsert and Query Projection]. Vegye figyelembe, hogy nem támogatja a helyi tárterület-emulátor, így a kód csak akkor, ha a table szolgáltatás fiók használatával.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery =
        TableQuery.from(CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp, HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString :
        cloudTable.execute(projectionQuery, emailResolver)) {
            System.out.println(projectedString);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-insert-or-replace-an-entity"></a>Hogyan: beszúrása vagy entitás cseréje
Gyakran hozzáadandó entitást egy táblához anélkül, hogy tudnák, ha a tábla már létezik. Egy beszúrása vagy lecserélése művelet lehetővé teszi egyetlen kérelem, amelyet az entitás szúrnak, ha nem létezik vagy lecserélheti a meglévőt, ellenkező esetben. Előzetes példák építve, az alábbi kód beszúrása vagy az entitás "Walter grönlandi" a felváltja. Miután létrehozta az új entitás, ez a kód meghívja a **TableOperation.insertOrReplace** metódust. Ez a kód majd meghívja a **hajtható végre** a a **CloudTable** a tábla és a Beszúrás objektumot, vagy cserélje le a tábla műveletet, mert a paraméterek. Egy entitás csak egy része frissíteni a **TableOperation.insertOrMerge** módszer is lehet használni. Vegye figyelembe, hogy beszúrása vagy lecserélése nem támogatott a helyi storage emulator, így a kód csak akkor, ha a table szolgáltatás fiók használatával. További információ a beszúrása vagy lecserélése és beszúrási vagy-egyesítéses ezen tudhat [Azure táblák: Introducing Upsert és Query Projection][Azure Tables: Introducing Upsert and Query Projection].

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    cloudTable.execute(insertCustomer5);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-an-entity"></a>Hogyan: entitás törlése
Egy entitás azt beolvasása után egyszerűen törölheti. Az entitás beolvasott, ha hívása **TableOperation.delete** törli az entitáshoz. Majd meghívják a **hajtható végre** a a **CloudTable** objektum. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Create a cloud table object for the table.
    CloudTable cloudTable = tableClient.getTableReference("people");

    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
        cloudTable.execute(retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    cloudTable.execute(deleteSmithJeff);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="how-to-delete-a-table"></a>Hogyan: tábla törlése
Végezetül a következő kódot törölhető egy tábla a tárfiókból. Egy tábla már törölt újból létre kell hozni egy ideig, a törlés, körülbelül 40 másodpercig általában kevesebb, mint a következő érhető el.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();

    // Delete the table and all its data if it exists.
    CloudTable cloudTable = tableClient.getTableReference("people");
    cloudTable.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```
[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>További lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Az Azure Storage Java SDK][Azure Storage SDK for Java]
* [Az Azure Storage ügyfél SDK-dokumentáció][Azure Storage ügyfél SDK-dokumentáció]
* [Az Azure Storage REST API-n][Azure Storage REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

További információ: [Azure Java-fejlesztőknek](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyfél SDK-dokumentáció]: http://dl.windowsazure.com/storage/javadoc/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
