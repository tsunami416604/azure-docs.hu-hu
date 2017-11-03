---
title: "Php-ből a table storage használata |} Microsoft Docs"
description: "Használata php-ből a Table szolgáltatás létrehozásához, és törölni kívánja a táblázatot, és helyezze, törlése, és a tábla lekérdezése."
services: cosmos-db
documentationcenter: php
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: 1e57f371-6208-4753-b2a0-05db4aede8e3
ms.service: cosmos-db
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 12/08/2016
ms.author: mimig
ms.openlocfilehash: 7a48446a11c5c6db0c9f4fdd8872b1e3c12e85c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-table-storage-from-php"></a>Php-ből a table storage használata
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-langsoon-tip-include](../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre a gyakori forgatókönyvek az Azure Table szolgáltatással. A mintákat a PHP és -felhasználási nyelven íródtak a [Azure SDK for PHP][download]. Az ismertetett forgatókönyvek **létrehozása és egy tábla törlésével és beszúrását, törlését és entitások egy tábla lekérdezése**. Az Azure Table szolgáltatás további információkért tekintse meg a [további lépések](#next-steps) szakasz.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása
A csak az Azure Table szolgáltatás hozzáférő PHP-alkalmazások létrehozására vonatkozó mérete a hivatkozási osztályok az Azure SDK-ban a php a kód. A fejlesztői eszközök hozhat létre az alkalmazás, például a Jegyzettömbbel.

Ebben az útmutatóban hívható a PHP-alkalmazás helyileg, vagy egy Azure webes szerepkörről, a feldolgozói szerepkör vagy a webhely belül futó tábla szolgáltatás funkciók használatára.

## <a name="get-the-azure-client-libraries"></a>Az Azure Ügyfélkódtárai beolvasása
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-access-the-table-service"></a>Állítsa be az alkalmazását, a Table szolgáltatás eléréséhez
Az Azure Table szolgáltatás API-kat használ, meg kell:

1. A robotot fájl használatával hivatkozik a [require_once] [ require_once] utasítást, és
2. Bármely osztályok segítségével lehet hivatkozni.

A következő példa bemutatja, hogyan a robotot fájl és a hivatkozás a **ServicesBuilder** osztály.

> [!NOTE]
> Ebben a cikkben szereplő példák azt feltételezik, hogy telepítette az Azure-szerkesztő segítségével, a PHP Klienskódtárak segítségével. Ha a könyvtárak manuálisan telepítette, hivatkoznia kell a <code>WindowsAzure.php</code> robotjába fájlt.
>
>

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Az alábbi példákban a `require_once` utasítás mindig megjelenik, de csak a szükséges végrehajtandó például osztályok hivatkozott.

## <a name="set-up-an-azure-storage-connection"></a>Az Azure storage-kapcsolat beállítása
Az Azure Table szolgáltatásügyfél hozható létre, először egy érvényes kapcsolati karakterláncot kell rendelkeznie. A szolgáltatás kapcsolati karakterlánc formátuma:

Élő szolgáltatások elérésére:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Az emulátor tárolóinak eléréséhez:

```php
UseDevelopmentStorage=true
```

Bármely Azure szolgáltatás ügyfele létrehozásához kell használnia a **ServicesBuilder** osztály. A következőket teheti:

* a kapcsolati karakterláncot adja át a közvetlenül vagy
* használja a **CloudConfigurationManager (CCM)** ellenőrizze a kapcsolati karakterlánc több külső forrás:
  * Alapértelmezés szerint mellékelt egy külső adatforrás - környezeti változók támogatása
  * új forrásból történő kiterjesztésével adhat hozzá a **ConnectionStringSource** osztály

Az itt leírt példák a kapcsolati karakterlánc közvetlenül fog adható át.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
```

## <a name="create-a-table"></a>Tábla létrehozása
A **TableRestProxy** objektum lehetővé teszi, hogy a tábla létrehozása a **createTable** metódust. Ha a táblázatok létrehozásáról, beállíthatja a Table szolgáltatás időtúllépés. (A Table szolgáltatás időtúllépési kapcsolatos további információkért lásd: [időtúllépések beállítása a szolgáltatási műveletek tábla][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Create table.
    $tableRestProxy->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
}
```

Táblanevek vonatkozó megkötésekkel kapcsolatos információkért lásd: [ismertetése a Table szolgáltatás adatmodell][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Egy entitás felvételére egy táblához, hozzon létre egy új **entitás** objektumot, és adja át a **TableRestProxy -> insertEntity**. Vegye figyelembe, hogy egy entitás létrehozásakor meg kell adnia egy `PartitionKey` és `RowKey`. Egy entitás egyedi azonosítói azok és értékek, amelyek sokkal gyorsabb, mint más entitás tulajdonságai kérdezhetők le. A rendszer használja `PartitionKey` automatikusan terjesztené a táblaentitásokat számos tárolási csomópont feladatait. Entitások azonos `PartitionKey` ugyanazon a csomóponton találhatók. (Ugyanazon a csomóponton tárolt entitásokat műveletek végrehajtása jobb, mint a más csomópontjai között tárolt entitásokat.) A `RowKey` egy partíció egy entitás egyedi azonosítója.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableRestProxy->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

További információ a táblázat tulajdonságai és típusok: [ismertetése a Table szolgáltatás adatmodell][table-data-model].

A **TableRestProxy** osztály entitások beszúrására szolgáló két alternatív módszert kínál: **insertOrMergeEntity** és **insertOrReplaceEntity**. Ezeket a módszereket használja, hozzon létre egy új **entitás** , és adja át paraméterként mindkét módszer. Az egyes módszerek szúrnak az entitás, ha nem létezik. Ha már létezik az entitás, **insertOrMergeEntity** tulajdonságértékek frissíti, ha a tulajdonságai már léteznek, és hozzáadja az új tulajdonságok ha azok nem léteznek, miközben **insertOrReplaceEntity** teljesen lecseréli a meglévő entitás. A következő példa bemutatja, hogyan használható **insertOrMergeEntity**. Ha az entitás `PartitionKey` "tasksSeattle" és `RowKey` "1" már nem létezik, szúrja be. Azonban ha azt korábban beszúrt (a fenti példa szerint), a `DueDate` tulajdonság frissülni fog, és a `Status` tulajdonság megkapja. A `Description` és `Location` tulajdonságok is frissülnek, de értékekkel, amely hatékonyan hagyja őket változatlan marad. Ha ez utóbbi a tulajdonság a volt nem szerepel a példában látható módon, de a célentitás már létezett, meglévő értékeik változatlan marad.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

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
    $tableRestProxy->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
A **TableRestProxy -> getEntity** módszer lehetővé teszi egyetlen entitás lekérdezése által végzett lekérdezés a `PartitionKey` és `RowKey`. A partíciós kulcs az alábbi példában a `tasksSeattle` és sorkulcsa `1` kerülnek átadásra a **getEntity** metódust.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableRestProxy->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
A lekérdezés Entitástulajdonságok egy részének kérheti le. Ezzel a módszerrel nevű *leképezése*, csökkenti a sávszélesség, és javíthatja a lekérdezési teljesítményt, különösen olyan nagyméretű entitásokat. Kérhető tulajdonság megadásához adja át a tulajdonság nevét a **-> addSelectField** metódust. Ön a metódus hívása többször fel további tulajdonságokat. Végrehajtása után **TableRestProxy -> queryEntities**, a visszaadott entitások csak van a megadott tulajdonságokat. (Ha táblaentitásokat részhalmazát adja vissza, használjon szűrőt a lekérdezések a fenti ábrán.)

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableRestProxy->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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
Meglévő entitás használatával frissíthető a **entitás -> setProperty** és **entitás -> addProperty** entitást, és ezután hívása módszerek **TableRestProxy updateEntity ->**. A következő példa egy entitás lekéri, módosítja egy tulajdonságot, egy másik tulajdonságának eltávolítja és új tulajdonsággal. Vegye figyelembe, hogy eltávolításához a tulajdonság érték beállítása **null**.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();

$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.

$entity->setPropertyValue("Location", null); //Removed Location.

$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableRestProxy->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Entitás törlése
Entitás törlése, adja át a táblázat nevét, és az entitás `PartitionKey` és `RowKey` számára a **TableRestProxy -> deleteEntity** metódus.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete entity.
    $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Vegye figyelembe, hogy párhuzamossági ellenőrzések állíthatók be a Etag, hogy egy entitás használatával a törlendő a **DeleteEntityOptions -> setEtag** metódus és a sikeres a **DeleteEntityOptions** objektum **deleteEntity** negyedik paraméterként.

## <a name="batch-table-operations"></a>Kötegelt tábla műveletek
A **TableRestProxy -> kötegelt** módszer lehetővé teszi egyetlen kérelem több műveletek végrehajtása. A mintát itt magában foglalja a műveletek hozzáadása **BatchRequest** objektumot, és ezután átadja a **BatchRequest** az objektum a **TableRestProxy -> kötegelt** metódust. A művelet hozzáadása egy **BatchRequest** objektum többször hívása a következő módszerek egyikét:

* **addInsertEntity** (hozzáad egy insertEntity művelet)
* **addUpdateEntity** (hozzáad egy updateEntity művelet)
* **addMergeEntity** (hozzáad egy mergeEntity művelet)
* **addInsertOrReplaceEntity** (hozzáad egy insertOrReplaceEntity művelet)
* **addInsertOrMergeEntity** (hozzáad egy insertOrMergeEntity művelet)
* **addDeleteEntity** (hozzáad egy deleteEntity művelet)

A következő példa bemutatja, hogyan hajthat végre **insertEntity** és **deleteEntity** egyetlen kérelem műveletek:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

    // Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

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
    $tableRestProxy->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
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

use WindowsAzure\Common\ServicesBuilder;
use MicrosoftAzure\Storage\Common\ServiceException;

// Create table REST proxy.
$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

try    {
    // Delete table.
    $tableRestProxy->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/dd179438.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte az Azure Table szolgáltatás alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről.

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [A PHP fejlesztői központ](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filters]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx
