---
title: Hogyan használható az Azure Storage Table szolgáltatás vagy az Azure Cosmos DB tábla API php-ből |} Microsoft Docs
description: Használata php-ből a Table szolgáltatás API létrehozása, és törölni kívánja a táblázatot, és helyezze, törlése, és a tábla lekérdezése.
services: cosmos-db
documentationcenter: php
author: SnehaGunda
manager: kfile
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: af193c5ec7993d44fe67216843eb18f459718cfe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-use-azure-storage-table-service-or-cosmos-db-table-api-from-php"></a>Hogyan használható az Azure Storage Table szolgáltatás vagy Cosmos DB tábla API php-ből
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a gyakori forgatókönyvek az Azure Storage Table szolgáltatás és az Azure Cosmos DB tábla API használatával. A mintákat a PHP és -felhasználási nyelven íródtak a [Azure Storage tábla PHP ügyféloldali kódtár][download]. Az ismertetett forgatókönyvek **létrehozása és egy tábla törlése**, és **beszúrását, törlését és entitások egy tábla lekérdezése**. Az Azure Table szolgáltatás további információkért tekintse meg a [további lépések](#next-steps) szakasz.


## <a name="create-an-azure-service-account"></a>Az Azure szolgáltatás-fiók létrehozása

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB tábla API-fiók létrehozása

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása

A csak a Storage Table szolgáltatás vagy az Azure Cosmos DB tábla API eléréséhez PHP-alkalmazás létrehozásának mérete az azure-storage-tábla SDK osztályok hivatkozni a php a kód. A fejlesztői eszközök hozhat létre az alkalmazás, például a Jegyzettömbbel.

Ebben az útmutatóban használja a Storage Table szolgáltatás vagy Azure Cosmos DB hívható a PHP-alkalmazás helyileg, vagy egy Azure webes szerepkörről, a feldolgozói szerepkör vagy a webhely belül futó szolgáltatásokat.

## <a name="get-the-client-library"></a>Az ügyfél-függvénytár

1. Hozzon létre egy fájlt a projekt gyökérkönyvtárában található composer.json, és az alábbi kód hozzáadása:
```json
{
  "require": {
    "microsoft/azure-storage-table": "*"
  }
}
```
2. Töltse le [composer.phar](http://getcomposer.org/composer.phar) a gyökérkönyvtárba. 
3. Nyisson meg egy parancssort, és hajtsa végre a következő parancsot a projekt gyökérkönyvtárában:
```
php composer.phar install
```
Azt is megteheti, lépjen a [Azure Storage tábla PHP ügyféloldali kódtár](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) a Githubon a forráskód klónozását.


## <a name="add-required-references"></a>Adja hozzá a szükséges hivatkozások
A Storage Table szolgáltatás vagy Azure Cosmos DB API-kat használ, a következőket kell tennie:

* A robotot fájl használatával hivatkozik a [require_once] [ require_once] utasítást, és
* Bármely osztályok használata hivatkozik.

A következő példa bemutatja, hogyan a robotot fájl és a hivatkozás a **TableRestProxy** osztály.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Az alábbi példákban a `require_once` utasítás mindig megjelenik, de csak a szükséges végrehajtandó például osztályok hivatkozott.

## <a name="add-a-storage-table-service-connection"></a>Adja hozzá a tárolási tábla szolgáltatáskapcsolódási
A tárolási Table szolgáltatásügyfél példányt létrehozni, először rendelkeznie kell érvényes kapcsolati karakterláncot. A Storage szolgáltatás kapcsolati karakterlánc formátuma:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Egy Azure Cosmos adatbázis-kapcsolat hozzáadása
Egy Azure Cosmos Adatbázistáblájából ügyfél példányt létrehozni, először egy érvényes kapcsolati karakterláncot kell rendelkeznie. Az Azure Cosmos DB kapcsolati karakterlánc formátuma:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>A Storage emulator kapcsolat hozzáadása
Az emulátor tároló elérésére:

```php
UseDevelopmentStorage = true
```

Az Azure Table szolgáltatásügyfél vagy Azure Cosmos DB ügyfél létrehozásához kell használnia a **TableRestProxy** osztály. A következőket teheti:

* a kapcsolati karakterláncot adja át a közvetlenül vagy
* Használja a **CloudConfigurationManager (CCM)** ellenőrizze a kapcsolati karakterlánc több külső forrás:
  * Alapértelmezés szerint az tartalmaz egy külső adatforrás - környezeti változók támogatása.
  * Új forrásból történő kiterjesztésével adhat hozzá a `ConnectionStringSource` osztály.

Az itt leírt példák a kapcsolati karakterlánc közvetlenül át.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Tábla létrehozása
A **TableRestProxy** objektum lehetővé teszi, hogy a tábla létrehozása a **createTable** metódust. Ha a táblázatok létrehozásáról, beállíthatja a Table szolgáltatás időtúllépés. (A Table szolgáltatás időtúllépési kapcsolatos további információkért lásd: [időtúllépések beállítása a szolgáltatási műveletek tábla][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

Táblanevek vonatkozó megkötésekkel kapcsolatos információkért lásd: [ismertetése a Table szolgáltatás adatmodell][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Egy entitás felvételére egy táblához, hozzon létre egy új **entitás** objektumot, és adja át a **TableRestProxy -> insertEntity**. Vegye figyelembe, hogy egy entitás létrehozásakor meg kell adnia egy `PartitionKey` és `RowKey`. Egy entitás egyedi azonosítói azok és értékek, amelyek sokkal gyorsabb, mint más entitás tulajdonságai kérdezhetők le. A rendszer használja `PartitionKey` automatikusan terjesztené a táblaentitásokat számos tárolási csomópont feladatait. Entitások azonos `PartitionKey` ugyanazon a csomóponton találhatók. (Ugyanazon a csomóponton tárolt entitásokat műveletek végrehajtása jobb, mint a más csomópontjai között tárolt entitásokat.) A `RowKey` egy partíció egy entitás egyedi azonosítója.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

További információ a táblázat tulajdonságai és típusok: [ismertetése a Table szolgáltatás adatmodell][table-data-model].

A **TableRestProxy** osztály entitások beszúrására szolgáló két alternatív módszert kínál: **insertOrMergeEntity** és **insertOrReplaceEntity**. Ezeket a módszereket használja, hozzon létre egy új **entitás** , és adja át paraméterként mindkét módszer. Az egyes módszerek szúrnak az entitás, ha nem létezik. Ha már létezik az entitás, **insertOrMergeEntity** tulajdonságértékek frissíti, ha a tulajdonságai már léteznek, és hozzáadja az új tulajdonságok ha azok nem léteznek, miközben **insertOrReplaceEntity** teljesen lecseréli a meglévő entitás. A következő példa bemutatja, hogyan használható **insertOrMergeEntity**. Ha az entitás `PartitionKey` "tasksSeattle" és `RowKey` "1" már nem létezik, szúrja be. Azonban ha azt korábban beszúrt (a fenti példa szerint), a `DueDate` tulajdonság frissül, és a `Status` tulajdonság hozzá lett adva. A `Description` és `Location` tulajdonságok is frissülnek, de értékekkel, amely hatékonyan hagyja őket változatlan marad. Ha ez utóbbi a tulajdonság a volt nem szerepel a példában látható módon, de a célentitás már létezett, meglévő értékeik változatlan marad.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
A **TableRestProxy -> getEntity** módszer lehetővé teszi egyetlen entitás lekérdezése által végzett lekérdezés a `PartitionKey` és `RowKey`. A partíciós kulcs az alábbi példában a `tasksSeattle` és sorkulcsa `1` kerülnek átadásra a **getEntity** metódust.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Entitás lekérdezések össze szűrők használata (további információkért lásd: [lekérdezése táblákat és entitásokat][filters]). A partíció összes entitásának lekérése, a szűrő használata "PartitionKey eq *partíció_neve*". A következő példa bemutatja, hogyan összes entitásának lekérése a `tasksSeattle` partíció úgy, hogy egy szűrőt, amely a **queryEntities** metódust.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Partíció entitástartományának részhalmazát
Az előző példában használt ugyanilyen mintájú segítségével bármely részét partíció entitástartományának lekérése. Az entitások lekérése részhalmazát határozza meg a szűrő használata (további információkért lásd: [lekérdezése táblákat és entitásokat][filters]). A következő példa bemutatja, hogyan szűrő segítségével egy adott összes entitásának lekérése `Location` és egy `DueDate` kisebb, mint a megadott dátum.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Entitás tulajdonságai részhalmazát
A lekérdezés Entitástulajdonságok egy részének kérheti le. Ezzel a módszerrel nevű *leképezése*, csökkenti a sávszélesség, és javíthatja a lekérdezési teljesítményt, különösen olyan nagyméretű entitásokat. Egy tulajdonság beolvasása megadásához adja át a tulajdonság nevét a **-> addSelectField** metódust. Ön a metódus hívása többször fel további tulajdonságokat. Végrehajtása után **TableRestProxy -> queryEntities**, a visszaadott entitások csak van a megadott tulajdonságokat. (Ha táblaentitásokat részhalmazát adja vissza, használjon szűrőt a lekérdezések a fenti ábrán.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Egy entitás frissítése
Használatával frissítheti a meglévő entitás a **entitás -> setProperty** és **entitás -> addProperty** entitást, és ezután hívása módszerek **TableRestProxy updateEntity->**. A következő példa egy entitás lekéri, módosítja egy tulajdonságot, egy másik tulajdonságának eltávolítja és új tulajdonsággal. Vegye figyelembe, hogy eltávolításához a tulajdonság érték beállítása **null**.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Entitás törlése
Entitás törlése, adja át a táblázat nevét, és az entitás `PartitionKey` és `RowKey` számára a **TableRestProxy -> deleteEntity** metódus.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Párhuzamossági ellenőrzésére, az Etag entitás használatával a törlendő állíthatja be a **DeleteEntityOptions -> setEtag** metódus és a sikeres a **DeleteEntityOptions** objektum  **deleteEntity** negyedik paraméterként.

## <a name="batch-table-operations"></a>Kötegelt tábla műveletek
A **TableRestProxy -> kötegelt** módszer lehetővé teszi egyetlen kérelem több műveletek végrehajtása. A mintát itt magában foglalja a műveletek hozzáadása **BatchRequest** objektumot, és ezután átadja a **BatchRequest** az objektum a **TableRestProxy -> kötegelt** metódust. A művelet hozzáadása egy **BatchRequest** objektum többször hívása a következő módszerek egyikét:

* **addInsertEntity** (hozzáad egy insertEntity művelet)
* **addUpdateEntity** (hozzáad egy updateEntity művelet)
* **addMergeEntity** (hozzáad egy mergeEntity művelet)
* **addInsertOrReplaceEntity** (hozzáad egy insertOrReplaceEntity művelet)
* **addInsertOrMergeEntity** (hozzáad egy insertOrMergeEntity művelet)
* **addDeleteEntity** (hozzáad egy deleteEntity művelet)

A következő példa bemutatja, hogyan hajthat végre **insertEntity** és **deleteEntity** egyetlen kérelem műveletei. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Tábla műveletek kötegelése kapcsolatos további információkért lásd: [entitás csoport tranzakciók végrehajtása][entity-group-transactions].

## <a name="delete-a-table"></a>Tábla törlése
Végezetül törölni kívánja a táblázatot, adja át a táblázat nevét, hogy a **TableRestProxy -> deleteTable** metódust.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>További lépések
Most, hogy megismerte az Azure Table szolgáltatás és az Azure Cosmos DB alapjait, az alábbi hivatkozásokból további.

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [A PHP fejlesztői központ](/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: http://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
