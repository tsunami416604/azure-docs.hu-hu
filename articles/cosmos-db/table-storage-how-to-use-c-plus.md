---
title: Az Azure Table Storage és az Azure Cosmos DB Table API használata C++ segítségével
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: e6d61e329ba91f53b11ace4d258b35950e188dcb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76771215"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Az Azure Table Storage és az Azure Cosmos DB Table API használata a C++ nyelvvel

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Ez az útmutató bemutatja a gyakori forgatókönyvek az Azure Table storage szolgáltatás vagy az Azure Cosmos DB Table API használatával. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. Ez a cikk a következő forgatókönyveket ismerteti:

* Táblázat létrehozása és törlése
* Táblaentitások kal való munka

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Az ajánlott verzió a Storage Client Library 2.2.0, amely a [NuGet](https://www.nuget.org/packages/wastorage) vagy a [GitHub](https://github.com/Azure/azure-storage-cpp/)használatával érhető el.
>

## <a name="create-accounts"></a>Fiókok létrehozása

### <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása

Ebben az útmutatóban c++ alkalmazás tárolási szolgáltatásait használhatja. Ehhez telepítse az Azure Storage Client Library for C++.To do do do to do it, install the Azure Storage Client Library for C++.

Az Azure Storage Client Library for C++ telepítéséhez használja az alábbi módszereket:

* **Linux:** Kövesse az Azure [Storage Ügyféltár C++ README: Első lépések linuxos](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) lapján megadott utasításokat.
* **Windows:** Windows rendszerben használja a [vcpkg](https://github.com/microsoft/vcpkg) függőségkezelőt. Kövesse a [gyorsindítást](https://github.com/microsoft/vcpkg#quick-start) a vcpkg inicializálásához. Ezután a következő paranccsal telepítse a tárat:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

A [README](https://github.com/Azure/azure-storage-cpp#download--install) fájlban útmutatót talál a forráskód létrehozásához és a Nugetbe való exportáláshoz.

### <a name="configure-access-to-the-table-client-library"></a>A Table ügyféloldali kódtárhoz való hozzáférés konfigurálása

Ha az Azure storage API-k at `include` a táblák eléréséhez szeretné használni, adja hozzá a következő állításokat a C++ fájl tetejéhez:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Az Azure Storage-ügyfél és a Cosmos DB-ügyfél egy kapcsolati sztringet használ a végpontok és az adatkezelési szolgáltatások elérésére szolgáló hitelesítő adatok tárolásához. Ügyfélalkalmazás futtatásakor meg kell adnia a tárolási kapcsolat i vagy Az Azure Cosmos DB kapcsolati karakterlánc a megfelelő formátumban.

### <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage kapcsolati sztring beállítása

Ez a példa bemutatja, hogyan deklarálegy statikus mezőt az Azure Storage kapcsolati karakterláncának tárolásához:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Használja a storage-fiók `<your_storage_account>`nevét. A <your_storage_account_key>, használja a hozzáférési kulcsot a Storage-fiók az [Azure Portalon](https://portal.azure.com)felsorolt. A Storage-fiókokról és a hozzáférési kulcsokról a [Tárfiók létrehozása című](../storage/common/storage-create-storage-account.md)témakörben talál további információt.

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Azure Cosmos DB kapcsolati sztring beállítása

Ez a példa bemutatja, hogyan deklarálegy statikus mezőt az Azure Cosmos DB kapcsolati karakterlánc ának tárolására:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Használja az Azure Cosmos DB-fiók nevét. `<your_cosmos_db_account>` Adja meg az `<your_cosmos_db_account_key>`elsődleges kulcsát. Adja meg a végpontot az `<your_cosmos_db_endpoint>` [Azure Portalon.](https://portal.azure.com)

Az alkalmazás helyi Windows-alapú számítógépen történő teszteléséhez az Azure Storage Emulatort használhatja, amely az [Azure SDK-val](https://azure.microsoft.com/downloads/) lett telepítve. A Storage Emulator egy olyan segédprogram, amely a helyi fejlesztői gépen elérhető Azure Blob, Queue és Table szolgáltatást szimulálja. A következő példa bemutatja, hogyan deklarálhatja a statikus mezőt a kapcsolati karakterlánc nak a helyi tárolóemulátorhoz való tárolásához:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulátorának elindításához a Windows asztaláról válassza a **Start** gombot vagy a Windows-kulcsot. Írja be és futtassa a *Microsoft Azure Storage Emulátort.* További információ: [Az Azure storage emulátor használata fejlesztési és tesztelési célokra.](../storage/common/storage-use-emulator.md)

### <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése

Az `cloud_storage_account` osztály segítségével képviselheti a tárfiók adatait. A tárfiók adatainak a tárolási kapcsolati `parse` karakterláncból való lekéréséhez használja a módszert.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután kap egy `cloud_table_client` hivatkozást egy osztályra. Ez az osztály lehetővé teszi a table storage szolgáltatásban tárolt táblák és entitások referenciaobjektumainak begetése. A következő kód `cloud_table_client` a korábban beolvasott tárfiók-objektum használatával hoz létre egy objektumot:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Entitások létrehozása és hozzáadása táblához

### <a name="create-a-table"></a>Tábla létrehozása

Az `cloud_table_client` objektumok lehetővé teszik a táblák és entitások referenciaobjektumainak bekéselését. A következő kód `cloud_table_client` létrehoz egy objektumot, és egy új tábla létrehozásához használja.

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

Ha entitást szeretne hozzáadni egy `table_entity` táblához, hozzon létre egy új objektumot, és adja át a programnak. `table_operation::insert_entity` Az alábbi kód az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulccsal rendelkező entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsokkal rendelkező entitások. A különböző partíciókulcsok használata nagyobb párhuzamos működés-méretezhetőséget tesz lehetővé. További információ: [A Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistája](../storage/common/storage-performance-checklist.md).

A következő kód létrehoz `table_entity` egy új példányt néhány ügyféladatot tárolni. A kód `table_operation::insert_entity` ezután `table_operation` meghívja egy entitást egy táblába való beszúrásához, és hozzákapcsolja az új tábla entitást. Végül a kód `execute` meghívja `cloud_table` az objektum metódusát. Az `table_operation` új kérést küld a Table szolgáltatásnak, `people` hogy szúrja be az új vevő entitást a táblába.  

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

Egyetlen írási művelettel egy teljes entitásköteget is beszúrhat a Table Service-be. A következő kód `table_batch_operation` létrehoz egy objektumot, majd három beszúrási műveletet ad hozzá. Minden beszúrási művelet egy új entitásobjektum létrehozásával, értékeinek `insert` beállításával, `table_batch_operation` majd az objektum metódusának felhívásával adja hozzá az entitást egy új beszúrási művelethez. Ezután a `cloud_table.execute` kód a művelet futtatásához hív.  

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

* Egy kötegben legfeljebb `insert`100 `replace` `insert-or-merge`, `delete`, `merge`, , , és `insert-or-replace` műveleteket tehet meg bármilyen kombinációban.  
* A kötegelt művelet lehet egy lekérési művelet, ha ez az egyetlen művelet a kötegben.  
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.  
* A kötegműveletek 4 MB nagyságú hasznos adatra vannak korlátozva.  

## <a name="query-and-modify-entities"></a>Entitások lekérdezése és módosítása

### <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése

Ha egy partíció összes entitását szeretné lekérdezni, használjon objektumot. `table_query` A következő kódpélda egy szűrőt `Smith` ad meg azoknak az entitásoknak, amelyek a partíciókulcs. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.  

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

A lekérdezés ebben a példában visszaadja az összes olyan entitást, amely megfelel a szűrőfeltételeknek. Ha nagy táblákkal rendelkezik és gyakran le kell töltenie a táblaentitásokat, javasoljuk, hogy adatait inkább Azure Storage-blobokban tárolja.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése

Ha nem szeretné lekérdezni a partíció összes entitását, megadhat egy tartományt. Kombinálja a partíciókulcs-szűrőt egy sorkulcs-szűrővel. A következő kódpélda két szűrőt használ `Smith` a partícióösszes entitásának lenyomásához, ahol `E` a sorkulcs (utónév) az ábécénél korábbi betűvel kezdődik, majd kinyomtatja a lekérdezés eredményeit.  

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

Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő `table_operation::retrieve_entity` kód a `Jeff Smith`vevő megadásához használatos. Ez a módszer csak egy entitást ad vissza, `table_result`nem pedig gyűjteményt, és a visszaadott érték a. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.  

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

Ha le szeretne cserélni egy entitást, kérje le a Table Service-ből, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table Service-be. A következő kód egy meglévő ügyfél telefonszámát és e-mail-címét módosítja. A hívás `table_operation::insert_entity`helyett ez `table_operation::replace_entity`a kód a . Ez a megközelítés hatására az entitás teljes mértékben lecserélődik a kiszolgálón, kivéve, ha a kiszolgálón lévő entitás megváltozott a lekérés óta. Ha megváltozott, a művelet sikertelen lesz. Ez a hiba megakadályozza, hogy az alkalmazás felülírja a lekérés és egy másik összetevő frissítése között végrehajtott módosításokat. A hiba megfelelő kezelése az entitás ismételt beolvasása, a módosítások végrehajtása, `table_operation::replace_entity` ha még mindig érvényes, majd egy másik művelet végrehajtása.  

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

`table_operation::replace_entity`a műveletek sikertelenek, ha az entitás megváltozott a kiszolgálóról való lekérés óta. Továbbá a sikeres siker `table_operation::replace_entity` érdekében először le kell kérnie az entitást a kiszolgálóról. Néha nem tudja, hogy az entitás létezik-e a kiszolgálón. A benne tárolt aktuális értékek nem relevánsak, mivel a frissítésnek felül kell írnia őket. Az eredmény eléréséhez `table_operation::insert_or_replace_entity` használjon műveletet. Ez a művelet beszúrja az entitást, ha nem létezik. A művelet lecseréli az entitást, ha létezik. A következő kódpéldában a `Jeff Smith` vevő entitás továbbra is lekérésre kerül, de `table_operation::insert_or_replace_entity`a rendszer a használatával visszamenti a kiszolgálóra. Az entitáson a lekérési és a frissítési művelet között történt összes módosítás felül lesz írva.  

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

Egy táblalekérdezéssel egy entitásnak csak bizonyos tulajdonságait is lekérdezheti. A következő kódban lévő `table_query::set_select_columns` lekérdezés a metódus t használja, hogy csak a táblában lévő entitások e-mail címét adja vissza.  

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

Az entitást a beolvasás után törölheti. Miután lekért egy `table_operation::delete_entity` entitást, hívja meg a törölni kívánt entitást. Akkor hívja `cloud_table.execute` meg a módszert. A következő kód lekéri és törli a `Smith` partíciókulcsával és `Jeff`a sorkulcsával rendelkező entitást.

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

Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. A törölt tábla a törlést követően egy ideig nem hozható létre újra.  

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

A Visual Studio Community Edition esetében, ha a projekt összeállítási hibákat kap a *storage_account.h* és *table.h*fájlok at tartalmazó fájlok miatt, távolítsa el az **/megengedő fordító** kapcsolót:

1. A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a projektre, és válassza a **Properties** (Tulajdonságok) elemet.
1. A **Property Pages** (Tulajdonságlapok) párbeszédpanelen bontsa ki a **Configuration Properties** (Konfigurációs tulajdonságok), majd a **C/C++** csomópontot, és válassza a **Language** (Nyelv) elemet.
1. A **Conformance mode** (Megfelelőségi mód) értékét állítsa **No** (Nem) értékűre.

## <a name="next-steps"></a>További lépések

A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

Az alábbi hivatkozásokat követve többet is megtudhat az Azure Storage és a Table API Azure Cosmos DB-ben való használatáról:

* [A Table API bemutatása](table-introduction.md)
* [Az Azure Storage-erőforrások listázása C++ nyelven](../storage/common/storage-c-plus-plus-enumeration.md)
* [A Storage ügyféloldali kódtára a C++ programnyelvhez – referencia](https://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
