---
title: Hogyan használható az Azure Table storage vagy az Azure Cosmos DB Table API a Javával
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: d7be0ed8d59063f75b3d4fadbf69237bdb2d0d13
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863686"
---
# <a name="how-to-use-azure-table-storage-or-azure-cosmos-db-table-api-from-java"></a>Az Azure Table Storage és az Azure Cosmos DB Table API használata a Java segítségével
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Áttekintés
Ez cikk bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Table Storage szolgáltatás és az Azure Cosmos DB használatával. A kódminták Java nyelven íródtak, és a [Java-hoz készült Azure Storage SDK-t][Azure Storage SDK for Java] használják. Az ismertetett forgatókönyvek a táblák **létrehozásával**, **listázásával** és **törlésével**, valamint a táblákban szereplő entitások **beszúrásával**, **lekérdezésével**, **módosításával** és **törlésével** foglalkoznak. A táblákkal kapcsolatos további információkért lásd a [További lépések](#next-steps) szakaszt.

> [!NOTE]
> Egy SDK elérhető az Azure Storage-et Android-eszközökön használó fejlesztők számára. További információk: [Androidhoz készült Azure Storage SDK][Azure Storage SDK for Android].
>

## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
Az útmutatóban olyan tárolási szolgáltatásokat fog használni, amelyek futtathatók helyileg egy Java-alkalmazásban, vagy egy Azure-beli webes szerepkörben vagy feldolgozói szerepkörben futó kódban.

A cikkben szereplő minták használatához telepítse a Java fejlesztői készletet (JDK-t), majd hozzon létre egy Azure Storage-fiókot vagy egy Azure Cosmos DB-fiókot Azure-előfizetésében. Ha ezzel végzett, ellenőrizze, hogy az Ön által használt fejlesztői rendszer megfelel-e a [Java-hoz készült Azure Storage SDK][Azure Storage SDK for Java] GitHub-adattárban felsorolt minimális követelményeknek, illetve függőségeknek. Ha az Ön által használt rendszer megfelel az említett követelményeknek, kövesse a Java-hoz készült Azure Storage-kódtárak adattárból való letöltésére és az Ön rendszerén történő telepítésére vonatkozó utasításokat. Ha elvégezte ezeket a feladatokat, létrehozhat egy Java-alkalmazást, amely a cikkben szereplő példákat használja.

## <a name="configure-your-application-to-access-table-storage"></a>Az alkalmazás konfigurálása a Table Storage elérésére
Adja hozzá a következő importálási utasításokat a Java-fájl elejéhez, ahol használni szeretné az Azure Storage API-kat vagy az Azure Cosmos DB Table API-t a táblák eléréséhez:

```java
// Include the following imports to use table APIs
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.table.*;
import com.microsoft.azure.storage.table.TableQuery.*;
```

## <a name="add-an-azure-storage-connection-string"></a>Azure Storage-kapcsolati sztring hozzáadása
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. Ügyfélalkalmazásban történő futtatáskor a tárolási kapcsolati sztringet a következő formátumban kell megadnia: Használja a Storage-fiók nevét és az [Azure Portalon](https://portal.azure.com) megtalálható elsődleges hozzáférési kulcsát az *AccountName* és *AccountKey* értékeként. 

Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:

```java
// Define the connection-string with your values.
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

## <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Azure Cosmos DB Table API kapcsolati sztringjének hozzáadása
Az Azure Cosmos DB-fiókok kapcsolati sztringet használnak a táblavégpontok és a hitelesítő adatok tárolásához. Ügyfélalkalmazásban történő futtatáskor az Azure Cosmos DB kapcsolati sztringjét a következő formátumban kell megadnia: Használja az Azure Cosmos DB-fiók nevét és az [Azure Portalon](https://portal.azure.com) megtalálható elsődleges hozzáférési kulcsát az *AccountName* és *AccountKey* értékeként. 

Ez a példa bemutatja, hogyan deklarálhat statikus mezőt az Azure Cosmos DB kapcsolati sztring tárolására:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Egy Azure-beli szerepkörön belül futó alkalmazás esetén ezt a sztringet a *ServiceConfiguration.cscfg* szolgáltatáskonfigurációs fájlban tárolhatja, és a  **RoleEnvironment.getConfigurationSettings** metódus meghívásával férhet hozzá. A következő példa bemutatja, hogyan kérhető le egy kapcsolati sztring egy *StorageConnectionString* nevű **Beállítási** elemből, amely a szolgáltatáskonfigurációs fájlban található.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

A kapcsolati sztringet a projekt config.properties fájljában is tárolhatja:

```java
StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

Az alábbi minták azt feltételezik, hogy a tárolási kapcsolati sztringet ezen két metódus egyikével kérte le.

## <a name="create-a-table"></a>Tábla létrehozása
A **CloudTableClient** objektum lehetővé teszi táblák és entitások referenciaobjektumainak lekérését. Az alábbi kód egy **CloudTableClient** objektumot hoz létre, és egy új **CloudTable** objektum létrehozásához használja, amely a „people” nevű táblát képviseli. 

> [!NOTE]
> A **CloudStorageAccount** objektumok más módon is létrehozhatók. További információkért lásd az [Azure Storage ügyféloldali SDK-referencia] **CloudStorageAccount** című részét.
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

## <a name="list-the-tables"></a>A táblák listázása
A táblák listájának lekéréséhez hívja meg a **CloudTableClient.listTables()** metódust, amellyel a táblák neveinek iterálható listáját kérheti le.

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

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Az entitásokat a rendszer Java-objektumokra képezi le egy, a **TableEntity** osztályt implementáló egyéni osztály használatával. Kényelmi okokból a **TableServiceEntity** osztály a **TableEntity** osztályt implementálja, és tükröződés segítségével képezi le a tulajdonságokat a tulajdonságokhoz elnevezett beolvasó és beállító metódusokra. Ha hozzá szeretne adni egy entitást egy táblához, először hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Az alábbi kód meghatároz egy entitásosztályt, amely az ügyfél keresztnevét használja sorkulcsként és a vezetéknevét partíciókulcsként. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint az eltérő partíciókulcsúak.

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

Az entitásokat érintő táblaműveletekhez egy **TableOperation** objektumra van szükség. Ez az objektum határozza meg az entitáson elvégzendő műveletet, amely egy **CloudTable** objektummal hajtható végre. Az alábbi kód a **CustomerEntity** osztály egy új példányát hozza létre, néhány tárolandó ügyféladattal. A kód ezután meghívja a **TableOperation.insertOrReplace** metódust egy **TableOperation** objektum létrehozására, amely egy entitást szúr be egy táblába, és társítja hozzá az új **CustomerEntity** entitást. Végül a kód meghívja az **execute** metódust a **CloudTable** objektumra vonatkozóan a „people” tábla és az új **TableOperation** objektum megadásával, ami pedig az új ügyfélentitás a „people” tábla történő beszúrásra, vagy ha létezik az entitás, annak cseréjére irányuló kérést küld a tárolási szolgáltatásnak.

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

## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Egyetlen írási művelettel egy teljes entitásköteget is beszúrhat a Table Service-be. Az alábbi kód egy **TableBatchOperation** objektumot hoz létre, majd hozzáad három beszúrási műveletet. Az egyes beszúrási műveletek a következőképpen adhatók hozzá: létre kell hozni egy új entitásobjektumot, be kell állítani az értékeit, majd meg kell hívni az **insert** metódust a **TableBatchOperation** objektumra vonatkozóan az entitás társításához egy új beszúrási művelettel. Ezután a kód meghívja az **execute** metódust a **CloudTable** objektumra vonatkozóan a „people” tábla és a **TableBatchOperation** objektum megadásával, az pedig egyetlen kérésben küldi el a kötegelt táblaműveleteket a tárolási szolgáltatásnak.

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

Kötegelt műveletek esetében ügyeljen a következőkre:

* Akár 100 beszúrás, törlés, egyesítés, csere, beszúrás-vagy-egyesítés és beszúrás-vagy-csere műveletet is végrehajthat, bármilyen kombinációban, egyetlen kötegben.
* A kötegművelet rendelkezhet lekérési művelettel, ha ez a kötegben található egyetlen művelet.
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.
* A kötegműveletek 4 MB nagyságú hasznos adatra vannak korlátozva.

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Ha egy táblából egy partíció összes entitását le szeretné kérdezni, használjon **TableQuery** objektumot. Hívja meg a **TableQuery.from** metódust egy adott táblán, amely a megadott típusú eredményt adja vissza. Az alábbi kód egy szűrőt ad meg a „Smith” partíciókulcsú entitásokra. A **TableQuery.generateFilterCondition** egy segédmetódus a lekérdezésszűrők létrehozásához. A szűrő a lekérdezésre való alkalmazásához hívja meg a **where** záradékot a **TableQuery.from** metódus által visszaadott referenciára vonatkozóan. Ha a lekérdezést úgy hajtja végre, hogy az **execute** metódust a **CloudTable** objektumon hívja meg, akkor az egy **iterátort** ad vissza a **CustomerEntity** eredménytípussal. Ezután a hurkokhoz visszaadott **iterátort** az eredmények feldolgozásához használhatja. A kód megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

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

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése
Ha nem szeretné az összes entitást lekérdezni egy partícióból, megadhat egy tartományt összehasonlító operátorok használatával egy szűrőben. Az alábbi kód két szűrő kombinálásával kéri le az összes olyan entitást a „Smith” partícióból, amelyeknél a sorkulcs (keresztnév) az ábécében az „E”-ig tartó betűk egyikével kezdődik. Ezután kinyomtatja a lekérdezés eredményeit. Ha az útmutató kötegelt beszúrással foglalkozó szakaszában a táblához adott entitásokat használja, a rendszer most csak két entitást ad vissza (Ben és Denise Smith), Jeff Smith nem szerepel benne.

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

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kód a **TableOperation.retrieve** metódust hívja meg, és partíciókulcs- vagy sorkulcsparaméterekkel adja meg a „Jeff Smith” nevű ügyfelet, ahelyett hogy létrehozna egy **TableQuery** objektumot, és szűrők használatával érné el ugyanezt. A lekérési művelet a végrehajtásakor egy gyűjtemény helyett csak egyetlen entitást ad vissza. A **getResultAsType** metódus az eredményt a hozzárendelési cél (egy **CustomerEntity** objektum) típusának megfelelően alakítja át. Ha ez a típus nem kompatibilis a lekérdezéshez megadott típussal, a rendszer kivételt ad vissza. A rendszer nullértéket ad vissza, ha egyetlen entitás partíció- és sorkulcsa sem egyezik pontosan. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.

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

## <a name="modify-an-entity"></a>Entitás módosítása
Ha módosítani szeretne egy entitást, kérje le a Table Service-ből, módosítsa az entitásobjektumot, majd egy csere vagy egyesítés művelettel mentse a módosításokat a Table Service-be. A következő kód egy meglévő ügyfél telefonszámát módosítja. A beszúrás művelet esetén használt **TableOperation.insert** metódus meghívása helyett ez a kód a **TableOperation.replace** metódust hívja meg. A **CloudTable.execute** metódus meghívja a Table Service-t, és lecseréli az entitást, hacsak egy másik alkalmazás nem módosította azóta, hogy az alkalmazás lekérte. Ebben az esetben a rendszer kivételt ad vissza, és az entitás lekérését, módosítását és mentését újra el kell végezni. Ez az optimista egyidejűségi újrapróbálkozási mintázat elosztott tárolórendszerekben gyakori.

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

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
Egy táblalekérdezéssel egy entitásnak csak bizonyos tulajdonságait is lekérdezheti. Ez a leképezésnek hívott technika csökkenti a sávszélesség felhasználását, és javítja a lekérdezési teljesítményt, főleg a nagy entitások esetében. Az alábbi kódban szereplő lekérdezés a **select** metódust használja a táblában található entitások e-mail-címének lekérdezéséhez. Az eredményeket a rendszer egy **EntityResolver** objektum segítségével egy **sztring** gyűjteménybe képezi le, amely a típuskonverziót a kiszolgálótól érkező entitásokon hajtja végre. A leképezésről az [Azure-táblákat, valamint az upsert (frissítés/beszúrás) műveletet és a lekérdezésleképezést][Azure Tables: Introducing Upsert and Query Projection] bemutató blogbejegyzésből tudhat meg többet. A helyi Storage Emulator nem támogatja a leképezést, így a kód csak a Table Service-fiók használatával működik.

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

## <a name="insert-or-replace-an-entity"></a>Entitás beszúrása vagy cseréje
Gyakran előfordulhat, hogy egy entitást anélkül szeretne hozzáadni egy táblához, hogy tudná, az létezik-e már a táblában. A beszúrás-vagy-csere művelettel egyetlen kéréssel elvégezheti mindezt, amely beszúrja az entitást, ha az nem létezik, és lecseréli a meglévőt, ha már létezik. A korábbi példák alapján az alábbi kód beszúrja vagy lecseréli „Walter Harp” entitását. Az új entitás létrehozását követően ez a kód meghívja a **TableOperation.insertOrReplace** metódust. Ez a kód meghívja az **execute** metódust a **CloudTable** objektumra vonatkozóan, amelynek paraméterei a tábla és a beszúrás-vagy-csere táblaművelet lesznek. Ha egy entitásnak csak egy részét szeretné frissíteni, használhatja ehelyett a **TableOperation.insertOrMerge** metódust. A helyi Storage Emulator nem támogatja a beszúrás-vagy-csere műveletet, így a kód csak a Table Service-fiók használatával működik. A beszúrás-vagy-csere és a beszúrás-vagy-egyesítés műveletről az [Azure-táblákat, valamint az upsert (frissítés/beszúrás) műveletet és a lekérdezésleképezést][Azure Tables: Introducing Upsert and Query Projection] bemutató blogbejegyzésből tudhat meg többet.

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

## <a name="delete-an-entity"></a>Entitás törlése
A lekérés után egyszerűen törölheti az entitásokat. Az entitás lekérése után hívja meg a **TableOperation.delete** metódust az entitás törléséhez. Ezután hívja meg az **execute** metódust a **CloudTable** objektumra vonatkozóan. Az alábbi kód lekérdez, majd töröl egy ügyfélentitást.

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

## <a name="delete-a-table"></a>Tábla törlése
Végül az alábbi kóddal törölhető egy tábla a tárfiókból. A tábla törlése után körülbelül 40 másodpercig nem tudja ismét létrehozni azt. 

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

* [Bevezetés az Azure Table Service használatába Javában](https://github.com/Azure-Samples/storage-table-java-getting-started)
* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [A Javához készült Azure Storage SDK][Azure Storage SDK for Java]
* [Azure Storage ügyféloldali SDK-referencia][Azure Storage ügyféloldali SDK-referencia]
* [Azure Storage REST API][Azure Storage REST API]
* [Az Azure Storage csapat blogja][Azure Storage Team Blog]

További információ: [Azure Java-fejlesztőknek](/java/azure).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Azure Storage SDK for Java]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure Storage ügyféloldali SDK-referencia]: http://azure.github.io/azure-storage-java/
[Azure Storage REST API]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Azure Tables: Introducing Upsert and Query Projection]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
