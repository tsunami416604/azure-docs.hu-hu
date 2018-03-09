---
title: "Az Azure Table storage Python használatának első lépései |} Microsoft Docs"
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
ms.date: 02/08/2018
ms.author: mimig
ms.openlocfilehash: 455479c9eb77093dd5611263fe5bdcf699b9d026
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-table-storage-using-python"></a>Az Azure Table storage Python használatának első lépései

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Az Azure Table Storage szolgáltatás strukturált NoSQL-adatokat tárol a felhőben, így séma nélküli kulcs-/attribútumtárat biztosítva. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. A Table Storage adataihoz számos alkalmazástípus gyorsan és költséghatékonyan férhet hozzá, a költségei pedig jellemzően alacsonyabbak, mint a hagyományos SQL hasonló mennyiségű adathoz való használata esetében.

A Table Storage segítségével olyan rugalmas adatkészleteket tárolhat, mint például webalkalmazások felhasználói adatai, címtárak, eszközadatok és bármilyen egyéb metaadat, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Az oktatóanyag bemutatja, hogyan használható a [Azure Cosmos DB táblában SDK for Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) az Azure Table storage gyakori forgatókönyvet. Az SDK neve jelzi való használathoz az Azure Cosmos DB, de használható, amely mindkét Azure Cosmos DB és Azure táblák tárolási, minden egyes szolgáltatást csak egy egyedi végponttal rendelkezik. Ezek a forgatókönyvek írja Python bemutató példákat használatával hogyan:
* Hozzon létre vagy töröljön a táblák
* INSERT és a lekérdezés entitások
* Módosíthatja az entitásokat

Ebben az oktatóanyagban a forgatókönyvek keresztül működő, miközben érdemes lehet hivatkozni a [Azure Cosmos DB SDK for Python API-referencia](https://azure.github.io/azure-cosmosdb-python/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag sikeres teljesítéséhez a következőkre lesz szüksége:

- [Python](https://www.python.org/downloads/) 2.7, 3.3-as, 3.4, 3.5-ös vagy 3.6.
- [Az Azure Cosmos DB Python SDK táblázat](https://pypi.python.org/pypi/azure-cosmosdb-table/). Ez az SDK csatlakoznak az Azure Table storage mind az Azure Cosmos DB tábla API-t.
- [Az Azure Storage-fiók](https://docs.microsoft.com/en-us/azure/storage/common/storage-create-storage-account#create-a-storage-account) vagy [Azure Cosmos DB fiók](https://azure.microsoft.com/en-us/try/cosmosdb/)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Az Azure szolgáltatás-fiók létrehozása

Azure Table storage vagy Azure Cosmos DB használatával táblák dolgozhat. További ehhez beolvassa a szolgáltatások közötti különbségekről [ajánlatok tábla](table-introduction.md#table-offerings). Hozzon létre egy fiókot a szolgáltatás használni fog lesz szüksége. 

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása
Legegyszerűbben az [Azure Portal](https://portal.azure.com) segítségével hozhatja létre első Azure Storage-fiókját. További tudnivalókért lásd: [Create a storage account](../storage/common/storage-create-storage-account.md#create-a-storage-account) (Tárfiók létrehozása).

Egy Azure storage-fiók használatával is létrehozhat [Azure PowerShell](../storage/common/storage-powershell-guide-full.md) vagy [Azure CLI](../storage/common/storage-azure-cli.md).

Ha most nem kíván tárfiókot létrehozni, az Azure Storage Emulatorral helyi környezetben futtathatja és tesztelheti a kódját. További információkért lásd: [Use the Azure Storage Emulator for Development and Testing](../storage/common/storage-use-emulator.md) (Fejlesztés és tesztelés az Azure Storage Emulatorral).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB tábla API-fiók létrehozása

Egy Azure Cosmos DB tábla API-fiók létrehozásával kapcsolatos utasításokért lásd: [tábla API-fiók létrehozása](create-table-dotnet.md#create-a-database-account).

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Python az Azure Cosmos Adatbázistáblájából SDK telepítése

A Storage-fiók létrehozása után a következő lépés a rendszer telepíti a [a Microsoft Azure Cosmos DB táblában SDK Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Az SDK telepítésével kapcsolatos részletekért tekintse meg a [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) fájl a Cosmos DB táblában SDK Python tárház a Githubon.

## <a name="import-the-tableservice-and-entity-classes"></a>A TableService és egyéb entitások osztályok importálása

Az Azure Table szolgáltatásban, a Python entitások dolgozni, használja a [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) és [entitás] [ py_Entity] osztályok. Adja hozzá ezt a kódot, felső részén a Python-fájl importálása:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Csatlakozás Azure Table szolgáltatás

Azure Storage Table szolgáltatás csatlakozhat, hozzon létre egy [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) objektum, másik számára pedig a Tárfiók nevét és a fiók kulcsának. Cserélje le `myaccount` és `mykey` a fióknevet és kulcsot.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Csatlakozás az Azure Cosmos DB-hez

A Azure Cosmos DB, másolja az elsődleges kapcsolati karakterláncot az Azure-portálon, és hozzon létre egy [TableService](https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html) objektum a másolt kapcsolati karakterlánc használatával:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Tábla létrehozása

Hívás [create_table] [ py_create_table] a tábla létrehozásához.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Egy entitás hozzáadásához először létre kell hoznia egy objektumot, amely jelöli az entitásban, majd adja át az objektumot a [TableService.insert_entity metódus][py_TableService]. A forrásentitás-objektum dictionary vagy típusú objektum lehet [entitás][py_Entity], és határozza meg az entitás nevét és értékeit. Minden entitás tartalmaznia kell a szükséges [PartitionKey és RowKey](#partitionkey-and-rowkey) tulajdonságokat adhat meg az entitás egyéb tulajdonságait.

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
from azure.cosmosdb.table.tablebatch import TableBatch
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

Entitás törlése úgy, hogy a **PartitionKey** és **RowKey** számára a [delete_entity] [ py_delete_entity] metódust.

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Tábla törlése

Ha már nincs szüksége egy táblát, vagy azokat az egységeket belül, a [delete_table] [ py_delete_table] végleg törölni kívánja a táblázatot az Azure Storage metódust.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>További lépések

* [Gyakori kérdések – fejlesztéséhez és a tábla API](https://docs.microsoft.com/en-us/azure/cosmos-db/faq#develop-with-the-table-api)
* [Az Azure Cosmos DB SDK for Python API-referencia](https://azure.github.io/azure-cosmosdb-python/)
* [Python fejlesztői központ](https://azure.microsoft.com/develop/python/)
* [A Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md): egy ingyenes, platformfüggetlen-alkalmazást a Windows, a macOS és a Linux vizuálisan adatok Azure Storage használata.
* [Working with Python a Visual Studio (Windows)](https://docs.microsoft.com/en-us/visualstudio/python/overview-of-python-tools-for-visual-studio)

[py_commit_batch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_create_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_delete_table]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_Entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.models.html
[py_get_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_insert_or_replace_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableService]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_TableBatch]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tablebatch.html
[py_merge_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
[py_update_entity]: https://azure.github.io/azure-cosmosdb-python/ref/azure.cosmosdb.table.tableservice.html
