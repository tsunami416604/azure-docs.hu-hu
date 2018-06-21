---
title: Az Azure Table Storage és az Azure Cosmos DB Table API használata a C++ nyelvvel | Microsoft Docs
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 520c0868e51f1212ee1beca8967d3c08f1d6e869
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797913"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Az Azure Table Storage és az Azure Cosmos DB Table API használata a C++ nyelvvel
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Table Storage szolgáltatás vagy az Azure Cosmos DB Table API használatával. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják. Az ismertetett forgatókönyvek a **táblák létrehozásával és törlésével**, valamint a **táblaentitások használatával** foglalkoznak.

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Az ajánlott verzió a Storage ügyféloldali kódtárának 2.2.0-s verziója, amely elérhető a [NuGeten](http://www.nuget.org/packages/wastorage) vagy a [GitHubon](https://github.com/Azure/azure-storage-cpp/) keresztül.
> 

## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
Az útmutatóban olyan Storage-szolgáltatásokat használ, amelyek futtathatók egy C++-alkalmazásban. Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.  

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux:** Kövesse az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) oldalán található utasításokat.  
* **Windows:** A Visual Studióban válassza a **Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Package Manager Console (Csomagkezelő konzol)** elemet. Írja be az alábbi parancsot a [NuGet csomagkezelő konzolján](http://docs.nuget.org/docs/start-here/using-the-package-manager-console), majd nyomja le az Enter billentyűt.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>A Table ügyféloldali kódtárhoz való hozzáférés konfigurálása
Adja hozzá az alábbi beszúrási utasításokat a C++-fájl elejéhez, ahol használni szeretné az Azure Storage API-kat a táblák eléréséhez:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Az Azure Storage-ügyfél és a Cosmos DB-ügyfél egy kapcsolati sztringet használ a végpontok és az adatkezelési szolgáltatások elérésére szolgáló hitelesítő adatok tárolásához. Ügyfélalkalmazás futtatásakor meg kell adnia a Storage kapcsolati sztringjét vagy az Azure Cosmos DB kapcsolati sztringjét a megfelelő formátumban.

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage kapcsolati sztring beállítása
 Használja a Storage-fiók nevét és az [Azure Portalon](https://portal.azure.com) megtalálható hozzáférési kulcsát az *AccountName* és *AccountKey* értékeként. További információ a Storage-fiókokkal és a hozzáférési kulcsokkal kapcsolatban: [Tudnivalók az Azure Storage-fiókokról](../storage/common/storage-create-storage-account.md). Ez a példa bemutatja, hogyan deklarálhat statikus mezőt az Azure Storage kapcsolati sztring tárolására:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Azure Cosmos DB kapcsolati sztring beállítása
Használja az Azure Cosmos DB-fiók nevét, az elsődleges kulcsot és az [Azure Portalon](https://portal.azure.com) található végpontot a *Fiók neve*, az *Elsődleges kulcs* és a *Végpont* értékeként. Ez a példa bemutatja, hogyan deklarálhat statikus mezőt az Azure Cosmos DB kapcsolati sztring tárolására:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Az alkalmazás helyi Windows-alapú számítógépen történő teszteléséhez az Azure [Storage Emulatort](../storage/common/storage-use-emulator.md) használhatja, amely az [Azure SDK-val](https://azure.microsoft.com/downloads/) lett telepítve. A Storage Emulator egy olyan segédprogram, amely a helyi fejlesztői gépen elérhető Azure Blob, Queue és Table szolgáltatást szimulálja. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure Storage Emulator elindításához kattintson a **Start** gombra vagy nyomja le a Windows billentyűt. Kezdje el beírni az **Azure Storage Emulator** kifejezést, majd válassza a **Microsoft Azure Storage Emulator** elemet az alkalmazások listájából.  

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.  

## <a name="retrieve-your-connection-string"></a>A kapcsolati sztring lekérése
A **cloud_storage_account** osztály használatával jelenítheti meg a Storage-fiók információit. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután szerezze be a **cloud_table_client** osztályra mutató hivatkozást, mivel ez lehetővé teszi, hogy megkaphassa a táblák hivatkozásobjektumait és a Table Storage szolgáltatásban tárolt entitásokat. Az alábbi kód egy **cloud_table_client** objektumot hoz létre a Storage-fiók fent lekért objektumának használatával:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Tábla létrehozása
A **cloud_table_client** objektum lehetővé teszi, hogy lekérje táblák és entitások hivatkozásobjektumait. Az alábbi kód egy **cloud_table_client** objektumot hoz létre, és egy új tábla létrehozásához használja azt.

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

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy új **table_entity** objektumot, és adja át a **table_operation::insert_entity** számára. Az alábbi kód az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb méretezhetőségét teszi lehetővé. További információ: [A Microsoft Azure Storage teljesítmény- és méretezhetőségi ellenőrzőlistája](../storage/common/storage-performance-checklist.md).

Az alábbi kód a **table_entity** egy új példányát hozza létre, néhány tárolandó ügyféladattal. A kód ezután meghívja a **table_operation::insert_entity** metódust egy **table_operation** objektum létrehozására, amely egy entitást szúr be egy táblába, és társítja hozzá az új táblaentitást. Végül a kód meghívja a végrehajtási metódust a **cloud_table** objektumra vonatkozóan. Az új **table_operation** kérést küld a Table Service számára az új ügyfélentitásnak a „people” táblába való beszúrásához.  

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

## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Egyetlen írási művelettel egy teljes entitásköteget is beszúrhat a Table Service-be. Az alábbi kód egy **table_batch_operation** objektumot hoz létre, majd hozzáad három beszúrási műveletet. Az egyes beszúrási műveletek a következőképpen adhatók hozzá: létre kell hozni egy új entitásobjektumot, be kell állítani az értékeit, majd meg kell hívni a beszúrási metódust a **table_batch_operation** objektumra vonatkozóan az entitás társításához egy új beszúrási művelettel. Ezután a **cloud_table.execute** meghívásával hajtható végre a művelet.  

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

* Akár 100 beszúrás, törlés, egyesítés, csere, beszúrás-vagy-egyesítés és beszúrás-vagy-csere műveletet is végrehajthat, bármilyen kombinációban, egyetlen kötegben.  
* A kötegművelet rendelkezhet lekérési művelettel, ha ez a kötegben található egyetlen művelet.  
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.  
* A kötegműveletek 4 MB nagyságú hasznos adatra vannak korlátozva.  

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Ha egy táblából egy partíció összes entitását le szeretné kérdezni, használjon **table_query** objektumot. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.  

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

A példában szereplő lekérdezés a szűrési feltételeknek megfelelő összes entitást megjeleníti. Ha nagy táblákkal rendelkezik és gyakran le kell töltenie a táblaentitásokat, javasoljuk, hogy adatait inkább Azure Storage-blobokban tárolja.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése
Ha nem szeretné az összes entitást lekérdezni egy partícióból, megadhat egy tartományt a partíciókulcs és a sorkulcs szűrőjének kombinálásával. Az alábbi példakód két szűrő segítségével kéri le az összes olyan entitást a „Smith” partícióból, ahol a sorkulcs (keresztnév) az ábécében az „E”-t megelőző betűvel kezdődik, majd megjeleníti a lekérdezés eredményeit.  

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

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kód a **table_operation::retrieve_entity** metódussal adja meg a „Jeff Smith” nevű ügyfelet. Ez a metódus egy gyűjtemény helyett csak egyetlen entitást ad vissza, és a visszaadott értéket a **table_result** tartalmazza. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.  

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

## <a name="replace-an-entity"></a>Entitás cseréje
Ha le szeretne cserélni egy entitást, kérje le a Table Service-ből, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table Service-be. A következő kód egy meglévő ügyfél telefonszámát és e-mail-címét módosítja. A **table_operation::insert_entity** meghívása helyett a kód a **table_operation::replace_entity** műveletet használja. A rendszer így teljesen lecseréli az entitást a kiszolgálón, hacsak az a lekérdezés óta nem módosult, mert ez esetben a művelet sikertelen lesz. Erre a hibára azért van szükség, hogy az alkalmazás ne írhasson felül véletlenül egy olyan módosítást, amelyet az alkalmazás egy másik összetevője hozott létre a lekérés és a frissítés között. A hiba megfelelő kezeléséhez kérje le újra az entitást, végezze el a módosításokat (ha még érvényesek), majd hajtson végre egy újabb **table_operation::replace_entity** műveletet. A következő szakaszban megtudhatja, hogyan bírálhatja felül ezt a viselkedést.  

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

## <a name="insert-or-replace-an-entity"></a>Entitás beszúrása vagy lecserélése
Ha az entitást a kiszolgálóról való lekérése óta módosították, a **table_operation::replace_entity** műveletek sikertelenek lesznek. Ezenkívül a **table_operation::replace_entity** művelet sikeres végrehajtásához először le kell kérnie az entitást a kiszolgálóról. Néha azonban nem tudható, hogy az entitás létezik-e a kiszolgálón, és hogy a benne tárolt aktuális értékek irrelevánsak-e – a frissítésnek felül kell írnia minden értéket. Ehhez használja a **table_operation::insert_or_replace_entity** műveletet. Ha nem létezik az entitás, ez a művelet beszúrja, ha pedig létezik, akkor a legutóbbi frissítés idejétől függetlenül lecseréli. Az alábbi kódpéldában a rendszer lekérdezi Jeff Smith ügyfélentitását, majd a **table_operation::insert_or_replace_entity** művelettel menti a kiszolgálóra. Az entitáson a lekérési és a frissítési művelet között történt összes módosítás felül lesz írva.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert-or-replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert-or-replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
Egy táblalekérdezéssel egy entitásnak csak bizonyos tulajdonságait is lekérdezheti. Az alábbi kódban szereplő lekérdezés a **table_query::set_select_columns** metódust használja a táblában található entitások e-mail-címének lekérdezéséhez.  

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
> 

## <a name="delete-an-entity"></a>Entitás törlése
A lekérés után egyszerűen törölheti az entitásokat. Az entitás lekérése után a törléshez hívja meg a **table_operation::delete_entity** műveletet az entitással. Ezután hívja meg a **cloud_table.execute** metódust. Az alábbi kód lekérdezi és törli a „Smith” partíciókulcsú és a „Jeff” sorkulcsú entitást.  

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

## <a name="delete-a-table"></a>Tábla törlése
Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. A törölt táblák a törlés után egy ideig nem hozhatók létre újra.  

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
* A Visual Studio 2017 Community Edition felépítési hibái

  Ha a projektben felépítési hibákat tapasztal a storage_account.h és a table.h belefoglalási fájl miatt, távolítsa el a **/permissive-** fordítókapcsolót. 
  - A **Solution Explorer** (Megoldáskezelő) lapon kattintson a jobb gombbal a projektre, és válassza a **Properties** (Tulajdonságok) elemet.
  - A **Property Pages** (Tulajdonságlapok) párbeszédpanelen bontsa ki a **Configuration Properties** (Konfigurációs tulajdonságok), majd a **C/C++** csomópontot, és válassza a **Language** (Nyelv) elemet.
  - A **Conformance mode** (Megfelelőségi mód) értékét állítsa **No** (Nem) értékűre.
   
## <a name="next-steps"></a>További lépések
Az alábbi hivatkozásokat követve többet is megtudhat az Azure Storage és a Table API Azure Cosmos DB-ben való használatáról: 

* [A Table API bemutatása](table-introduction.md)
* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Azure Storage-erőforrások listázása C++-ban](../storage/common/storage-c-plus-plus-enumeration.md)
* [A Storage ügyféloldali kódtára a C++ programnyelvhez – referencia](http://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
