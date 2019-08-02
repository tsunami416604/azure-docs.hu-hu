---
title: Azure Blob Storage-kötések Azure Functionshoz
description: Ismerje meg, hogyan használható az Azure Blob Storage-eseményindítók és-kötések Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/15/2018
ms.author: cshoe
ms.openlocfilehash: fc7cb7f82fce4f7da02f39b0b423841ac270dcbd
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564817"
---
# <a name="azure-blob-storage-bindings-for-azure-functions"></a>Azure Blob Storage-kötések Azure Functionshoz

Ez a cikk azt ismerteti, hogyan használható az Azure Blob Storage-kötések Azure Functions. Azure Functions támogatja a Blobok trigger-, bemeneti és kimeneti kötéseit. A cikk minden kötéshez tartalmaz egy szakaszt:

* [Blob trigger](#trigger)
* [BLOB bemeneti kötése](#input)
* [BLOB kimeneti kötése](#output)

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> A blob Storage-trigger helyett használja a Event Grid triggert a blob Storage-fiókokhoz, nagy léptékű vagy a késés csökkentése érdekében. További információ: [trigger](#trigger) szakasz.

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A blob Storage-kötések a [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet csomagban, 2. x verzióban érhetők el. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Blob) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A blob Storage-kötések a [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomagban, 3. x verzióban érhetők el. A csomag forráskódja a [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs) GitHub-adattárban.

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

### <a name="latency-issues"></a>Késéssel kapcsolatos problémák

Ha a Function alkalmazás a használati terven van, akkor akár 10 percet is igénybe vehet az új Blobok feldolgozásakor, ha egy Function alkalmazás üresjáratban van. Ha el szeretné kerülni ezt a késést, átválthat egy App Service-csomagra az Always On enabled lehetőséggel. Egy [Event Grid triggert](functions-bindings-event-grid.md) is használhat a blob Storage-fiókkal. Példaként tekintse meg a [Event Grid oktatóanyagot](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json). 

### <a name="queue-storage-trigger"></a>Üzenetsor-tárolási trigger

Event Grid mellett a Blobok feldolgozásának egy másik alternatívája a várólista-tároló triggere, de nem rendelkezik beépített támogatással a blob eseményeihez. A Blobok létrehozásakor vagy frissítésekor üzenetsor-üzeneteket kell létrehoznia. A jelen [cikk későbbi, a blob bemeneti kötési példája](#input---example)című részében talál egy példát, amely azt feltételezi, hogy ezt elvégezte.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Eseményindító - C#-példa

Az alábbi példa egy olyan [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy naplót ír be, amikor a `samples-workitems` tárolóban blobot adnak hozzá vagy frissítenek.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A blob `{name}` trigger elérési útjának `samples-workitems/{name}` karakterlánca egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ az `BlobTrigger` attribútumról: [trigger-attributes](#trigger---attributes).

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és a kötést használó [Python-kódban](functions-reference-python.md) . A függvény naplót ír, amikor egy blobot vesznek fel vagy `samples-workitems` frissülnek a [tárolóban](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A blob `{name}` trigger elérési útjának `samples-workitems/{name}` karakterlánca egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a *function. JSON* fájl tulajdonságairól: [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Az alábbi C# parancsfájl- `Stream`kód a következőhöz kötődik:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Az alábbi C# parancsfájl- `CloudBlockBlob`kód a következőhöz kötődik:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

A következő példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötést használja. A függvény naplót ír, amikor egy blobot vesznek fel vagy `samples-workitems` frissülnek a tárolóban.

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

A blob `{name}` trigger elérési útjának `samples-workitems/{name}` karakterlánca egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a *function. JSON* fájl tulajdonságairól: [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

### <a name="trigger---python-example"></a>Trigger – Python-példa

Az alábbi példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és a kötést használó [Python-kódban](functions-reference-python.md) . A függvény naplót ír, amikor egy blobot vesznek fel vagy `samples-workitems` frissülnek a [tárolóban](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

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

A blob `{name}` trigger elérési útjának `samples-workitems/{name}` karakterlánca egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#trigger---blob-name-patterns) a cikk későbbi részében.

További információ a *function. JSON* fájl tulajdonságairól: [konfigurációs](#trigger---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa egy blob trigger kötést mutat be egy *function. JSON* fájlban és a kötést használó [Java-kódban](functions-reference-java.md) . A függvény naplót ír, amikor egy blobot vesznek fel vagy `myblob` frissülnek a tárolóban.

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

  Egy teljes példa: [eseményindító – C#-példa](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútum a paramétert, a metódus vagy az osztály szintjén alkalmazhatók. Az alábbi példa bemutatja az osztály és metódust:

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

* A `BlobTrigger` attribútum `Connection` tulajdonság.
* A `StorageAccount` a paramétert, amelyek azonos, alkalmazott attribútum a `BlobTrigger` attribútum.
* A `StorageAccount` attribútum a függvény a alkalmazni.
* A `StorageAccount` attribútum az osztály a alkalmazni.
* A Function alkalmazás alapértelmezett Storage-fiókja ("AzureWebJobsStorage").

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `BlobTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `blobTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. A kivételek a [használat](#trigger---usage) szakaszban vannak feltüntetve. |
|**name** | n/a | A blobot jelölő változó neve a függvény kódjában. |
|**path** | **BlobPath** |A figyelni kívánt [tároló](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) .  A [blob neve minta](#trigger---blob-name-patterns)lehet. |
|**kapcsolat** | **kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [blob Storage](../storage/common/storage-account-overview.md#types-of-storage-accounts)-fiókhoz.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Eseményindító - használat

A C# és C# a szkriptben a következő paramétereket használhatja a kiváltó blobhoz:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* A POCO szerializálható JSON-ként
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

az <sup>1</sup> érték "be" `direction` kötést igényel a *function. JSON* fájlban vagy `FileAccess.ReadWrite` egy C# Class könyvtárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

`string` A(,)vagyaPocotípusúkötéscsakakkorajánlott,haablobméretekicsi,mertablobteljestartalmabetöltődik`Byte[]`a memóriába. Általában a vagy `Stream` `CloudBlockBlob` a típus használata javasolt. További információ: [párhuzamosság és memóriahasználat](#trigger---concurrency-and-memory-usage) a cikk későbbi részében.

A JavaScriptben a bemeneti blob adatai a használatával `context.bindings.<name from function.json>`érhetők el.

## <a name="trigger---blob-name-patterns"></a>Trigger – blob neve mintázat

A blob neve mintát megadhatja a `path` *function. JSON* fájlban vagy `BlobTrigger` az Attribute konstruktorban. A név minta lehet [szűrő vagy kötési kifejezés](./functions-bindings-expressions-patterns.md)is. A következő szakasz példákat tartalmaz.

### <a name="get-file-name-and-extension"></a>Fájl nevének és kiterjesztésének beolvasása

Az alábbi példa bemutatja, hogyan köthető a blob-fájl nevéhez és kiterjesztéséhez:

```json
"path": "input/{blobname}.{blobextension}",
```
Ha a blob neve *Original-Blob1. txt*, a (z `blobname` ) `blobextension` és a (z) függvény kódjában található változók értékei az *eredeti-Blob1* és a *txt*.

### <a name="filter-on-blob-name"></a>Szűrés a blob nevén

Az alábbi példa csak a `input` tárolóban lévő blobokat indítja el, amelyek az "Original-" karakterlánccal kezdődnek:

```json
"path": "input/original-{name}",
```

Ha a blob neve *Original-Blob1. txt*, a függvény kódjában található `name` `Blob1`változó értéke.

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

Ha a blob neve  *{20140101}-soundfile. mp3*, a `name` függvény kódjában található változó értéke *hangállomány. mp3*.

## <a name="trigger---metadata"></a>Trigger – metaadatok

A blob trigger számos metaadat-tulajdonságot biztosít. Ezek a tulajdonságok a kötési kifejezésekben való használata más kötések részeként vagy a kód paramétereiben használható. Ezeknek az értékeknek ugyanazok a szemantikai értékei, mint a [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet) típusa.

|Tulajdonság  |Típus  |Leírás  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az indító blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-ja az elsődleges helyen.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobproperties)|A blob rendszertulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A blobhoz tartozó felhasználó által definiált metaadatok.|

Az alábbi C# szkriptek és JavaScript-példák például az indító blob elérési útját naplózzák, beleértve a tárolót:

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

## <a name="trigger---blob-receipts"></a>Trigger – blob-visszaigazolások

A Azure Functions futtatókörnyezet biztosítja, hogy a blob trigger függvény többször is meghívja ugyanazt az új vagy frissített blobot. Annak megállapításához, hogy egy adott blob-verzió feldolgozása megtörtént-e, a *blob*-visszaigazolásokat tart fenn.

Azure Functions a blob-visszaigazolásokat egy *Azure-webjobs* nevű tárolóban tárolja – az Azure Storage-fiókban lévő gazdagépeket a Function alkalmazáshoz `AzureWebJobsStorage`(amelyet az alkalmazás-beállítás határoz meg). A blob-visszaigazolás a következő információkat tartalmazhatja:

* Az aktivált függvény (" *&lt;Function alkalmazás neve >* . Funkciók. a függvény neve > ", például:  *&lt;* "MyFunctionApp.Functions.CopyBlob")
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* A ETag (a blob verziószáma, például: "0x8D1DC6E70A277EF")

Egy blob újrafeldolgozásának kényszerítéséhez törölje az adott blobhoz tartozó blob-elismervényt az *Azure-webjobs-hosts* tárolóból manuálisan. Előfordulhat, hogy az újrafeldolgozás nem azonnal, hanem egy későbbi időpontban történik.

## <a name="trigger---poison-blobs"></a>Trigger – mérges Blobok

Ha egy blob trigger-függvény meghibásodik egy adott blobnál, Azure Functions az újrapróbálkozások alapértelmezés szerint összesen 5 alkalommal működnek.

Ha mind az 5 próbálkozás sikertelen, Azure Functions hozzáadja az üzenetet egy *webjobs-blobtrigger-méreg*nevű Storage-várólistához. A méreg-Blobok üzenetsor-üzenete egy JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (a Format  *&lt;függvény alkalmazásának neve >* . Funkciók. függvény neve >)  *&lt;*
* BlobType ("BlockBlob" vagy "PageBlob")
* Tárolónév
* BlobName
* ETag (blob-verzió azonosítója, például: "0x8D1DC6E70A277EF")

## <a name="trigger---concurrency-and-memory-usage"></a>Trigger – párhuzamosságok és memóriahasználat

A blob-trigger belsőleg használ egy várólistát, így az egyidejű függvények maximális száma a [Host. JSON fájlban található várólisták konfigurációjának megfelelően](functions-host-json.md#queues)van szabályozva. Az alapértelmezett beállítások a párhuzamosságot 24 hívásra korlátozzák. Ez a korlát külön vonatkozik a blob-triggert használó összes függvényre.

[A használati terv az](functions-scale.md#how-the-consumption-and-premium-plans-work) egyik virtuális GÉPEN (VM) lévő function alkalmazást 1,5 GB memóriára korlátozza. A memóriát a párhuzamosan futó és a functions Runtime is használja. Ha egy blob által aktivált függvény a teljes blobot betölti a memóriába, az adott függvény által a Blobok által használt maximális memória 24 * a blob maximális mérete. Például a három blob által aktivált függvényt használó Function alkalmazás, valamint az alapértelmezett beállítások esetében a 3 * 24 = 72 függvény meghívása esetén a virtuális gépek maximális száma.

A JavaScript és a Java függvények a teljes blobot a memóriába töltik be, a C# functions pedig a következőhöz kötődik `string` `Byte[]`:, vagy poco.

## <a name="trigger---polling"></a>Trigger – lekérdezés

Ha a figyelt blob-tároló több mint 10 000 blobot tartalmaz (az összes tárolóban), a functions futtatókörnyezet megvizsgálja a naplófájlokat, hogy figyelje az új vagy módosított blobokat. Ez a folyamat késéseket okozhat. Előfordulhat, hogy a rendszer nem indít el egy függvényt, amíg a blob létrehozása után több percig vagy tovább nem ér.

> [!WARNING]
> Emellett a [tárolási naplók a "legjobb megoldás" alapon jönnek létre](/rest/api/storageservices/About-Storage-Analytics-Logging) . Nem garantálható, hogy minden esemény rögzítve legyen. Bizonyos körülmények között előfordulhat, hogy a naplók kimaradnak.
> 
> Ha gyorsabb vagy megbízhatóbb blob-feldolgozásra van szüksége, érdemes lehet üzenetsor- [üzenetet](../storage/queues/storage-dotnet-how-to-use-queues.md) létrehozni a blob létrehozásakor. Ezután használjon egy [üzenetsor](functions-bindings-storage-queue.md) -triggert a blob-trigger helyett a blob feldolgozásához. Egy másik lehetőség a Event Grid használata; Tekintse meg az oktatóanyagot, amely automatizálja a [feltöltött képek átméretezését Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="input"></a>Input (Bemenet)

BLOB Storage-beli bemeneti kötés használata a Blobok olvasásához.

## <a name="input---example"></a>Bemenet – példa

Tekintse meg az adott nyelvű példa:

* [C#](#input---c-example)
* [C# script (.csx)](#input---c-script-example)
* [Java](#input---java-examples)
* [JavaScript](#input---javascript-example)
* [Python](#input---python-example)

### <a name="input---c-example"></a>Bemenet – C# példa

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

### <a name="input---c-script-example"></a>Bemeneti – C# parancsfájl – példa

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájl és [ C# parancsfájl (. CSX)](functions-reference-csharp.md) kódban, amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő `path` tulajdonságok alapján adható meg:

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

### <a name="input---javascript-example"></a>Bemenet – JavaScript-példa

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő `path` tulajdonságok alapján adható meg:

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

### <a name="input---python-example"></a>Bemenet – Python-példa

<!--Same example for input and output. -->

A következő példa egy *function. JSON* fájlban és a kötéseket használó [Python-kódban](functions-reference-python.md) mutatja be a blob bemeneti és kimeneti kötéseit. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő `path` tulajdonságok alapján adható meg:

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

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

### <a name="input---java-examples"></a>Bevitel – Java-példák

Ez a szakasz tartalmazza az alábbi példák:

* [HTTP-trigger, keresse meg a blob nevét a lekérdezési karakterláncból](#http-trigger-look-up-blob-name-from-query-string-java)
* [Üzenetsor-trigger, a blob nevének fogadása az üzenetsor-üzenetből](#queue-trigger-receive-blob-name-from-queue-message-java)

#### <a name="http-trigger-look-up-blob-name-from-query-string-java"></a>HTTP-trigger, keresse meg a blob nevét a lekérdezési karakterláncból (Java)

 A következő példa egy Java-függvényt mutat be ```HttpTrigger``` , amely a jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó paraméter fogadására. A ```BlobInput``` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek ```byte[]```.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message-java"></a>Üzenetsor-trigger, fogadó blob neve az üzenetsor-üzenetből (Java)

 A következő példa egy Java-függvényt mutat be ```QueueTrigger``` , amely a jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó üzenet fogadására. A ```BlobInput``` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek ```byte[]```.

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

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@BlobInput` a jegyzeteket azon paramétereknél, amelyek értéke egy blobból származik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>`.

## <a name="input---attributes"></a>Bemenet - attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktora a blob elérési útját és `FileAccess` az olvasási vagy írási paramétereket jelölő paramétert a következő példában látható módon veszi át:

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

`StorageAccount` Az attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információkért lásd: [eseményindító - attribútumok](#trigger---attributes).

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Blob` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `blob`. |
|**direction** | n/a | Meg kell `in`. A kivételek a [használat](#input---usage) szakaszban vannak feltüntetve. |
|**name** | n/a | A blobot jelölő változó neve a függvény kódjában.|
|**path** |**BlobPath** | A blob elérési útja. |
|**kapcsolat** |**kapcsolat**| Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó [tárolási kapcsolati karakterláncot](../storage/common/storage-configure-connection-string.md) tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [csak blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **Access (Hozzáférés)** | Azt jelzi, hogy olvasás vagy írás történik-e. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Bemenet - használat

A C# és C# a szkriptben a következő típusparaméter-típusokat használhatja a blob bemeneti kötéséhez:

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

az <sup>1</sup> érték "be" `direction` kötést igényel a *function. JSON* fájlban vagy `FileAccess.ReadWrite` egy C# Class könyvtárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

A vagy `string` `Byte[]` a alkalmazáshoz való kötés csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy `Stream` `CloudBlockBlob` a típus használata javasolt. További információ: a jelen cikk [párhuzamosságok és memóriahasználat](#trigger---concurrency-and-memory-usage) című szakasza.

A JavaScriptben a használatával `context.bindings.<name from function.json>`férhet hozzá a blob-adataihoz.

## <a name="output"></a>Output

Blobok írásához használja a blob Storage kimeneti kötéseit.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [Java](#output---java-examples)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa egy [ C# olyan függvény](functions-dotnet-class-library.md) , amely blob-triggert és két kimeneti blob-kötést használ. A függvényt egy képblob létrehozásával indítják el a *minta-rendszerkép* tárolóban. Létrehozza a képblob kis és közepes méretű másolatait.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    IImageFormat format;

    using (Image<Rgba32> input = Image.Load(image, out format))
    {
      ResizeImage(input, imageSmall, ImageSize.Small, format);
    }

    image.Position = 0;
    using (Image<Rgba32> input = Image.Load(image, out format))
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
```

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájl és [ C# parancsfájl (. CSX)](functions-reference-csharp.md) kódban, amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő `path` tulajdonságok alapján adható meg:

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

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő `path` tulajdonságok alapján adható meg:

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

### <a name="output---python-example"></a>Kimenet – Python-példa

<!--Same example for input and output. -->

A következő példa egy *function. JSON* fájlban és a kötéseket használó [Python-kódban](functions-reference-python.md) mutatja be a blob bemeneti és kimeneti kötéseit. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő `path` tulajdonságok alapján adható meg:

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

Itt látható a Python-kód:

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

* [HTTP-trigger a OutputBinding használatával](#http-trigger-using-outputbinding-java)
* [Üzenetsor-trigger, függvény visszatérési értékének használata](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger a OutputBinding (Java) használatával

 A következő példa egy Java-függvényt mutat be ```HttpTrigger``` , amely a jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó paraméter fogadására. A ```BlobInput``` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek ```byte[]```. A ```BlobOutput``` jegyzet a következőhöz kötődik ```OutputBinding outputItem```:, amelyet a függvény használ a bemeneti blob tartalmának a konfigurált tárolóba való írásához.

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

 A következő példa egy Java-függvényt mutat be ```QueueTrigger``` , amely a jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó üzenet fogadására. A ```BlobInput``` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek ```byte[]```. A ```BlobOutput``` jegyzet a függvény visszatérési értékéhez kötődik, amelyet ezután a futtatókörnyezet használ a bemeneti blob tartalmának a konfigurált tárolóba való írásához.

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

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `@BlobOutput` a megjegyzések a függvények azon paramétereit, amelyek értékét a blob Storage-ban lévő objektumba kívánja írni.  A paraméternek a következőnek kell lennie `OutputBinding<T>`:, ahol a T bármely natív Java-típus vagy POJO.

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktora a blob elérési útját és `FileAccess` az olvasási vagy írási paramétereket jelölő paramétert a következő példában látható módon veszi át:

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

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

`StorageAccount` Az attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információkért lásd: [eseményindító - attribútumok](#trigger---attributes).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `Blob` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Meg kell `blob`. |
|**direction** | n/a | Kimeneti kötéshez be `out` kell állítani. A kivételek a [használat](#output---usage) szakaszban vannak feltüntetve. |
|**name** | n/a | A blobot jelölő változó neve a függvény kódjában.  `$return` Állítsa a értékre a függvény visszatérési értékének hivatkozásához.|
|**path** |**BlobPath** | A blob-tároló elérési útja. |
|**kapcsolat** |**kapcsolat**| Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" `connection` értékre van állítva, a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha üresen `connection` hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű `AzureWebJobsStorage`alkalmazás-beállításban.<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [csak blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n/a | **Access (Hozzáférés)** | Azt jelzi, hogy olvasás vagy írás történik-e. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Kimenet – használat

A C# és C# a szkriptben a következő típusokhoz köthető Blobok írásához:

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

<sup>1</sup> a "in" kötést kötelező `direction` megadni a *function. JSON* fájlban vagy `FileAccess.Read` egy C# Class könyvtárban. Azonban használhatja azt a Container objektumot is, amellyel a futtatókörnyezet írási műveleteket hajthat végre, például blobokat tölthet fel a tárolóba.

<sup>2</sup> a "be `direction` " kötést igényli a *function. JSON* fájlban vagy `FileAccess.ReadWrite` egy C# Class könyvtárban.

Ha egy Storage SDK-típushoz próbál kötni, és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e a [megfelelő Storage SDK](#azure-storage-sdk-version-in-functions-1x)-verzióra mutató hivatkozással.

Az aszinkron funkciók, a visszaadott értékének használata vagy `IAsyncCollector` helyett egy `out` paraméter.

A vagy `string` `Byte[]` a alkalmazáshoz való kötés csak akkor ajánlott, ha a blob mérete kicsi, mivel a blob teljes tartalma betöltődik a memóriába. Általában a vagy `Stream` `CloudBlockBlob` a típus használata javasolt. További információ: a jelen cikk [párhuzamosságok és memóriahasználat](#trigger---concurrency-and-memory-usage) című szakasza.


A JavaScriptben a használatával `context.bindings.<name from function.json>`férhet hozzá a blob-adataihoz.

## <a name="exceptions-and-return-codes"></a>Kivételek és a visszatérési kódok

| Kötés |  Hivatkozás |
|---|---|
| Blob | [BLOB-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Table, Queue |  [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Table, Queue |  [Hibaelhárítás](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

* [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Blob storage trigger](functions-create-storage-blob-triggered-function.md)
--->
