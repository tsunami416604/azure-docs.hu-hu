---
title: Azure Blob Storage-kötések Azure Functionshoz
description: Ismerje meg, hogyan használható az Azure Blob Storage-eseményindítók és-kötések Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: 593d596881d0ec53b0f18e8af5a71b55c619f7a5
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922120"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob Storage-kötések Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használható az Azure Blob Storage-kötések Azure Functions. Azure Functions támogatja a Blobok trigger-, bemeneti és kimeneti kötéseit. A cikk minden kötéshez tartalmaz egy szakaszt:

* [BLOB-trigger](#trigger)
* [BLOB bemeneti kötése](#input)
* [BLOB kimeneti kötése](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> A blob Storage-trigger helyett használja a Event Grid triggert a blob Storage-fiókokhoz, nagy léptékű vagy a késés csökkentése érdekében. További információ: [trigger](#trigger) szakasz.

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

A blob Storage-kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomagban, 2. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

A blob Storage-kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomagban, 3. x verzióban érhetők el. A csomag forráskódja az [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Eseményindító

A blob Storage-trigger új vagy frissített blob észlelésekor egy függvényt indít el. A blob tartalma bemenetként van megadva a függvénynek.

A [Event Grid trigger](functions-bindings-event-grid.md) beépített támogatást nyújt a [blob eseményeihez](../storage/blobs/storage-blob-event-overview.md) , és egy új vagy frissített blob észlelésekor egy függvény elindítására is használható. Példaként tekintse meg a [rendszerkép átméretezése Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) oktatóanyaggal című témakört.

A blob Storage-trigger helyett használja Event Grid a következő helyzetekben:

* Blob Storage-fiókok
* Nagy léptékű
* Minimális késés

### <a name="blob-storage-accounts"></a>Blob Storage-fiókok

A blob [Storage-fiókok](../storage/common/storage-account-overview.md#types-of-storage-accounts) támogatják a blob bemeneti és kimeneti kötéseit, a blob-eseményindítók esetében azonban nem. A blob Storage-eseményindítók általános célú Storage-fiókot igényelnek.

### <a name="high-scale"></a>Nagy léptékű

A nagy léptékű tárolók olyan tárolók, amelyek több mint 100 000 blobtal rendelkeznek, vagy amelyek másodpercenként több mint 100 blob-frissítéssel rendelkeznek.

### <a name="latency-issues"></a>Késési problémák

Ha a Function alkalmazás a használati terven van, akkor akár 10 percet is igénybe vehet az új Blobok feldolgozásakor, ha egy Function alkalmazás üresjáratban van. Ha el szeretné kerülni ezt a késést, átválthat egy App Service-csomagra az Always On enabled lehetőséggel. Egy [Event Grid triggert](functions-bindings-event-grid.md) is használhat a blob Storage-fiókkal. Példaként tekintse meg a [Event Grid oktatóanyagot](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Queue Storage-eseményindító

Event Grid mellett a Blobok feldolgozásának egy másik alternatívája a várólista-tároló triggere, de nem rendelkezik beépített támogatással a blob eseményeihez. A Blobok létrehozásakor vagy frissítésekor üzenetsor-üzeneteket kell létrehoznia. A jelen [cikk későbbi, a blob bemeneti kötési példája című részében](#input---example)talál egy példát, amely azt feltételezi, hogy ezt elvégezte.

## <a name="trigger---example"></a>Trigger – példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely a `samples-workitems` tárolóban lévő blob hozzáadásakor vagy frissítésekor naplót ír.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A blob trigger elérési útján `{name}` sztring `samples-workitems/{name}` egy olyan [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a `BlobTrigger` attribútumról: [trigger-attributes](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az alábbi példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és a kötést használó kódban. A függvény naplót ír a `samples-workitems` [tárolóban](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)lévő blob hozzáadásakor vagy frissítésekor.

Itt található a *function. JSON* fájlban található kötési adat:

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

A blob trigger elérési útján `{name}` sztring `samples-workitems/{name}` egy olyan [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a *function. JSON* fájl tulajdonságairól: [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Az alábbi C# parancsfájl-kód a `Stream`hoz kötődik:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Az alábbi C# parancsfájl-kód a `CloudBlockBlob`hoz kötődik:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A következő példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötést használja. A függvény naplót ír a `samples-workitems` tárolóban lévő blob hozzáadásakor vagy frissítésekor.

Itt látható a *function. JSON* fájl:

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

A blob trigger elérési útján `{name}` sztring `samples-workitems/{name}` egy olyan [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a *function. JSON* fájl tulajdonságairól: [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt látható a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és a kötést használó [Python-kódban](functions-reference-python.md) . A függvény naplót ír a `samples-workitems` [tárolóban](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)lévő blob hozzáadásakor vagy frissítésekor.

Itt látható a *function. JSON* fájl:

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

A blob trigger elérési útján `{name}` sztring `samples-workitems/{name}` egy olyan [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a *function. JSON* fájl tulajdonságairól: [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Az alábbi példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és a kötést használó [Java-kódban](functions-reference-java.md) . A függvény naplót ír a `myblob` tárolóban lévő blob hozzáadásakor vagy frissítésekor.

Itt látható a *function. JSON* fájl:

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

A Java-kód a következő:

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

---

## <a name="trigger---attributes"></a>Trigger – attribútumok

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat a blob-trigger konfigurálásához:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Az attribútum konstruktora egy elérésiút-karakterláncot vesz fel, amely jelzi a tárolót, és opcionálisan egy [blob-nevet](#trigger---blob-name-patterns). Például:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  A teljes példa: [trigger példa](#trigger---example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

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

A használandó Storage-fiók a következő sorrendben van meghatározva:

* A `BlobTrigger` attribútum `Connection` tulajdonsága.
* A `StorageAccount` attribútum a `BlobTrigger` attribútummal megegyező paraméterre lett alkalmazva.
* A függvényre alkalmazott `StorageAccount` attribútum.
* Az osztályra alkalmazott `StorageAccount` attribútum.
* A Function alkalmazás alapértelmezett Storage-fiókja ("AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A `@BlobTrigger` attribútummal hozzáférést biztosíthat a függvényt kiváltó blobhoz. A részletekért tekintse meg az [trigger példáját](#trigger---example) .

---

## <a name="trigger---configuration"></a>Trigger – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `BlobTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | `blobTrigger`értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | – | `in`értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. A kivételek a [használat](#trigger---usage) szakaszban vannak feltüntetve. |
|**név** | – | A blobot jelölő változó neve a függvény kódjában. |
|**elérési útja** | **BlobPath** |A figyelni kívánt [tároló](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) .  A [blob neve minta](#trigger---blob-name-patterns)lehet. |
|**kapcsolat** | **Kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Trigger – használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A Blobok adatai a `context.bindings.<NAME>` használatával érhetők el, ahol a `<NAME>` megegyezik a *function. JSON*fájlban megadott értékkel.

# <a name="pythontabpython"></a>[Python](#tab/python)

A blob-adatelérést a [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)típussal megadott paraméterrel érheti el. A részletekért tekintse meg az [trigger példáját](#trigger---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

A `@BlobTrigger` attribútummal hozzáférést biztosíthat a függvényt kiváltó blobhoz. A részletekért tekintse meg az [trigger példáját](#trigger---example) .

---

## <a name="trigger---blob-name-patterns"></a>Trigger – blob neve mintázat

A blob neve mintát megadhatja a *function. JSON* fájl `path` tulajdonságában vagy a `BlobTrigger` Attribute konstruktorban. A név minta lehet [szűrő vagy kötési kifejezés](./functions-bindings-expressions-patterns.md)is. A következő szakasz példákat tartalmaz.

### <a name="get-file-name-and-extension"></a>Fájl nevének és kiterjesztésének beolvasása

Az alábbi példa bemutatja, hogyan köthető a blob-fájl nevéhez és kiterjesztéséhez:

```json
"path": "input/{blobname}.{blobextension}",
```

Ha a blob neve *Original-Blob1. txt*, a függvény kódjában található `blobname` és `blobextension` változók értékei: *Original-Blob1* és *txt*.

### <a name="filter-on-blob-name"></a>Szűrés a blob nevén

Az alábbi példa csak a "Original-" karakterlánccal kezdődő `input` tárolóban lévő blobokat indítja el:

```json
"path": "input/original-{name}",
```

Ha a blob neve *Original-Blob1. txt*, a függvény kódja `name` változó értéke `Blob1`.

### <a name="filter-on-file-type"></a>Szűrés fájltípus alapján

A következő példa csak a *. png* fájlok esetében indít el:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Kapcsos zárójelek szűrése fájlnevekben

Ha kapcsos zárójeleket szeretne keresni a fájlnevekben, a kapcsos zárójeleket két zárójel használatával kell kipróbálni. A következő példa azokat a blobokat szűri, amelyek kapcsos zárójelekkel rendelkeznek a névben:

```json
"path": "images/{{20140101}}-{name}",
```

Ha a blob neve *{20140101}-soundfile. mp3*, a függvény kódjában található `name` változó értéke *hangállomány. mp3*.

## <a name="trigger---metadata"></a>Trigger – metaadatok

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

A metaadatok nem érhetők el a Pythonban.

# <a name="javatabjava"></a>[Java](#tab/java)

A metaadatok nem érhetők el a javában.

---

## <a name="trigger---blob-receipts"></a>Trigger – blob-visszaigazolások

A Azure Functions futtatókörnyezet biztosítja, hogy a blob trigger függvény többször is meghívja ugyanazt az új vagy frissített blobot. Annak megállapításához, hogy egy adott blob-verzió feldolgozása megtörtént-e, a *blob-visszaigazolásokat*tart fenn.

Azure Functions a blob-visszaigazolásokat egy *Azure-webjobs* nevű tárolóban tárolja – a Function alkalmazás Azure Storage-fiókjában lévő gazdagépeket (az Alkalmazásbeállítások `AzureWebJobsStorage`). A blob-visszaigazolás a következő információkat tartalmazhatja:

* Az aktivált függvény (" *&lt;Function app name >* . Funkciók. *&lt;függvény neve >* ", például:" MyFunctionApp. functions. CopyBlob ")
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* A ETag (a blob verziószáma, például: "0x8D1DC6E70A277EF")

Egy blob újrafeldolgozásának kényszerítéséhez törölje az adott blobhoz tartozó blob-elismervényt az *Azure-webjobs-hosts* tárolóból manuálisan. Előfordulhat, hogy az újrafeldolgozás nem azonnal, hanem egy későbbi időpontban történik.

## <a name="trigger---poison-blobs"></a>Trigger – mérges Blobok

Ha egy blob trigger-függvény meghibásodik egy adott blobnál, Azure Functions az újrapróbálkozások alapértelmezés szerint összesen 5 alkalommal működnek.

Ha mind az 5 próbálkozás sikertelen, Azure Functions hozzáadja az üzenetet egy *webjobs-blobtrigger-méreg*nevű Storage-várólistához. A méreg-Blobok üzenetsor-üzenete egy JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (formátumban *&lt;Function alkalmazás neve >* . Funkciók. *&lt;függvény neve >* )
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* BlobName
* ETag (blob-verzió azonosítója, például: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger – párhuzamosságok és memóriahasználat

A blob-trigger belsőleg használ egy várólistát, így az egyidejű függvények maximális száma a [Host. JSON fájlban található várólisták konfigurációjának megfelelően](functions-host-json.md#queues)van szabályozva. Az alapértelmezett beállítások a párhuzamosságot 24 hívásra korlátozzák. Ez a korlát külön vonatkozik a blob-triggert használó összes függvényre.

[A használati terv az](functions-scale.md#how-the-consumption-and-premium-plans-work) egyik virtuális GÉPEN (VM) lévő function alkalmazást 1,5 GB memóriára korlátozza. A memóriát a párhuzamosan futó és a functions Runtime is használja. Ha egy blob által aktivált függvény a teljes blobot betölti a memóriába, az adott függvény által a Blobok által használt maximális memória 24 * a blob maximális mérete. Például a három blob által aktivált függvényt használó Function alkalmazás, valamint az alapértelmezett beállítások esetében a 3 * 24 = 72 függvény meghívása esetén a virtuális gépek maximális száma.

A JavaScript és a Java függvények a teljes blobot a memóriába töltik be, és C# ha `string`hoz, `Byte[]`hoz vagy pocohöz kötődnek.

## <a name="trigger---polling"></a>Trigger – lekérdezés

A lekérdezés hibridként működik a naplók vizsgálata és az időszakos tároló-ellenőrzések futtatása között. A Blobok a 10 000-es csoportokba vannak beolvasva, és az intervallumok közötti folytatási tokent használják.

> [!WARNING]
> Emellett a [tárolási naplók a "legjobb megoldás" alapon jönnek létre](/rest/api/storageservices/About-Storage-Analytics-Logging) . Nem garantálható, hogy minden esemény rögzítve legyen. Bizonyos körülmények között előfordulhat, hogy a naplók kimaradnak.
> 
> Ha gyorsabb vagy megbízhatóbb blob-feldolgozásra van szüksége, érdemes lehet üzenetsor- [üzenetet](../storage/queues/storage-dotnet-how-to-use-queues.md) létrehozni a blob létrehozásakor. Ezután használjon egy [üzenetsor-triggert](functions-bindings-storage-queue.md) a blob-trigger helyett a blob feldolgozásához. Egy másik lehetőség a Event Grid használata; Tekintse meg az oktatóanyagot, amely [automatizálja a feltöltött képek átméretezését Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Input (Bemenet)

BLOB Storage-beli bemeneti kötés használata a Blobok olvasásához.

## <a name="input---example"></a>Bemenet – példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A következő példa egy [ C# ](functions-dotnet-class-library.md) üzenetsor-triggert és egy bemeneti blob-kötést használó függvény. Az üzenetsor-üzenet tartalmazza a blob nevét, és a függvény naplózza a blob méretét.

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájl és [ C# parancsfájl (. CSX)](functions-reference-csharp.md) kódban, amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metadata tulajdonság a blob nevének megadására szolgál a `path` tulajdonságaiban:

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metadata tulajdonság a blob nevének megadására szolgál a `path` tulajdonságaiban:

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

A következő példa egy *function. JSON* fájlban és a kötéseket használó [Python-kódban](functions-reference-python.md) mutatja be a blob bemeneti és kimeneti kötéseit. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metadata tulajdonság a blob nevének megadására szolgál a `path` tulajdonságaiban:

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

A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-trigger, keresse meg a blob nevét a lekérdezési karakterláncból](#http-trigger-look-up-blob-name-from-query-string)
* [Üzenetsor-trigger, a blob nevének fogadása az üzenetsor-üzenetből](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-trigger, keresse meg a blob nevét a lekérdezési karakterláncból

 Az alábbi példa egy Java-függvényt mutat be, amely a `HttpTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó paraméter fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és átadja a tartalmát a függvénynek `byte[]`ként.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Üzenetsor-trigger, a blob nevének fogadása az üzenetsor-üzenetből

 Az alábbi példa egy Java-függvényt mutat be, amely a `QueueTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó üzenet fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és átadja a tartalmát a függvénynek `byte[]`ként.

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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@BlobInput` megjegyzéseket azon paramétereknél, amelyek értéke egy blobból származik.  Ezt a jegyzetet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel lehet használni `Optional<T>`használatával.

---

## <a name="input---attributes"></a>Input-attributes

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktora a blob elérési útját, valamint az olvasási vagy írási `FileAccess` paramétert az alábbi példában látható módon mutatja be:

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

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

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

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot osztály, metódus vagy paraméter szintjén. További információ: [trigger-attributes](#trigger---attributes).

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A `@BlobInput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha egy bájtos tömböt használ az attribútummal, állítsa a `dataType` `binary`re. A részletekért tekintse meg a [bemeneti példát](#input---example) .

---

## <a name="input---configuration"></a>Bemenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `Blob` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | `blob`értékre kell állítani. |
|**direction** | – | `in`értékre kell állítani. A kivételek a [használat](#input---usage) szakaszban vannak feltüntetve. |
|**név** | – | A blobot jelölő változó neve a függvény kódjában.|
|**elérési útja** |**BlobPath** | A blob elérési útja. |
|**kapcsolat** |**Kapcsolat**| Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó [tárolási kapcsolati karakterláncot](../storage/common/storage-configure-connection-string.md) tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [csak blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|– | **Access (Hozzáférés)** | Azt jelzi, hogy olvasás vagy írás történik-e. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet – használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A Blobok adatai a `context.bindings.<NAME>` használatával érhetők el, ahol a `<NAME>` megegyezik a *function. JSON*fájlban megadott értékkel.

# <a name="pythontabpython"></a>[Python](#tab/python)

A blob-adatelérést a [InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)típussal megadott paraméterrel érheti el. A részletekért tekintse meg a [bemeneti példát](#input---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

A `@BlobInput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha egy bájtos tömböt használ az attribútummal, állítsa a `dataType` `binary`re. A részletekért tekintse meg a [bemeneti példát](#input---example) .

---

## <a name="output"></a>Kimenet

Blobok írásához használja a blob Storage kimeneti kötéseit.

## <a name="output---example"></a>Kimenet – példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A következő példa egy [ C# olyan függvény](functions-dotnet-class-library.md) , amely blob-triggert és két kimeneti blob-kötést használ. A függvényt egy képblob létrehozásával indítják el a *minta-rendszerkép* tárolóban. Létrehozza a képblob kis és közepes méretű másolatait.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájl és [ C# parancsfájl (. CSX)](functions-reference-csharp.md) kódban, amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metadata tulajdonság a blob nevének megadására szolgál a `path` tulajdonságaiban:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metadata tulajdonság a blob nevének megadására szolgál a `path` tulajdonságaiban:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

<!--Same example for input and output. -->

A következő példa egy *function. JSON* fájlban és a kötéseket használó [Python-kódban](functions-reference-python.md) mutatja be a blob bemeneti és kimeneti kötéseit. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metadata tulajdonság a blob nevének megadására szolgál a `path` tulajdonságaiban:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-trigger a OutputBinding használatával](#http-trigger-using-outputbinding-java)
* [Üzenetsor-trigger, függvény visszatérési értékének használata](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger a OutputBinding (Java) használatával

 Az alábbi példa egy Java-függvényt mutat be, amely a `HttpTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó paraméter fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és átadja a tartalmát a függvénynek `byte[]`ként. A `BlobOutput` jegyzet a `OutputBinding outputItem`hoz kötődik, amelyet a függvény használ a bemeneti blob tartalmának a konfigurált tárolóba való írásához.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Üzenetsor-trigger, függvény visszatérési értéke (Java) használatával

 Az alábbi példa egy Java-függvényt mutat be, amely a `QueueTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó üzenet fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és átadja a tartalmát a függvénynek `byte[]`ként. A `BlobOutput` jegyzet a függvény visszatérési értékéhez kötődik, amelyet a futtatókörnyezet használ a bemeneti blob tartalmának a konfigurált tárolóba való írásához.

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@BlobOutput` megjegyzése a függvények paramétereit, amelyek értékét egy blob Storage-objektumba kívánja írni.  A paraméter típusának `OutputBinding<T>`nak kell lennie, ahol a T bármely natív Java-típus vagy POJO.

---

## <a name="output---attributes"></a>Kimenet – attribútumok

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktora a blob elérési útját, valamint az olvasási vagy írási `FileAccess` paramétert az alábbi példában látható módon mutatja be:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

A `Connection` tulajdonság beállításával megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A `@BlobOutput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha egy bájtos tömböt használ az attribútummal, állítsa a `dataType` `binary`re. A részletekért tekintse meg a [kimeneti példát](#output---example) .

---

Teljes példa: [kimeneti példa](#output---example).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot osztály, metódus vagy paraméter szintjén. További információ: [trigger-attributes](#trigger---attributes).

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `Blob` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | `blob`értékre kell állítani. |
|**direction** | – | Kimeneti kötés esetén `out` értékre kell állítani. A kivételek a [használat](#output---usage) szakaszban vannak feltüntetve. |
|**név** | – | A blobot jelölő változó neve a függvény kódjában.  A függvény visszatérési értékének hivatkozásához állítsa a `$return` értéket.|
|**elérési útja** |**BlobPath** | A blob-tároló elérési útja. |
|**kapcsolat** |**Kapcsolat**| Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a `connection` "MyStorage" értékre állítja, a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha üresen hagyja a `connection`, a functions futtatókörnyezet az alapértelmezett Storage-kapcsolatok karakterláncot használja az `AzureWebJobsStorage`nevű alkalmazás-beállításban.<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [csak blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|– | **Access (Hozzáférés)** | Azt jelzi, hogy olvasás vagy írás történik-e. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScriptben a `context.bindings.<name from function.json>`használatával férhet hozzá a blob-adataihoz.

# <a name="pythontabpython"></a>[Python](#tab/python)

A függvények paramétereinek deklarálása a blob Storage-ba való kiíráshoz a következő típusok szerint végezhető el:

* Karakterláncok `func.Out(str)`ként
* Streamek `func.Out(func.InputStream)`ként

A részletekért tekintse meg a [kimeneti példát](#output---example) .

# <a name="javatabjava"></a>[Java](#tab/java)

A `@BlobOutput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha egy bájtos tömböt használ az attribútummal, állítsa a `dataType` `binary`re. A részletekért tekintse meg a [kimeneti példát](#output---example) .

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés |  Leírások |
|---|---|
| Blob | [BLOB-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tábla, üzenetsor |  [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor |  [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
