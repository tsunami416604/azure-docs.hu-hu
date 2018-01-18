---
title: "Azure Table storage használata a Python |} Microsoft Docs"
description: "Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható."
services: cosmos-db
documentationcenter: python
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 7ddb9f3e-4e6d-4103-96e6-f0351d69a17b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: a786f82d94a1a0039ed65a618670f872ffa3e3c2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-use-azure-table-storage-with-python"></a>Python Azure Table storage használata

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Ez az útmutató bemutatja, hogyan hajthat végre az Azure Table storage gyakori helyzetek a Python használatával a [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python). Az ismertetett forgatókönyvek tartalmaznak, létrehozása és egy tábla törlésével és a entitás lekérdezése.

Ebben az oktatóanyagban a forgatókönyvek keresztül működő, miközben Kezdésként tekintse meg a [Python API-referencia az Azure Storage szolgáltatás SDK](https://azure-storage.readthedocs.io/en/latest/index.html).

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="install-the-azure-storage-sdk-for-python"></a>Python az Azure Storage SDK telepítése

Miután létrehozott egy tárfiókot, a következő lépés a rendszer telepíti a [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python). Az SDK telepítésével kapcsolatos részletekért tekintse meg a [README.rst](https://github.com/Azure/azure-storage-python/blob/master/README.rst) fájlban találhatók a Storage szolgáltatás SDK Python tárház a Githubon.

## <a name="create-a-table"></a>Tábla létrehozása

Az Azure Table szolgáltatás a Python használatához importálnia kell a [TableService] [ py_TableService] modul. Mivel lesz a táblaentitásokat dolgozik, akkor is kell a [entitás] [ py_Entity] osztály. Adja hozzá ezt a kódot, felső részén a Python-fájl importálása:

```python
from azure.storage.table import TableService, Entity
```

Hozzon létre egy [TableService] [ py_TableService] objektum, a tárfiók nevét és a fiók kulcsának benyújtása. Cserélje le `myaccount` és `mykey` a fióknevet és a kulcs és a hívás [create_table] [ py_create_table] tábla létrehozása az Azure Storage.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')

table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Egy entitás hozzáadásához először létre kell hoznia egy objektumot, amely jelöli az entitásban, majd adja át az objektumot a [TableService][py_TableService].[ insert_entity] [ py_insert_entity] metódust. A forrásentitás-objektum dictionary vagy típusú objektum lehet [entitás][py_Entity], és határozza meg az entitás nevét és értékeit. Minden entitás tartalmaznia kell a szükséges [PartitionKey és RowKey](#partitionkey-and-rowkey) tulajdonságokat adhat meg az entitás egyéb tulajdonságait.

Ebben a példában a szótár-objektumot hoz létre képviselő entitás, majd továbbítja azt a [insert_entity] [ py_insert_entity] módszert veheti fel a táblázatban:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the trash', 'priority' : 200}
table_service.insert_entity('tasktable', task)
```

Ez a példa létrehoz egy [entitás] [ py_Entity] objektumot, majd továbbítja azokat a a [insert_entity] [ py_insert_entity] módszert veheti fel a táblázatban:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>PartitionKey és RowKey

Meg kell adni egy **PartitionKey** és egy **RowKey** összes entitás tulajdonság. Ezek azok az entitások egyedi azonosítói együtt, egy entitás elsődleges kulcsának alkotják. Ezek az értékek sokkal gyorsabb, mint bármely más entitás tulajdonságai, mivel csak ezek a Tulajdonságok indexelt kérdezheti használatával kérdezheti le.

A Table szolgáltatás által használt **PartitionKey** intelligens módon táblaentitásokat szét a tárhely csomópontot. Az entitások **PartitionKey** ugyanazon a csomóponton találhatók. **RowKey** tartozik, a partíción belül entitás egyedi azonosítója.

## <a name="update-an-entity"></a>Egy entitás frissítése

Frissíti az összes olyan egy entitás tulajdonságértékek, hívja meg a [update_entity] [ py_update_entity] metódust. Ez a példa bemutatja, hogyan lecseréli a meglévő entitás frissített verzióra:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage', 'priority' : 250}
table_service.update_entity('tasktable', task)
```

Ha a frissítendő entitás nem létezik, majd a frissítés művelet sikertelen lesz. Ha azt szeretné, hogy egy entitás tárolására hogy megtalálható-e vagy sem, használjon [insert_or_replace_entity][py_insert_or_replace_entity]. A következő példában az első hívás lecseréli a meglévő entitás. A második hívás szúrja be egy új entitást, a megadott PartitionKey nem entitás óta, és RowKey létezik a táblában.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001', 'description' : 'Take out the garbage again', 'priority' : 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003', 'description' : 'Buy detergent', 'priority' : 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> A [update_entity] [ py_update_entity] módszer a felváltja az összes tulajdonságok és értékek egy meglévő entitás, amely tulajdonságok eltávolítása egy meglévő entitás is használhatja. Használhatja a [merge_entity] [ py_merge_entity] módszer új vagy módosított tulajdonságértékek nem teljesen cseréli le az entitás meglévő entitás frissítheti.

## <a name="modify-multiple-entities"></a>Több entitás módosítása

A Table szolgáltatás által a kérés atomi feldolgozása érdekében elküldheti a több műveletei együtt egy kötegben. Először is a [TableBatch] [ py_TableBatch] osztály több művelet hozzáadása egy tételt. Ezután hívja [TableService][py_TableService].[ commit_batch] [ py_commit_batch] egy atomi művelet műveleteknek elküldeni. Összes entitás kötegelt a módosítani kívánt partícióra kell lennie.

Ebben a példában két olyan entitásra kötegben együtt hozzáadása:

```python
from azure.storage.table import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004', 'description' : 'Go grocery shopping', 'priority' : 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005', 'description' : 'Clean the bathroom', 'priority' : 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Kötegek is használható a környezet kezelő szintaxisa:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006', 'description' : 'Go grocery shopping', 'priority' : 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007', 'description' : 'Clean the bathroom', 'priority' : 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Egy entitás lekérdezése

Egy tábla entitás lekérdezéséhez adja át a PartitionKey és a RowKey a [TableService][py_TableService].[ get_entity] [ py_get_entity] metódust.

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Az entitások készletének lekérdezése

Alapján is kereshet az entitások készletének úgy, hogy megadja az egy szűrési karakterláncot a **szűrő** paraméter. Ebben a példában megkeresi az összes feladat budapesti PartitionKey a szűrő alkalmazásával:

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése

Minden entitás lekérdezésben visszaadja a tulajdonságok is korlátozhatja. Ezzel a módszerrel nevű *leképezése*, csökkenti a sávszélesség, és javíthatja a lekérdezési teljesítményt, különösen olyan nagyméretű az entitások vagy a eredményhalmazt. Használja a **válasszon** paraméter és a kívánt tulajdonságokat nevei küld vissza az ügyfélnek fázis.

A lekérdezés a következő kódrészlet csak entitások leírását a táblázatban adja vissza.

> [!NOTE]
> Az alábbi kódrészletben működik, csak az Azure Storage ellen. A storage emulator által nem támogatott.

```python
tasks = table_service.query_entities('tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Entitás törlése

Entitás törlése a PartitionKey és RowKey történő átadásával az [delete_entity] [ py_delete_entity] metódust.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Tábla törlése

Ha már nincs szüksége egy táblát, vagy azokat az egységeket belül, a [delete_table] [ py_delete_table] végleg törölni kívánja a táblázatot az Azure Storage metódust.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>További lépések

* [Az Azure Storage SDK for Python API-referencia](https://azure-storage.readthedocs.io/en/latest/index.html)
* [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [A Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md): egy ingyenes, platformfüggetlen-alkalmazást a Windows, a macOS és a Linux vizuálisan adatok Azure Storage használata.

[py_commit_batch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.commit_batch
[py_create_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.create_table
[py_delete_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_entity
[py_delete_table]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.delete_table
[py_Entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.models.html#azure.storage.table.models.Entity
[py_get_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.get_entity
[py_insert_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_entity
[py_insert_or_replace_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.insert_or_replace_entity
[py_merge_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.merge_entity
[py_update_entity]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html#azure.storage.table.tableservice.TableService.update_entity
[py_TableService]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tableservice.html
[py_TableBatch]: https://azure-storage.readthedocs.io/en/latest/ref/azure.storage.table.tablebatch.html#azure.storage.table.tablebatch.TableBatch