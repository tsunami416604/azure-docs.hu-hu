---
title: "Azure Table Storage és Azure Cosmos DB tábla API használata Ruby |} Microsoft Docs"
description: "Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható."
services: cosmos-db
documentationcenter: ruby
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 047cd9ff-17d3-4c15-9284-1b5cc61a3224
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 02/27/2018
ms.author: mimig
ms.openlocfilehash: 104d793826116462f71e4889386906256b2df8f8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-ruby"></a>Ruby Azure Table Storage és Azure Cosmos DB tábla API használata
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a gyakori forgatókönyvek az Azure Table szolgáltatás és az Azure Cosmos DB tábla API használatával. A minták írt Ruby és használni a [Azure Storage tábla ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table). Az ismertetett forgatókönyvek **létrehozása és egy tábla törlésével és a tábla az entitások lekérdezése**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Tároló vagy Azure Cosmos DB hozzáférés hozzáadása
Azure Storage- vagy Azure Cosmos DB használni, töltse le, és a Ruby Azure-csomag, amely tartalmaz, a felhasználók kényelme érdekében szalagtár szerepel, amely a táblázat többi szolgáltatásokkal kommunikálni.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix).
2. Típus **gem telepítése azure-storage-tábla** a gem és függőségeinek telepítésére a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
Kedvenc szövegszerkesztőjével használja, a Ruby, ahol tárolására használni kívánt fájl elejéhez adja hozzá a következőket:

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Az Azure Storage-kapcsolat hozzáadása
Az Azure Storage modul olvassa be a környezeti változók **AZURE_STORAGE_ACCOUNT** és **AZURE_STORAGE_ACCESS_KEY** az Azure Storage-fiók eléréséhez szükséges információkat. Ha ezek a környezeti változók nem, meg kell adnia a fiók használata előtt **Azure::Storage::Table::TableService** az alábbi kódra:

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Ezek az értékek beolvasása klasszikus vagy erőforrás-kezelő storage-fiókot az Azure-portálon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg a használni kívánt tárfiókot.
3. Kattintson a beállítások panelen kattintson a jobb **hívóbetűk**.
4. A elérési kulcsok paneljén megjelenő láthatja, a hozzáférési kulcs 1 és 2 elérési kulcsot. Ezek bármelyikét használhatja.
5. A Másolás ikonra a kulcs másolása a vágólapra.

## <a name="add-an-azure-cosmos-db-connection"></a>Egy Azure Cosmos adatbázis-kapcsolat hozzáadása
A Azure Cosmos DB, másolja az elsődleges kapcsolati karakterláncot az Azure-portálon, és hozzon létre egy **ügyfél** objektumba a másolt kapcsolati karakterláncot. Átadhatók a **ügyfél** objektum létrehozásakor egy **TableService** objektum:

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Tábla létrehozása
A **Azure::Storage::Table::TableService** objektum lehetővé teszi, hogy a táblákat és entitásokat. A tábla létrehozásához használja a **create_table()** metódust. Az alábbi példa létrehoz egy táblát, vagy kiírja a hiba, ha van ilyen.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Egy entitás hozzáadásához először létre kell hoznia egy ujjlenyomat-objektumot, amely meghatározza az entitás tulajdonságait. Vegye figyelembe, hogy minden entitás meg kell adnia egy **PartitionKey** és **RowKey**. Ezek az entitások egyedi azonosítói, és sokkal gyorsabb, mint a többi tulajdonság lekérdezett érték. Használja az Azure Storage **PartitionKey** automatikusan terjesztené a táblaentitásokat számos tárolási csomópont feladatait. Entitások azonos **PartitionKey** ugyanazon a csomóponton találhatók. A **RowKey** tartozik, a partíción belül entitás egyedi azonosítója.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Egy entitás frissítése
Több módszer érhető el frissíteni a meglévő entitás áll rendelkezésre:

* **update_entity():** cserélje ki egy meglévő entitás frissítése.
* **merge_entity():** meglévő entitás frissíti a meglévő entitás új tulajdonságértékek egyesíti.
* **insert_or_merge_entity():** cserélje ki egy meglévő entitás frissíti. Ha nem entitás létezik, egy új szúrja be:
* **insert_or_replace_entity():** meglévő entitás frissíti a meglévő entitás új tulajdonságértékek egyesíti. Ha nem entitás létezik, a program beszúrja egy újat.

A következő példa bemutatja, hogy egy entitás használatával frissítése **update_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

A **update_entity()** és **merge_entity()**, ha a frissítendő entitás nem létezik, akkor a frissítési művelet sikertelen lesz. Ezért, ha egy entitás, függetlenül attól, hogy létezik-e tárolni szeretné, Ehelyett használjon **insert_or_replace_entity()** vagy **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Az entitások csoportok használata
Egyes esetekben érdemes elküldeni a több műveletei együtt egy kötegelt atomi feldolgozása a kiszolgáló biztosításához. Hajthatja végre, hogy, először létre kell hoznia egy **kötegelt** objektumot, majd a **execute_batch()** metódusa **TableService**. A következő példa bemutatja, hogy két olyan entitásra RowKey 2 és 3 kötegben elküldése. Figyelje meg, hogy csak az azonos PartitionKey rendelkező entitások működik.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Egy entitás lekérdezése
Egy tábla egy entitás lekérdezéséhez használja a **get_entity()** metódus úgy, hogy a táblázat neve **PartitionKey** és **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Az entitások készletének lekérdezése
Az egy tábla entitások készletének lekérdezéséhez létrehozni lekérdezés kivonat objektumot, és használja a **query_entities()** metódust. A következő példa bemutatja, hogy ugyanarra a entitások első **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Az eredménykészlet nem egy egyetlen lekérdezés vissza, ha a folytatási kód ad vissza, használhatja a következő lapjain beolvasása.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
A lekérdezés egy táblához pár tulajdonságok kérhetnek le egy entitás. Ez a módszer, "leképezés," nevű csökkenti a sávszélesség, és javíthatja a lekérdezési teljesítményt, különösen olyan nagyméretű entitásokat. A select záradékban használható, és adja át a tulajdonságok az ügyfél kerüljön szeretné a nevét.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Entitás törlése
Egy entitás törléséhez használja a **delete_entity()** metódust. Továbbítsa az entitás, a PartitionKey és az entitás RowKey tartalmazó tábla nevében.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Tábla törlése
Egy tábla törléséhez használja a **delete_table()** metódus és pass a törölni kívánt tábla nevében.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>További lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Ruby fejlesztői központ](https://azure.microsoft.com/develop/ruby/)
* [A Microsoft Azure Storage tábla ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table) 

