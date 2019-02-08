---
title: Az Azure Blob storage-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Blob storage-eseményindítók és kötések az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 33f79569a2478c7e234b04ba2dee4e9b8883abae
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895890"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Az Azure Blob storage-kötések az Azure Functions szolgáltatáshoz

Ez a cikk bemutatja, hogyan használható az Azure Blob storage-kötések az Azure Functions szolgáltatásban. Az Azure Functions támogatja a-trigger, bemeneti és kimeneti kötések blobok. A cikk tartalmazza minden egyes kötés szakaszát:

* [Blob trigger](#trigger)
* [BLOB bemeneti kötéssel](#input)
* [Kimeneti kötés BLOB](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> Az Event Grid eseményindító használata helyett a Blob storage-eseményindító csak a blob storage-fiókok, nagy méretű, vagy a késés csökkentése érdekében. További információkért lásd: a [eseményindító](#trigger) szakaszban.

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Blob storage-kötések szerepelnek a [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Blob storage-kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A Blob storage-eseményindító függvény elindul, egy új vagy frissített blob észlelése esetén. A blob tartalmát vannak megadva a függvény bemeneteként.

A [Event Grid-trigger](functions-bindings-event-grid.md) beépített támogatást nyújt a [blob események](../storage/blobs/storage-blob-event-overview.md) és elindítani a függvény egy új vagy frissített blob észlelése esetén is használható. Egy vonatkozó példáért tekintse meg a [kép átméretezése az Event Griddel](../event-grid/resize-images-on-storage-blob-upload-event.md) oktatóanyag.

Event Grid a Blob storage-eseményindító helyett használja a következő esetekben:

* Blob Storage-fiókok
* Nagy méretű
* Minimalizálja a hálózati késést

### <a name="blob-storage-accounts"></a>Blob Storage-fiókok

[BLOB storage-fiókok](../storage/common/storage-account-overview.md#types-of-storage-accounts) támogatottak a blob bemeneti és kimeneti kötések blob eseményindító esetében nem. Blobtároló eseményindítóiról egy általános célú tárfiók szükséges.

### <a name="high-scale"></a>Nagy méretű

Nagy méretű lazán adható meg több mint 100 000 blobok bennük rendelkező tárolókat vagy a storage-fiókok, amelyek másodpercenként több mint 100 blob frissítéseit.

### <a name="latency-issues"></a>Késési problémák

Ha a függvényalkalmazást a Használatalapú csomag, létezhet legfeljebb 10 perces késleltetést új blobok feldolgozása, ha a függvényalkalmazás inaktív volt. E késleltetés elkerülése érdekében válthat az App Service-csomag az Always On engedélyezve van. Használhatja egy [Event Grid-trigger](functions-bindings-event-grid.md) a Blob storage-fiókjában. Egy vonatkozó példáért tekintse meg a [Event Grid oktatóanyag](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Queue storage-eseményindító

Event Grid mellett egy másik lehetőség az blobok feldolgozása a Queue storage eseményindítója, de azt nem tartalmaz beépített támogatást az Azure blob-események. Hozzon létre üzenetsori üzenetek létrehozásakor vagy frissítésekor a blobok kellene. Például, hogy azt feltételezi, hogy végezze el, amely, tekintse meg a [blob bemeneti kötésnek a példában a cikk későbbi részében](#input---example).

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely ír egy naplófájl, amikor egy blob hozzáadásakor vagy frissítésekor a a `samples-workitems` tároló.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A karakterlánc `{name}` a blob eseményindító útvonal `samples-workitems/{name}` létrehoz egy [kifejezés kötés](functions-triggers-bindings.md#binding-expressions-and-patterns) használható a függvény kódját a riasztást kiváltó blob fájlneve eléréséhez. További információkért lásd: [Blob-name minták](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a `BlobTrigger` attribútumot, lásd: [eseményindító - attribútumok](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa bemutatja egy kötelező a blob eseményindító egy *function.json* fájl és [Python-kód](functions-reference-python.md) , amely a kötés használja. A függvény ír a napló, amikor egy blob hozzáadásakor vagy frissítésekor a a `samples-workitems` [tároló](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A karakterlánc `{name}` a blob eseményindító útvonal `samples-workitems/{name}` létrehoz egy [kifejezés kötés](functions-triggers-bindings.md#binding-expressions-and-patterns) használható a függvény kódját a riasztást kiváltó blob fájlneve eléréséhez. További információkért lásd: [Blob-name minták](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ *function.json* fájl tulajdonságai, tekintse meg a [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot, amely összekapcsolja a `Stream`:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Íme a C#-szkriptkódot, amely összekapcsolja a `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy kötelező a blob eseményindító egy *function.json* fájl és [JavaScript-kódot](functions-reference-node.md) , amely a kötés használja. A függvény ír a napló, amikor egy blob hozzáadásakor vagy frissítésekor a a `samples-workitems` tároló.

Íme a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A karakterlánc `{name}` a blob eseményindító útvonal `samples-workitems/{name}` létrehoz egy [kifejezés kötés](functions-triggers-bindings.md#binding-expressions-and-patterns) használható a függvény kódját a riasztást kiváltó blob fájlneve eléréséhez. További információkért lásd: [Blob-name minták](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ *function.json* fájl tulajdonságai, tekintse meg a [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Eseményindító - Python-példát

Az alábbi példa bemutatja egy kötelező a blob eseményindító egy *function.json* fájl és [Python-kód](functions-reference-python.md) , amely a kötés használja. A függvény ír a napló, amikor egy blob hozzáadásakor vagy frissítésekor a a `samples-workitems` [tároló](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Íme a *function.json* fájlt:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A karakterlánc `{name}` a blob eseményindító útvonal `samples-workitems/{name}` létrehoz egy [kifejezés kötés](functions-triggers-bindings.md#binding-expressions-and-patterns) használható a függvény kódját a riasztást kiváltó blob fájlneve eléréséhez. További információkért lásd: [Blob-name minták](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ *function.json* fájl tulajdonságai, tekintse meg a [konfigurációs](#trigger---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A Python-kód itt látható:

```python
import logging
import azure.functions as func

def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa bemutatja egy kötelező a blob eseményindító egy *function.json* fájl és [Java-kódok](functions-reference-java.md) , amely a kötés használja. A függvény ír a napló, amikor egy blob hozzáadásakor vagy frissítésekor a a `myblob` tároló.

Íme a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "file",
            "type": "blobTrigger",
            "direction": "in",
            "path": "myblob/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A Java-kód itt látható:

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```


## <a name="trigger---attributes"></a>Eseményindító - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a következő attribútumok konfigurálása a blob eseményindító:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)

  Az attribútum konstruktorának paramétereként meg egy elérési út karakterlánca, amely azt jelzi, hogy tekintse meg a tároló és opcionálisan egy [blob minta](#trigger---blob-name-patterns). Például:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  A használandó tárfiókot adjon meg egy másik megoldást kínál. A konstruktorban vesz igénybe egy tárolási kapcsolati karakterlánccal tartalmazó alkalmazásbeállítás neve. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

A használandó tárfiókot határozza meg a következő sorrendben:

* A `BlobTrigger` attribútum `Connection` tulajdonság.
* A `StorageAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `BlobTrigger` attribútum.
* A `StorageAccount` attribútum a függvény a alkalmazni.
* A `StorageAccount` attribútum az osztály a alkalmazni.
* Az alapértelmezett tárfiókot a függvényalkalmazás ("AzureWebJobsStorage" alkalmazás beállítás).

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `BlobTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `blobTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. A kivételeket jeleztük a [használati](#trigger---usage) szakaszban. |
|**name** | n/a | A változó, amely a függvény kódját a blob neve. | 
|**path** | **BlobPath** |A [tároló](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) figyeléséhez.  Előfordulhat, hogy egy [blob minta](#trigger---blob-name-patterns). | 
|**kapcsolat** | **kapcsolat** | A tárolási kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.<br><br>A kapcsolati karakterlánc nem lehet egy általános célú tárfiók olyan [Blob storage-fiók](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

A riasztást kiváltó BLOB C# és C#-szkript, használhatja a következő paraméter típusa:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Egy POCO szerializálható JSON-fájlként
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> "inout" kötés szükséges `direction` a *function.json* vagy `FileAccess.ReadWrite` egy C# osztály könyvtárban.

Ha próbál meg kötést létrehozni a Storage SDK-t típusú, és a egy hibaüzenet, ellenőrizze, hogy rendelkezik-e egy hivatkozást [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

Kötés `string`, `Byte[]`, vagy POCO csak akkor javasolt, ha a blob mérete kisebb, mint a teljes blob tartalmát betölti a memóriába. Általában célszerű használni egy `Stream` vagy `CloudBlockBlob` típusa. További információkért lásd: [egyidejűség és a memória kihasználtsági](#trigger---concurrency-and-memory-usage) a cikk későbbi részében.

A JavaScript, a bemeneti blob-adatokhoz való hozzáférés `context.bindings.<name from function.json>`.

## <a name="trigger---blob-name-patterns"></a>Eseményindító - blob neve minták

A blob neve mintát is megadhat a `path` tulajdonság *function.json* vagy a `BlobTrigger` attribútumok konstruktorában. A minta lehet egy [szűrő vagy kötési kifejezés](functions-triggers-bindings.md#binding-expressions-and-patterns). A következő szakaszok példákat.

### <a name="get-file-name-and-extension"></a>Fájl neve és a bővítmény beszerzése

Az alábbi példa bemutatja, hogyan kötést létrehozni a blob nevét és a bővítmény külön:

```json
"path": "input/{blobname}.{blobextension}",
```
Ha a blob neve *eredeti-Blob1.txt*, értékeit a `blobname` és `blobextension` változók a függvénykódot *eredeti-Blob1* és *txt*.

### <a name="filter-on-blob-name"></a>A blob neve szűrése

Az alábbi példa eseményindítók csak a blobok a `input` tárolójára, a karakterlánc "eredeti –":

```json
"path": "input/original-{name}",
```

Ha a blob neve *eredeti-Blob1.txt*, az értékét a `name` a függvény kódját a változó `Blob1`.

### <a name="filter-on-file-type"></a>A fájl típusa szűrése

Az alábbi példa kizárólag a aktivál *.png* fájlok:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>A fájlnevekben kapcsos zárójelek szűrése

A fájlnevekben kapcsos zárójelek kereséséhez a zárójelek escape két kapcsos zárójelek használatával. A következő példa szűrőket a blobokat, amelyekre a kapcsos zárójelek között szerepel a neve:

```json
"path": "images/{{20140101}}-{name}",
```

Ha a blob neve  *{20140101}-soundfile.mp3*, a `name` a függvénykódot a változó értéke *soundfile.mp3*.

## <a name="trigger---metadata"></a>Eseményindító - metaadatok

A blob eseményindító számos metaadat-tulajdonságot tartalmaz. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezekkel az értékekkel rendelkezik a azonos szemantikákkal, a [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) típusa.

|Tulajdonság  |Típus  |Leírás  |
|---------|---------|---------|
|`BlobTrigger`|`string`|A riasztást kiváltó blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-azonosítóját az elsődleges helyen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|A blob rendszer tulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A felhasználó által definiált metaadatok a BLOB.|

Például az alábbi C#-szkript és a JavaScript-példák jelentkezzen be az elérési utat a riasztást kiváltó blob, beleértve a tároló:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

## <a name="trigger---blob-receipts"></a>Eseményindító - blob visszaigazolások

Az Azure Functions runtime biztosítja, hogy nincs blob által aktivált függvény meghívása megtörténik egynél többször a azonos új vagy frissített BLOB. Meghatározni, ha egy adott blob verzió feldolgozása karbantartott *visszaigazolások blob*.

Az Azure Functions-tárolók visszaigazolások nevű tárolóban lévő blob *azure-webjobs-gazdagépek* a függvényalkalmazás Azure storage-fiókban (határozzák meg az Alkalmazásbeállítás `AzureWebJobsStorage`). Egy blob beérkezési rendelkezik a következő információkat:

* Az aktivált függvény ("*&lt;függvényalkalmazás neve >*. A műveletek.  *&lt;függvény neve >*", például: "MyFunctionApp.Functions.CopyBlob")
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag címke (például egy blob verzió azonosítója: "0x8D1DC6E70A277EF")

A blob újrafeldolgozás kényszerítéséhez blob fogadását, hogy a BLOB törlése a *azure-webjobs-gazdagépek* tároló manuálisan.

## <a name="trigger---poison-blobs"></a>Eseményindító - ártalmas blobok

Ha egy blob eseményindító függvény egy adott BLOB nem sikerül, az Azure Functions, amely alapértelmezés szerint 5-ször összesen függvény újrapróbálkozik.

Ha minden 5 alkalommal sikertelen, a az Azure Functions egy üzenetet ad hozzá nevű üzenetsor-tárolóba *webjobs-blobtrigger-poison*. Az üzenetsorban található üzenet ártalmas blobok esetében a következő JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (a következő formátumban  *&lt;függvényalkalmazás neve >*. A műveletek.  *&lt;függvény neve >*)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* BlobName
* Az ETag (például egy blob verzió azonosítója: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Eseményindító - egyidejűség és a memóriahasználat

A blob eseményindító az üzenetsor belsőleg, így az egyidejű függvény meghívásához maximális számát szabályozza a [üzenetsorok konfigurációját a host.json](functions-host-json.md#queues). Az alapértelmezett beállítások 24 indítások az egyidejűségi korlátozza. Ezt a korlátot külön-külön mindegyik függvény, amely egy blob eseményindító vonatkozik.

[A használatalapú csomag](functions-scale.md#how-the-consumption-plan-works) korlátozza egy függvényalkalmazást egy virtuális gépen (VM) 1,5 GB memória. Memória és a Functions futtatókörnyezete maga minden párhuzamosan függvény példány által használt. Ha egy blob által aktivált függvény a teljes blob betölti a memóriába, csak a blobok a függvény által használt maximális memória: 24 * blob maximális mérete. Például egy függvényalkalmazást a három blob által aktivált függvények és az alapértelmezett beállításokat kellene 3 * 24 = 72 maximális VM-enkénti egyidejűségi függvény meghívásához.

JavaScript és Java-funkciók a teljes blob betölti a memóriába, és C# funkciók, amelyek teendő, ha kötött `string`, `Byte[]`, vagy POCO.

## <a name="trigger---polling"></a>Trigger - lekérdezés

Ha a figyelt blobtárolóban több mint 10 000 BLOB tartalmaz, a Functions runtime vizsgálatok a naplófájlok új vagy módosított blobok megtekintéshez. Ez a folyamat az késleltetések eredményezheti. Függvény előfordulhat, hogy nem első indul el, amíg több percet vagy hosszabb a blob létrehozása után. Emellett [tárolási naplók jönnek létre egy "ajánlott beavatkozást"](/rest/api/storageservices/About-Storage-Analytics-Logging) alapját. Nincs garancia arra, hogy rögzítve lesznek-e az összes eseményt. Bizonyos körülmények között a naplók kimaradhatnak. Ha a gyorsabb és megbízhatóbb blob feldolgozás van szüksége, érdemes lehet létrehozni egy [üzenetsori üzenet](../storage/queues/storage-dotnet-how-to-use-queues.md) létrehozásakor, a blob. Ezután egy [üzenetsor eseményindító](functions-bindings-storage-queue.md) feldolgozni a blob blob eseményindító helyett. Egy másik lehetőség az, hogy az Event Grid; használata Lásd a következő oktatóanyagot [feltöltött Event Grid használatával képek átméretezésének automatizálása](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="input"></a>Input (Bemenet)

A Blob storage bemeneti kötés használatával olvasni a blobokat.

## <a name="input---example"></a>Adjon meg – példa

Tekintse meg az adott nyelvű példa:

* [C#](#input---c-example)
* [C# script (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Bemenet – C#-példa

Az alábbi példa egy [C#-függvény](functions-dotnet-class-library.md) egy üzenetsor eseményindító és a egy bemeneti blob-kötést használó. Az üzenetsorban található üzenet a blob nevét tartalmazza, és a függvény naplózza a blob mérete.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```        

### <a name="input---c-script-example"></a>Bemenet – C#-szkript példa

<!--Same example for input and output. -->

Az alábbi példa bemutatja a blob bemeneti és kimeneti kötése egy *function.json* fájl és [C#-szkript (.csx)](functions-reference-csharp.md) kódokat, amelyek a kötéseket. A funkció lehetővé teszi a szöveges blob egy példányát. A függvény, amely tartalmazza a blob másolásához nevét, egy üzenetsor által aktivált. Az új blob neve *{originalblobname}-példány*.

Az a *function.json* fájlt, a `queueTrigger` metaadat-tulajdonságot a blob nevének megadására szolgál a `path` tulajdonságai:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input---javascript-example"></a>Bemenet - JavaScript-példa

<!--Same example for input and output. -->

Az alábbi példa bemutatja a blob bemeneti és kimeneti kötése egy *function.json* fájl és [JavaScript-kódot](functions-reference-node.md) , amely használja a kötéseket. A funkció lehetővé teszi, hogy egy blob egy példányát. A függvény, amely tartalmazza a blob másolásához nevét, egy üzenetsor által aktivált. Az új blob neve *{originalblobname}-példány*.

Az a *function.json* fájlt, a `queueTrigger` metaadat-tulajdonságot a blob nevének megadására szolgál a `path` tulajdonságai:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="input---python-example"></a>Bemenet – Python-példát

<!--Same example for input and output. -->

Az alábbi példa bemutatja a blob bemeneti és kimeneti kötése egy *function.json* fájl és [Python-kód](functions-reference-python.md) , amely használja a kötéseket. A funkció lehetővé teszi, hogy egy blob egy példányát. A függvény, amely tartalmazza a blob másolásához nevét, egy üzenetsor által aktivált. Az új blob neve *{originalblobname}-példány*.

Az a *function.json* fájlt, a `queueTrigger` metaadat-tulajdonságot a blob nevének megadására szolgál a `path` tulajdonságai:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A Python-kód itt látható:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Bemenet - Java-példák

Ez a szakasz tartalmazza az alábbi példák:

* [HTTP-eseményindító, keresse meg a lekérdezési karakterláncot a blob neve](#http-trigger-look-up-blob-name-from-query-string-java)
* [Várólista-eseményindító, blobnév fogadjon üzenetsori üzenet](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>HTTP-eseményindító, keresse meg a blob nevét, a lekérdezési karakterlánc (Java)

 Az alábbi példa bemutatja egy Java-függvény, amely használja a ```HttpTrigger``` jegyzet fogadásához egy fájlt a blob storage-tároló nevét tartalmazó paraméter. A ```BlobInput``` jegyzet majd beolvassa a fájlt, és adja át annak tartalmát a függvényt, egy ```byte[]```.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Várólista-eseményindító, blobnév fogadjon az üzenetsorban található üzenet (Java)

 Az alábbi példa bemutatja egy Java-függvény, amely használja a ```QueueTrigger``` jegyzet egy fájlt a blob storage-tároló nevét tartalmazó üzenetet fogadni. A ```BlobInput``` jegyzet majd beolvassa a fájlt, és adja át annak tartalmát a függvényt, egy ```byte[]```.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@BlobInput` jegyzet paraméterekkel, amelynek az értéke egy blob kellene származnia.  A jegyzet használható natív Java-típusokat, POJOs vagy nullázható értékek használatával `Optional<T>`.

## <a name="input---attributes"></a>Bemenet - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktorának paramétereként meg az elérési utat a blobra mutató és a egy `FileAccess` paraméter olvasási vagy írási, jelezve, az alábbi példában látható módon:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Használhatja a `StorageAccount` attribútumot az osztályra, módszer vagy paraméter szintű tárfiókot adjon meg. További információkért lásd: [eseményindító - attribútumok](#trigger---attributes).

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Blob` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `blob`. |
|**direction** | n/a | Meg kell `in`. A kivételeket jeleztük a [használati](#input---usage) szakaszban. |
|**name** | n/a | A változó, amely a függvény kódját a blob neve.|
|**path** |**BlobPath** | A blob elérési útja. |
|**kapcsolat** |**kapcsolat**| Tartalmazó alkalmazásbeállítás neve a [tárolási kapcsolati karakterlánc](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-azure-storage-account) használata ehhez a kötéshez. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.<br><br>A kapcsolati karakterlánc nem lehet egy általános célú tárfiók olyan [csak blob storage-fiók](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **Access (Hozzáférés)** | Azt jelzi, hogy meg fog kell olvasása vagy írása. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A blob bemeneti kötésnek a C# és C#-szkript, használhatja a következő paraméter típusa:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> "inout" kötés szükséges `direction` a *function.json* vagy `FileAccess.ReadWrite` egy C# osztály könyvtárban.

Ha próbál meg kötést létrehozni a Storage SDK-t típusú, és a egy hibaüzenet, ellenőrizze, hogy rendelkezik-e egy hivatkozást [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

Kötés `string` vagy `Byte[]` csak akkor javasolt, ha a blob mérete kisebb,-e, a teljes blob tartalmát a memóriába betöltött. Általában célszerű használni egy `Stream` vagy `CloudBlockBlob` típusa. További információkért lásd: [egyidejűség és a memória kihasználtsági](#trigger---concurrency-and-memory-usage) a cikk elején.

A JavaScript, a blob adatait az eléréséhez `context.bindings.<name from function.json>`.

## <a name="output"></a>Kimenet

A Blob storage kimeneti kötések segítségével írhat a blobokat.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

Az alábbi példa egy [C#-függvény](functions-dotnet-class-library.md) , amely egy blob eseményindító használ, és két kimeneti blob kötéseit. A függvény aktiválódik egy kép található blob létrehozása az *minta-lemezképek* tároló. A kép blob kis és közepes méretű másolatát hozza létre.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

<!--Same example for input and output. -->

Az alábbi példa bemutatja a blob bemeneti és kimeneti kötése egy *function.json* fájl és [C#-szkript (.csx)](functions-reference-csharp.md) kódokat, amelyek a kötéseket. A funkció lehetővé teszi a szöveges blob egy példányát. A függvény, amely tartalmazza a blob másolásához nevét, egy üzenetsor által aktivált. Az új blob neve *{originalblobname}-példány*.

Az a *function.json* fájlt, a `queueTrigger` metaadat-tulajdonságot a blob nevének megadására szolgál a `path` tulajdonságai:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

<!--Same example for input and output. -->

Az alábbi példa bemutatja a blob bemeneti és kimeneti kötése egy *function.json* fájl és [JavaScript-kódot](functions-reference-node.md) , amely használja a kötéseket. A funkció lehetővé teszi, hogy egy blob egy példányát. A függvény, amely tartalmazza a blob másolásához nevét, egy üzenetsor által aktivált. Az új blob neve *{originalblobname}-példány*.

Az a *function.json* fájlt, a `queueTrigger` metaadat-tulajdonságot a blob nevének megadására szolgál a `path` tulajdonságai:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

### <a name="output---python-example"></a>Kimenet – Python-példát

<!--Same example for input and output. -->

Az alábbi példa bemutatja a blob bemeneti és kimeneti kötése egy *function.json* fájl és [Python-kód](functions-reference-python.md) , amely használja a kötéseket. A funkció lehetővé teszi, hogy egy blob egy példányát. A függvény, amely tartalmazza a blob másolásához nevét, egy üzenetsor által aktivált. Az új blob neve *{originalblobname}-példány*.

Az a *function.json* fájlt, a `queueTrigger` metaadat-tulajdonságot a blob nevének megadására szolgál a `path` tulajdonságai:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A Python-kód itt látható:

```python
import logging
import azure.functions as func

def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

### <a name="output---java-examples"></a>Kimenet – Java-példák

Ez a szakasz tartalmazza az alábbi példák:

* [HTTP-eseményindító OutputBinding használatával](#http-trigger-using-outputbinding-java)
* [Üzenetsor eseményindító függvény visszaadott értékének használata](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-eseményindító OutputBinding (Java) használatával

 Az alábbi példa bemutatja egy Java-függvény, amely használja a ```HttpTrigger``` jegyzet fogadásához egy fájlt a blob storage-tároló nevét tartalmazó paraméter. A ```BlobInput``` jegyzet majd beolvassa a fájlt, és adja át annak tartalmát a függvényt, egy ```byte[]```. A ```BlobOutput``` jegyzet összekapcsolja ```OutputBinding outputItem```, majd amelyet a függvény a bemeneti blob tartalmának írása a konfigurált tárolóba.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Üzenetsor eseményindító függvény visszaadott értékének (Java) használatával

 Az alábbi példa bemutatja egy Java-függvény, amely használja a ```QueueTrigger``` jegyzet egy fájlt a blob storage-tároló nevét tartalmazó üzenetet fogadni. A ```BlobInput``` jegyzet majd beolvassa a fájlt, és adja át annak tartalmát a függvényt, egy ```byte[]```. A ```BlobOutput``` jegyzet a függvény visszaadott értékének, a modul által a bemeneti blob tartalmának írása a konfigurált tárolóba majd használt van kötve.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `@BlobOutput` függvény paraméterei, amelynek az értéke az a blob storage-objektum tartalmazná a jegyzet.  A paraméter típusa legyen `OutputBinding<T>`, ahol a T natív Java-típusra vagy egy pojo-vá.

## <a name="output---attributes"></a>Kimenet – attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktorának paramétereként meg az elérési utat a blobra mutató és a egy `FileAccess` paraméter olvasási vagy írási, jelezve, az alábbi példában látható módon:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Beállíthatja a `Connection` tulajdonságot adja meg a storage-fiókot szeretne használni, az alábbi példában látható módon:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

Használhatja a `StorageAccount` attribútumot az osztályra, módszer vagy paraméter szintű tárfiókot adjon meg. További információkért lásd: [eseményindító - attribútumok](#trigger---attributes).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Blob` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `blob`. |
|**direction** | n/a | Meg kell `out` a kimeneti kötés. A kivételeket jeleztük a [használati](#output---usage) szakaszban. |
|**name** | n/a | A változó, amely a függvény kódját a blob neve.  Állítsa be `$return` való hivatkozáshoz függvény visszatérési értéke.|
|**path** |**BlobPath** | A blobco elérési útja. |
|**kapcsolat** |**kapcsolat**| A tárolási kapcsolati karakterlánc használata ehhez a kötéshez tartalmazó alkalmazásbeállítás neve. Azon alkalmazásbeállítás neve "AzureWebJobs" kezdődik, ha csak a maradékot Itt a neve is megadhat. Például, ha a beállított `connection` a "MyStorage", a Functions futtatókörnyezete úgy tűnik, a beállítás, amely alkalmazás neve "AzureWebJobsMyStorage." Ha meghagyja a `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc nevű Alkalmazásbeállítás `AzureWebJobsStorage`.<br><br>A kapcsolati karakterlánc nem lehet egy általános célú tárfiók olyan [csak blob storage-fiók](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **Access (Hozzáférés)** | Azt jelzi, hogy meg fog kell olvasása vagy írása. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

A C# és a C#-szkript hozhasson létre a következő típusú blobok írni:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> "a" kötés szükséges `direction` a *function.json* vagy `FileAccess.Read` egy C# osztály könyvtárban. A tárolóobjektum, amellyel a modul írási művelet, például blobok feltöltése a tárolóba is használhatja.

<sup>2</sup> "inout" kötés szükséges `direction` a *function.json* vagy `FileAccess.ReadWrite` egy C# osztály könyvtárban.

Ha próbál meg kötést létrehozni a Storage SDK-t típusú, és a egy hibaüzenet, ellenőrizze, hogy rendelkezik-e egy hivatkozást [a megfelelő Storage SDK-verzió](#azure-storage-sdk-version-in-functions-1x).

Az aszinkron funkciók, a visszaadott értékének használata vagy `IAsyncCollector` helyett egy `out` paraméter.

Kötés `string` vagy `Byte[]` csak akkor javasolt, ha a blob mérete kisebb,-e, a teljes blob tartalmát a memóriába betöltött. Általában célszerű használni egy `Stream` vagy `CloudBlockBlob` típusa. További információkért lásd: [egyidejűség és a memória kihasználtsági](#trigger---concurrency-and-memory-usage) a cikk elején.


A JavaScript, a blob adatait az eléréséhez `context.bindings.<name from function.json>`.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés |  Leírások |
|---|---|
| Blob | [A BLOB-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Table, Queue |  [Storage-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Hibaelhárítás](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

* [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
