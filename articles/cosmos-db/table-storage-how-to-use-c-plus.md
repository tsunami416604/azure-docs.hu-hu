---
title: Azure Table Storage és Azure Cosmos DB használata C++ |} Microsoft Docs
description: Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható.
services: cosmos-db
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: f191f308-e4b2-4de9-85cb-551b82b1ea7c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: 60c419a45e795d5968b58fbb6b469d06750a0679
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Azure Table storage és Azure Cosmos DB tábla API használata C++
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a gyakori forgatókönyvek az Azure Table storage szolgáltatást vagy az Azure Cosmos DB tábla API használatával. A minták írt C++ és használni a [Azure Storage ügyféloldali kódtára a C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Az ismertetett forgatókönyvek **létrehozása és egy tábla törlése** és **táblaentitásokat használata**.

> [!NOTE]
> Ez az útmutató az Azure Storage ügyféloldali kódtár célozza meg, a C++ 1.0.0 verzió vagy újabb. Az ajánlott verziója a Storage ügyféloldali kódtára 2.2.0, amelyik keresztül elérhető [NuGet](http://www.nuget.org/packages/wastorage) vagy [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 

## <a name="create-an-azure-service-account"></a>Az Azure szolgáltatás-fiók létrehozása
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB tábla API-fiók létrehozása
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>A C++-alkalmazás létrehozása
Ez az útmutató egy C++ alkalmazáson belül futó tárolási szolgáltatásokkal fogja használni. Ehhez az szükséges, akkor telepítse az Azure Storage ügyféloldali kódtára a C++ és az Azure storage-fiók létrehozása az Azure-előfizetése.  

Telepítse az Azure Storage ügyféloldali kódtára a C++, a következő módszereket használhatja:

* **Linux:** hajtsa végre az adott a [Azure Storage ügyféloldali kódtára a C++ információs](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) lap.  
* **Windows:** a Visual Studióban kattintson **eszközök > NuGet-Csomagkezelő > Csomagkezelő konzol**. Írja be a következő parancsot a [NuGet Package Manager console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) nyomja le az ENTER billentyűt.  
  
     Install-Package wastorage

## <a name="configure-access-to-the-table-client-library"></a>A tábla ügyféloldali kódtár való hozzáférés konfigurálása
Adja hozzá a következők utasítást, hogy a táblázatok eléréséhez használható az Azure storage API-kkal ahová C++ fájl elejéhez:  

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Egy Azure Storage-ügyfél vagy egy Cosmos DB ügyfél a kapcsolati karakterlánc segítségével tárolja a végpontok és adatok szolgáltatások eléréséhez szükséges hitelesítő adatokat. Ha egy ügyfél-alkalmazás fut, a tárolási kapcsolati karakterlánc- vagy Azure Cosmos DB kapcsolati karakterláncot a megfelelő formátumban kell megadnia.

## <a name="set-up-an-azure-storage-connection-string"></a>Egy Azure Storage kapcsolati karakterlánc beállítása
 A tárfiók és a hozzáférési kulcs nevét használja a tárfiók, szerepel a [Azure Portal](https://portal.azure.com) a a *AccountName* és *AccountKey* értékeket. A Storage-fiókok és a hívóbetűk információkért lásd: [Azure Storage-fiókok](../storage/common/storage-create-storage-account.md). Ez a példa bemutatja, hogyan deklarálhatnak ahhoz, hogy az Azure Storage kapcsolati karakterlánc statikus mezőben:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="set-up-an-azure-cosmos-db-connection-string"></a>Egy Azure Cosmos DB kapcsolati karakterlánc beállítása
Azure Cosmos DB fiókját, az elsődleges kulcs és a felsorolt végpont nevét használja a [Azure Portal](https://portal.azure.com) a a *fióknév*, *elsődleges kulcs*, és  *Végpont* értékeket. Ez a példa bemutatja, hogyan deklarálhatnak ahhoz, hogy az Azure Cosmos DB kapcsolati karakterlánc statikus mezőben:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_cosmos_db_account;AccountKey=your_cosmos_db_account_key;TableEndpoint=your_cosmos_db_endpoint"));
```


Az alkalmazás tesztelése a helyi Windows-alapú számítógép, használhatja az Azure [storage emulator](../storage/common/storage-use-emulator.md) együtt települ, amely a [Azure SDK](https://azure.microsoft.com/downloads/). A storage emulator egy segédprogram, amely a helyi fejlesztési számítógépén elérhető Azure Blob, Queue és Table szolgáltatások. A következő példa bemutatja, hogyan deklarálhatja, hogy tárolni tudja a kapcsolati karakterláncot a helyi storage emulator statikus mezőben:  

```cpp
// Define the connection string with Azure storage emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Az Azure storage emulator elindításához kattintson a **Start** gombra, vagy nyomja meg a Windows billentyűt. Írja be a szöveget **Azure Storage Emulator**, majd válassza ki **Microsoft Azure Storage Emulator** az alkalmazások listájából.  

A következő minták azt feltételezik, hogy használt két módszer közül egyik beolvasni a tárolási kapcsolati karakterlánc.  

## <a name="retrieve-your-connection-string"></a>A kapcsolat-karakterlánc beolvasása
Használhatja a **cloud_storage_account** osztályt határoz meg a tárfiók adatait. A tárfiók adatait a tárolási kapcsolati karakterlánc lekéréséhez használja a **elemezni** metódust.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Ezután a mutató hivatkozás beszerzése egy **cloud_table_client** osztályt, lehetővé teszi, hogy a hivatkozás objektum lekérése a Table storage szolgáltatáson belül tárolt táblákat és entitásokat. Az alábbi kód létrehoz egy **cloud_table_client** objektum azt lekérése fent tárolóobjektum fiók használatával:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-a-table"></a>Tábla létrehozása
A **cloud_table_client** objektum lehetővé teszi, hogy a hivatkozás objektum lekérése a táblákat és entitásokat. Az alábbi kód létrehoz egy **cloud_table_client** objektumot, és hozzon létre egy új táblát használja.

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
Egy entitás felvételére egy táblához, hozzon létre egy új **table_entity** objektumot, és adja át a **table_operation::insert_entity**. A következő kódot az ügyfél keresztnevét használja sorkulcsnak és a Vezetéknév, a partíciós kulcs. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban, mint a különböző partíciókulcsúak lekérdezhetők, de az eltérő partíciókulcsok használata lehetővé teszi a párhuzamos művelet nagyobb méretezhetőségét. További információkért lásd: [Microsoft Azure storage teljesítményére és méretezhetőségére ellenőrzőlista](../storage/common/storage-performance-checklist.md).

Az alábbi kód létrehoz egy új példányát **table_entity** az egyes ügyféladatokat kell tárolni. A következő kód hívások **table_operation::insert_entity** létrehozásához egy **table_operation** objektum entitás beszúrása egy táblát, és az új táblázat entitás társítja. Végezetül a kódja meghívja az execute metódus a **cloud_table** objektum. És az új **table_operation** kérést küld a Table szolgáltatás az új ügyfél entitás beszúrása a "felhasználók" táblában.  

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
Egyetlen írási művelettel a Table szolgáltatásba entitásköteget is beszúrhat. Az alábbi kód létrehoz egy **table_batch_operation** objektumot, és hozzáadja a három a beszúrási műveletek rá. Minden egyes végrehajtott beszúrási művelet hozzáadott új forrásentitás-objektum létrehozása, az értékét, és majd az insert metódus hívása a **table_batch_operation** objektum az entitás egy új insert művelethez társítható. Ezt követően **cloud_table.execute** a művelet végrehajtásához.  

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

A kötegműveletekkel kapcsolatban ügyeljen a következőkre:  

* Tetszőleges kombinációját egyetlen kötegben legfeljebb 100 insert, törlése, egyesítési, csere, beszúrási vagy egyesítés és beszúrása vagy lecserélése műveletek végezhetők el.  
* A kötegelt művelet lehet a beolvasási műveletet, ha a köteg egyetlen művelete.  
* Egy adott kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.  
* A kötegelt művelet 4 MB-os adattartalom korlátozódik.  

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Ha egy táblából egy partíció összes entitását, használja a **table_query** objektum. Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.  

> [!NOTE]
> Ezek a módszerek jelenleg nem támogatottak az Azure Cosmos DB a c++-hoz.

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

Ebben a példában a lekérdezés során az összes entitást a szűrési feltételeknek. Ha nagy táblák és kell letölteni a táblaentitásokat gyakran, azt javasoljuk, hogy az adatok tárolva az Azure storage blobs helyette.

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése
Ha nem szeretné az összes entitást lekérdezni egy partícióból, megadhat egy tartományt a partíciókulcs és a sorkulcs szűrőjének kombinálásával. Az alábbi példakód két szűrő segítségével kéri le az összes olyan entitást a „Smith” partícióból, ahol a sorkulcs (keresztnév) az ábécében az „E”-t megelőző betűvel kezdődik, majd megjeleníti a lekérdezés eredményeit.  

> [!NOTE]
> Ezek a módszerek jelenleg nem támogatottak az Azure Cosmos DB a c++-hoz.

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
Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kódban **table_operation::retrieve_entity** adja meg a "Jeff Smith". Ez a módszer adja vissza egy gyűjtemény helyett csak egyetlen entitást, és a visszaadott érték **table_result**. Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.  

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
Entitás cseréje lekéréséhez a Table szolgáltatásból, módosítsa az entitásobjektumot, és mentse a módosításokat vissza a Table szolgáltatás. Az alábbi kód vált egy meglévő ügyfél telefonszámát és az e-mail címét. Telefonhívás helyett **table_operation::insert_entity**, a kódban **table_operation::replace_entity**. A rendszer így teljesen lecseréli az entitást a kiszolgálón, hacsak az a lekérdezés óta nem módosult, mert ez esetben a művelet sikertelen lesz. Erre a hibára azért van szükség, hogy az alkalmazás ne írhasson felül véletlenül egy olyan módosítást, amelyet az alkalmazás egy másik összetevője hozott létre a lekérés és a frissítés között. Ez a hiba megfelelő kezeléséhez az, hogy újra bejegyzés lekérdezésére, hajtsa végre a módosításokat (Ha még érvényesek), és hajtsa végre egy másik **table_operation::replace_entity** műveletet. A következő szakaszban megtudhatja, hogyan bírálhatja felül ezt a viselkedést.  

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
**table_operation::replace_entity** műveletek sikertelenek lesznek, ha az entitást a kiszolgálóról lekérdezés óta módosult. Ezenkívül meg kell kérnie az entitást a kiszolgálóról először ahhoz, hogy **table_operation::replace_entity** sikeres. Egyes esetekben azonban nem tudja Ha az entitás létezik-e a kiszolgálón, és a benne tárolt aktuális értékek irrelevánsak – a frissítés mindent felülír. Ennek érdekében használhatja a **table_operation::insert_or_replace_entity** műveletet. Ha nem létezik az entitás, ez a művelet beszúrja, ha pedig létezik, akkor a legutóbbi frissítés idejétől függetlenül lecseréli. Az alábbi példakódban az ügyfélentitást Jeff Smith program továbbra is, de azok majd mentésekor a kiszolgálón keresztül **table_operation::insert_or_replace_entity**. Az entitás a lekérés és a frissítési művelet között történt összes módosítást felül lesznek írva.  

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
A lekérdezés egy táblához pár tulajdonságok kérhetnek le egy entitás. A lekérdezés a következő kódrészlet használja a **table_query::set_select_columns** metódus csak az e-mail címek entitások vissza a táblában.  

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
> Egy entitás bizonyos tulajdonságait lekérdezése hatékonyabb működését, mint az összes tulajdonság beolvasása.
> 
> 

## <a name="delete-an-entity"></a>Entitás törlése
Egy entitás azt beolvasása után egyszerűen törölheti. Az entitás beolvasott, ha hívása **table_operation::delete_entity** törli az entitáshoz. Majd hívja a **cloud_table.execute** metódust. A következő kódot kéri le, és törli az entitást a partíciós kulcs "Smith" és "Jeff" sor kulcs.  

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
* Szerkesztés a Visual Studio 2017 Community Edition hibák

  Ha a project build hibák miatt a belefoglalási fájlok storage_account.h és table.h lekérdezi, távolítsa el a **/ megengedő-** fordító kapcsoló. 
  - A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki **tulajdonságok**.
  - Az a **tulajdonságlapjain** párbeszédpanelen bontsa ki **konfigurációs tulajdonságok**, bontsa ki a **C/C++**, és válassza ki **nyelvi**.
  - Állítsa be **megfelelési mód** való **nem**.
   
## <a name="next-steps"></a>További lépések
Az alábbi hivatkozásokból tudhat meg többet Azure Storage és a tábla API-nak Azure Cosmos DB: 

* [A tábla API bemutatása](table-introduction.md)
* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [A c++ Azure Storage-erőforrások felsorolása](../storage/common/storage-c-plus-plus-enumeration.md)
* [A Storage ügyféloldali kódtára a c++ nyelvhez – dokumentáció](http://azure.github.io/azure-storage-cpp)
* [Az Azure Storage-dokumentáció](https://azure.microsoft.com/documentation/services/storage/)
