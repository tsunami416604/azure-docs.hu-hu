---
title: "Azure Table Storage használata Ruby |} Microsoft Docs"
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
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: decc6ffb38a4358d3593642f9cedb59d08f6bfef
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-ruby"></a>Ruby Azure Table storage használata
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a gyakori forgatókönyvek az Azure Table szolgáltatással. A minták íródtak, a Ruby API használatával. Az ismertetett forgatókönyvek **létrehozása egy tábla törlésével, és a tábla az entitások lekérdezése**.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby-alkalmazás létrehozása
További tájékoztatást szeretne Ruby-alkalmazás létrehozása, olvassa el [Ruby sínek webalkalmazás egy Azure virtuális gépen](../virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-access-storage"></a>Állítsa be az alkalmazását tároló elérésére
Azure Storage használatához szüksége töltse le és használja a Ruby azure csomagot tartalmaz, a felhasználók kényelme érdekében szalagtár szerepel, amely a többi tárolási szolgáltatásokkal kommunikálni.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems használja a csomag beszerzése
1. Használjon például egy parancssori felületet **PowerShell** (Windows), **Terminálszolgáltatások** (Mac), vagy **Bash** (Unix).
2. Típus **gem telepítése azure** a gem és függőségeinek telepítésére a parancsablakban.

### <a name="import-the-package"></a>A csomag importálása
Kedvenc szövegszerkesztőjével használja, a Ruby, ahol tárolására használni kívánt fájl elejéhez adja hozzá a következőket:

```ruby
require "azure"
```

## <a name="set-up-an-azure-storage-connection"></a>Egy Azure Storage-kapcsolat beállítása
Az azure-moduljának a környezeti változókat olvassák **AZURE\_tárolási\_fiók** és **AZURE\_tárolási\_hozzáférés\_kulcs** az Azure Storage-fiók eléréséhez szükséges információkat. Ha ezek a környezeti változók nem, meg kell adnia a fiók használata előtt **Azure::TableService** az alábbi kódra:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your azure storage access key>"
```

Ezek az értékek beolvasása klasszikus vagy erőforrás-kezelő storage-fiókot az Azure-portálon:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Nyissa meg a használni kívánt tárfiókot.
3. Kattintson a beállítások panelen kattintson a jobb **hívóbetűk**.
4. A elérési kulcsok paneljén megjelenő láthatja, a hozzáférési kulcs 1 és 2 elérési kulcsot. Ezek bármelyikét használhatja.
5. A Másolás ikonra a kulcs másolása a vágólapra.

## <a name="create-a-table"></a>Tábla létrehozása
A **Azure::TableService** objektum lehetővé teszi, hogy a táblákat és entitásokat. A tábla létrehozásához használja a **létrehozása\_table()** metódust. Az alábbi példa létrehoz egy táblát, vagy kiírja a hiba, ha van ilyen.

```ruby
azure_table_service = Azure::TableService.new
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

* **frissítés\_entity():** cserélje ki egy meglévő entitás frissítése.
* **Egyesítési\_entity():** meglévő entitás frissíti a meglévő entitás új tulajdonságértékek egyesíti.
* **Helyezze be\_vagy\_egyesítési\_entity():** cserélje ki egy meglévő entitás frissíti. Ha nem entitás létezik, egy új szúrja be:
* **Helyezze be\_vagy\_cserélje le\_entity():** meglévő entitás frissíti a meglévő entitás új tulajdonságértékek egyesíti. Ha nem entitás létezik, a program beszúrja egy újat.

A következő példa bemutatja, hogy egy entitás használatával frissítése **frissítése\_entity()**:

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

A **frissítése\_entity()** és **egyesítési\_entity()**, ha a frissítendő entitás nem létezik, akkor a frissítési művelet sikertelen lesz. Ezért ha egy entitás, függetlenül attól, hogy létezik-e tárolni, Ehelyett használjon **beszúrása\_vagy\_cserélje le\_entity()** vagy **beszúrása\_vagy\_egyesítési\_entity()**.

## <a name="work-with-groups-of-entities"></a>Az entitások csoportok használata
Egyes esetekben érdemes elküldeni a több műveletei együtt egy kötegelt atomi feldolgozása a kiszolgáló biztosításához. Hajthatja végre, hogy, először létre kell hoznia egy **kötegelt** objektumot, majd a **hajtható végre\_batch()** metódusa **TableService**. A következő példa bemutatja, hogy két olyan entitásra RowKey 2 és 3 kötegben elküldése. Figyelje meg, hogy csak az azonos PartitionKey rendelkező entitások működik.

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
Egy tábla egy entitás lekérdezéséhez használja a **beolvasása\_entity()** metódus úgy, hogy a táblázat neve **PartitionKey** és **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Az entitások készletének lekérdezése
Az egy tábla entitások készletének lekérdezéséhez létrehozni lekérdezés kivonat objektumot, és használja a **lekérdezés\_entities()** metódust. A következő példa bemutatja, hogy ugyanarra a entitások első **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Ha az eredményhalmaz van visszaállításához a folytatási kód adja vissza, amelyek segítségével beolvasni a következő lapjain egyetlen lekérdezés túl nagy.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
A lekérdezés egy táblához pár tulajdonságok kérhetnek le egy entitás. Ez a módszer, "leképezés" nevű csökkenti a sávszélesség, és javíthatja a lekérdezési teljesítményt, különösen olyan nagyméretű entitásokat. A select záradékban használható, és adja át a tulajdonságok az ügyfél kerüljön szeretné a nevét.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Entitás törlése
Egy entitás törléséhez használja a **törlése\_entity()** metódust. Az entitás-, a PartitionKey és az entitás RowKey tartalmazó tábla nevében továbbítani kell.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Tábla törlése
Egy tábla törléséhez használja a **törlése\_table()** metódus és pass a törölni kívánt tábla nevében.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>További lépések

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Rubyhoz készült Azure SDK](http://github.com/WindowsAzure/azure-sdk-for-ruby) GitHub tárházából

