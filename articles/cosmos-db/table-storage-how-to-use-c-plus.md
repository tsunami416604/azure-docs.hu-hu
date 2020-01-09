---
title: Az Azure Table Storage és a Azure Cosmos DB Table APIC++
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: wmengmsft
ms.author: wmeng
ms.openlocfilehash: 5df344b3f9f3d2fc2ff6fa65667039c545b70841
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441184"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Az Azure Table Storage és az Azure Cosmos DB Table API használata a C++ nyelvvel

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Ez az útmutató a gyakori forgatókönyveket mutatja be az Azure Table Storage szolgáltatás vagy a Azure Cosmos DB Table API használatával. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. Ez a cikk a következő forgatókönyveket ismerteti:

* Tábla létrehozása és törlése
* Tábla entitások használata

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Az ajánlott verzió a Storage ügyféloldali kódtár 2.2.0, amely a [NuGet](https://www.nuget.org/packages/wastorage) vagy a [GitHub](https://github.com/Azure/azure-storage-cpp/)használatával érhető el.
>

## <a name="create-accounts"></a>Fiókok létrehozása

### <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása

Ebben az útmutatóban egy C++ alkalmazás tárolási funkcióit használja. Ehhez telepítse az Azure Storage ügyféloldali kódtárat a következőhöz C++:.

Az Azure Storage ügyféloldali Kódtárajának C++telepítéséhez használja az alábbi módszereket:

* **Linux:** Kövesse az [Azure Storage ügyféloldali kódtár C++ útmutatásait: első lépések Linuxon](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) lapon.
* **Windows:** Windows rendszeren használja a [vcpkg](https://github.com/microsoft/vcpkg) -t a függőség-kezelőként. A vcpkg inicializálásához [kövesse az első](https://github.com/microsoft/vcpkg#quick-start) lépéseket. Ezután használja a következő parancsot a könyvtár telepítéséhez:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

A következő útmutatóból megtudhatja, hogyan hozhatja létre a forráskódot, és hogyan exportálhat Nuget a [readme](https://github.com/Azure/azure-storage-cpp#download--install) fájlban.

### <a name="configure-access-to-the-table-client-library"></a>A Table ügyféloldali kódtárhoz való hozzáférés konfigurálása

Ha az Azure Storage API-kat szeretné használni a táblák eléréséhez, adja hozzá a következő `include` utasításokat C++ a fájl elejéhez:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Az Azure Storage-ügyfél és a Cosmos DB-ügyfél egy kapcsolati sztringet használ a végpontok és az adatkezelési szolgáltatások elérésére szolgáló hitelesítő adatok tárolásához. Ügyfélalkalmazás futtatásakor meg kell adnia a tárolási kapcsolódási karakterláncot, vagy a megfelelő formátumban kell megadnia a Azure Cosmos DB a kapcsolódási karakterláncot.

### <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage kapcsolati sztring beállítása

Ebből a példából megtudhatja, hogyan deklarálhat egy statikus mezőt az Azure Storage kapcsolódási karakterláncának tárolásához:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

`<your_storage_account>`éhez használja a Storage-fiókjának nevét. < Your_storage_account_key > esetében használja a [Azure Portalban](https://portal.azure.com)felsorolt Storage-fiók elérési kulcsát. A Storage-fiókokról és a hozzáférési kulcsokról a [Storage-fiók létrehozása](../storage/common/storage-create-storage-account.md)című témakörben olvashat bővebben.

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Azure Cosmos DB kapcsolati sztring beállítása

Ebből a példából megtudhatja, hogyan deklarálhat egy statikus mezőt a Azure Cosmos DB kapcsolódási karakterláncának tárolásához:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

`<your_cosmos_db_account>`hoz használja a Azure Cosmos DB fiókjának nevét. Adja meg `<your_cosmos_db_account_key>`elsődleges kulcsát. Adja meg a `<your_cosmos_db_endpoint>`[Azure Portalban](https://portal.azure.com) felsorolt végpontot.

Az alkalmazás helyi Windows-alapú számítógépeken való teszteléséhez használhatja az Azure [SDK](https://azure.microsoft.com/downloads/)-val telepített Azure Storage emulatort is. A Storage Emulator egy olyan segédprogram, amely a helyi fejlesztői gépen elérhető Azure Blob, Queue és Table szolgáltatást szimulálja. Az alábbi példa bemutatja, hogyan deklarálhat egy statikus mezőt a kapcsolódási karakterláncnak a helyi tároló emulátorhoz való tárolásához:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure Storage-emulátor elindításához a Windows asztaláról válassza a **Start** gombot vagy a Windows-kulcsot. *Microsoft Azure Storage Emulator*megadása és futtatása. További információkért lásd: [Fejlesztés és tesztelés az Azure Storage Emulatorral](../storage/common/storage-use-emulator.md).

### <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése

A Storage-fiók adatainak megjelenítéséhez használhatja a `cloud_storage_account` osztályt. A Storage-fiók adatainak a Storage-kapcsolódási karakterláncból való lekéréséhez használja a `parse` metódust.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután szerezzen be egy `cloud_table_client` osztályra mutató hivatkozást. Ez az osztály lehetővé teszi a Table Storage szolgáltatásban tárolt táblák és entitások hivatkozási objektumainak lekérését. A következő kód egy `cloud_table_client` objektumot hoz létre a korábban beolvasott Storage Account objektum használatával:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Entitások létrehozása és hozzáadása egy táblához

### <a name="create-a-table"></a>Tábla létrehozása

A `cloud_table_client` objektumok lehetővé teszi a táblák és entitások hivatkozási objektumainak beolvasását. A következő kód létrehoz egy `cloud_table_client` objektumot, és felhasználja egy új tábla létrehozásához.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy új `table_entity` objektumot, és adja át a `table_operation::insert_entity`nak. Az alábbi kód az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az ugyanazzal a partíciós kulccsal rendelkező entitások gyorsabban kérhetők le, mint a különböző partíciós kulcsokkal rendelkező entitások. A különböző partíciós kulcsok használata lehetővé teszi a párhuzamos működés nagyobb mértékű méretezhetőségét. További információ: [A Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistája](../storage/common/storage-performance-checklist.md).

A következő kód a `table_entity` egy új példányát hozza létre, és néhány vásárlói adattal együtt tárolja azokat. A következő hívások `table_operation::insert_entity` egy `table_operation` objektum létrehozásához, amely beszúr egy entitást egy táblába, és társítja az új tábla entitást. Végül a kód meghívja a `execute` metódust a `cloud_table` objektumon. Az új `table_operation` egy kérést küld a Table servicenak az új ügyfél entitásnak a `people` táblába való beszúrásához.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása

Egyetlen írási művelettel egy teljes entitásköteget is beszúrhat a Table Service-be. A következő kód létrehoz egy `table_batch_operation` objektumot, majd három beszúrási műveletet hoz létre hozzá. Az egyes beszúrási műveletek új entitás-objektum létrehozásával, az értékek megadásával, majd a `table_batch_operation` objektum `insert` metódusának meghívásával társíthatók az entitást egy új beszúrási művelettel. Ezután a kód meghívja a `cloud_table.execute` a művelet futtatásához.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Kötegelt műveletek esetében ügyeljen a következőkre:

* Legfeljebb 100 `insert`, `delete`, `merge`, `replace`, `insert-or-merge`és `insert-or-replace` műveletet végezhet egyetlen köteg bármely kombinációjában.  
* Egy batch-művelet lekéréses művelettel rendelkezhet, ha ez az egyetlen művelet a kötegben.  
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.  
* A kötegműveletek 4 MB nagyságú hasznos adatra vannak korlátozva.  

## <a name="query-and-modify-entities"></a>Entitások lekérdezése és módosítása

### <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése

Egy partíció összes entitásához tartozó tábla lekérdezéséhez használjon `table_query` objektumot. A következő mintakód olyan entitások szűrőjét határozza meg, amelyekben `Smith` a partíciós kulcs. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.  

> [!NOTE]
> Ezek a metódusok jelenleg nem támogatottak a C++ nyelv esetén az Azure Cosmos DB szolgáltatásban.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

Az ebben a példában szereplő lekérdezés az összes olyan entitást adja vissza, amelyek megfelelnek a szűrési feltételeknek. Ha nagy táblákkal rendelkezik és gyakran le kell töltenie a táblaentitásokat, javasoljuk, hogy adatait inkább Azure Storage-blobokban tárolja.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése

Ha nem szeretné lekérdezni egy partíció összes entitását, megadhat egy tartományt. A partíciós kulcs szűrőjét egy sor kulcs szűrővel kombinálja. A következő példa két szűrőt használ a Partition `Smith` összes entitásának beolvasásához, ahol a sor kulcsa (Utónév) az ábécében `E`nál korábbi betűvel kezdődik, majd kinyomtatja a lekérdezés eredményeit.  

> [!NOTE]
> Ezek a metódusok jelenleg nem támogatottak a C++ nyelv esetén az Azure Cosmos DB szolgáltatásban.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése

Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kód a `table_operation::retrieve_entity`t használja az ügyfél `Jeff Smith`megadásához. Ez a metódus csak egyetlen entitást ad vissza, nem egy gyűjteményt, és a visszaadott érték `table_result`. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Entitás cseréje

Ha le szeretne cserélni egy entitást, kérje le a Table Service-ből, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table Service-be. A következő kód egy meglévő ügyfél telefonszámát és e-mail-címét módosítja. A `table_operation::insert_entity`meghívása helyett ez a kód `table_operation::replace_entity`használ. Ez a módszer azt eredményezi, hogy az entitás teljes mértékben le lesz cserélve a kiszolgálón, kivéve, ha a kiszolgáló entitása megváltozott a lekérése óta. Ha megváltozott, a művelet sikertelen lesz. Ez a hiba megakadályozza, hogy az alkalmazás felülírja a beolvasás és a frissítés egy másik összetevővel végzett módosítását. Ennek a hibának a megfelelő kezelése az entitás ismételt beolvasása, ha még érvényes, végezze el a módosításokat, majd hajtson végre egy másik `table_operation::replace_entity` műveletet.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Entitás beszúrása vagy cseréje

`table_operation::replace_entity` művelet meghiúsul, ha az entitás megváltozott a kiszolgálóról való lekérése óta. Emellett először le kell kérnie az entitást a kiszolgálóról ahhoz, hogy a `table_operation::replace_entity` sikeres legyen. Néha nem tudja, hogy az entitás létezik-e a kiszolgálón. A benne tárolt aktuális értékek nem relevánsak, mert a frissítés felülírja őket. Ennek az eredménynek a végrehajtásához használjon `table_operation::insert_or_replace_entity` műveletet. A művelet beszúrja az entitást, ha az nem létezik. Ha létezik, a művelet lecseréli az entitást. A következő példában a `Jeff Smith` ügyfél entitása továbbra is beolvasható, de a `table_operation::insert_or_replace_entity`használatával ment vissza a kiszolgálóra. Az entitáson a lekérési és a frissítési művelet között történt összes módosítás felül lesz írva.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése

Egy táblalekérdezéssel egy entitásnak csak bizonyos tulajdonságait is lekérdezheti. A következő kódban szereplő lekérdezés a `table_query::set_select_columns` metódust használja a tábla entitások e-mail-címeinek visszaadásához.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Egy entitás néhány tulajdonságának lekérdezése hatékonyabb, mint minden tulajdonság lekérése.
>

## <a name="delete-content"></a>Tartalom törlése

### <a name="delete-an-entity"></a>Entitás törlése

Az entitásokat a lekérése után törölheti. Az entitások lekérése után hívja meg a `table_operation::delete_entity`t a törölni kívánt entitással. Ezután hívja meg a `cloud_table.execute` metódust. A következő kód lekérdezi és törli a `Smith` partíciós kulcsával rendelkező entitást `Jeff`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Tábla törlése

Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. Egy törölt tábla nem érhető el újra a törlés után egy ideig.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

A Visual Studio Community Edition esetében, ha a projekt felépítési hibákat tartalmaz a *storage_account. h* és a *table. h*fájl belefoglalása miatt, távolítsa el a **/permissive-** fordítóprogram kapcsolóját:

1. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a projektre, és válassza a **Properties** (Tulajdonságok) elemet.
1. A **Property Pages** (Tulajdonságlapok) párbeszédpanelen bontsa ki a **Configuration Properties** (Konfigurációs tulajdonságok), majd a **C/C++** csomópontot, és válassza a **Language** (Nyelv) elemet.
1. A **Conformance mode** (Megfelelőségi mód) értékét állítsa **No** (Nem) értékűre.

## <a name="next-steps"></a>Következő lépések

A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

Az alábbi hivatkozásokat követve többet is megtudhat az Azure Storage és a Table API Azure Cosmos DB-ben való használatáról:

* [A Table API bemutatása](table-introduction.md)
* [Azure Storage-erőforrások listázása C++-ban](../storage/common/storage-c-plus-plus-enumeration.md)
* [A Storage ügyféloldali kódtára a C++ programnyelvhez – referencia](https://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
