---
title: "Az Azure Functions Blob tároló kötések"
description: "Azure Blob storage eseményindítók és kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: e0c608fe3a80c9d704774e592a1eba383bbdffe8
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Az Azure Functions Blob tároló kötések

Ez a cikk ismerteti az Azure Functions kötések Azure Blob storage használata. Az Azure Functions támogatja indítás, bemeneti és kimeneti BLOB kötései.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Csak a BLOB storage-fiókok](../storage/common/storage-create-storage-account.md#blob-storage-accounts) nem támogatottak. A BLOB storage eseményindítók és kötések általános célú tárfiók szükséges. 

## <a name="blob-storage-trigger"></a>A BLOB storage eseményindító

A Blob storage eseményindító segítségével indítsa el a következő függvényt egy új vagy frissített blob észlelésekor. A blob tartalmát vannak megadva, a függvény bemenete.

> [!NOTE]
> Egy blob eseményindító használatakor a fogyasztás terven létezhet legfeljebb 10 perces késleltetést új blobok feldolgozása után egy függvény app inaktív állapotba került. A függvény alkalmazás futtatása után blobok feldolgozása azonnal megtörténik. A kezdeti késleltetés elkerülése érdekében fontolja meg az alábbi lehetőségek közül:
> - Használja az App Service-csomag a mindig engedélyezve van.
> - Egy másik mechanizmus használatával indul el, a blob feldolgozására, például egy üzenetsor-üzenetet, amely tartalmazza a blob neve. Egy vonatkozó példáért lásd: a [blob bemeneti/kimeneti kötések példa a cikk későbbi részében](#input--output---example).

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [Lefordított C#](#trigger---c-example)
* [C# parancsfájl](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Eseményindító - C# – példa

Az alábbi példában látható [előre le fordítva C#](functions-dotnet-class-library.md) kódot, amely a napló ír, amikor egy blob hozzáadott vagy frissített a `samples-workitems` tároló.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

További információ a `BlobTrigger` attribútum című [eseményindító - attribútumok az előfordított C#](#trigger---attributes-for-precompiled-c).

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájl és [C# parancsfájl](functions-reference-csharp.md) kódot, amely a kötés használja. A függvény egy napló ír, amikor egy blob hozzáadott vagy frissített a `samples-workitems` tároló.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# parancsfájlkód, amelyhez van kötve van egy `Stream`:

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Itt a C# parancsfájlkód, amelyhez van kötve van egy `CloudBlockBlob`:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájl- és [JavaScript-kód] (funkciók-referencia-node.md), amely a kötés használja. A függvény egy napló ír, amikor egy blob hozzáadott vagy frissített a `samples-workitems` tároló.

Itt a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A [konfigurációs](#trigger---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>Eseményindító - attribútumok az előfordított C#

A [előre le fordítva C#](functions-dotnet-class-library.md) funkciók, ha egy blob eseményindítót a következő attribútumokat használhatja:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)NuGet-csomagot a definiált [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Az attribútum konstruktora tart egy elérési út karakterláncát, amely jelzi a tárolót, hogy figyelje, illetve opcionálisan egy [blob mintát](#trigger---blob-name-patterns). Íme egy példa:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

  Beállíthatja a `Connection` tulajdonság adja meg a tárfiókot, a következő példában látható módon:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)NuGet-csomagot a definiált [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs)

  Adja meg a tárfiókot egy másik lehetőséget nyújt. A konstruktornak, amely tartalmazza a tárolási kapcsolati karakterlánc alkalmazásbeállítás neve vesz igénybe. Az attribútum a paraméter, módszer vagy osztály szintjén is alkalmazható. A következő példa bemutatja az osztály és módszer:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

A használt tárfiók határozza meg a következő sorrendben:

* A `BlobTrigger` attribútum `Connection` tulajdonság.
* A `StorageAccount` attribútuma ugyanezt a paramétert, mint a `BlobTrigger` attribútum.
* A `StorageAccount` függvény attribútuma.
* A `StorageAccount` osztály attribútuma.
* Az alapértelmezett tárfiók a függvény ("AzureWebJobsStorage" alkalmazásbeállítás) alkalmazás.

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `BlobTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | meg kell `blobTrigger`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya** | n/a | meg kell `in`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. A kivételeket jeleztük a [használati](#trigger---usage) szakasz. |
|**név** | n/a | A blob függvény kódban jelölő neve. | 
|**elérési út** | **BlobPath** |A tároló figyelésére.  Lehetséges, hogy egy [blob mintát](#trigger-blob-name-patterns). | 
|**kapcsolat** | **Kapcsolat** | A tárolási kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a nevét itt csak a maradékot. Ha például `connection` "MyStorage", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyStorage." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc az nevű Alkalmazásbeállítás `AzureWebJobsStorage`.<br><br>A kapcsolati karakterlánc nem lehet egy általános célú tárfiók olyan [csak a blob storage-fiók](../storage/common/storage-create-storage-account.md#blob-storage-accounts).<br>Helyileg kidolgozása, Alkalmazásbeállítások kísérhet értékeit a [local.settings.json fájl](functions-run-local.md#local-settings-file).|

## <a name="trigger---usage"></a>Eseményindító - használat

C# és C# a parancsfájlt, nyissa meg a blobadatokat metódusparaméter használatával `Stream paramName`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. Köthető a következő típusok:

* `TextReader`
* `Stream`
* `ICloudBlob`("inout" kötés irányban szükséges *function.json*)
* `CloudBlockBlob`("inout" kötés irányban szükséges *function.json*)
* `CloudPageBlob`("inout" kötés irányban szükséges *function.json*)
* `CloudAppendBlob`("inout" kötés irányban szükséges *function.json*)

Amint, néhány, a következő típusú szükséges egy `inout` irányban kötés *function.json*. Ebben az irányban nem támogatja a szokásos szerkesztő az Azure portálon, így a speciális szerkesztő kell használni.

Szöveges BLOB várható, ha kell kötni a `string` típusa. Ez csak akkor javasolt, ha a blob mérete kisebb, mint a teljes blob tartalmát a memóriába betöltött. Általában célszerű használni egy `Stream` vagy `CloudBlockBlob` típusa.

JavaScript, nyissa meg a bemeneti blob adatok `context.bindings.<name>`.

## <a name="trigger---blob-name-patterns"></a>Eseményindító - blob neve minták

A blob neve mintát is megadhat a `path` tulajdonság *function.json* vagy a `BlobTrigger` attribútum konstruktora. A minta lehet egy [szűrőt, vagy kötési kifejezés](functions-triggers-bindings.md#binding-expressions-and-patterns).

### <a name="filter-on-blob-name"></a>A blob neve szűrése

A következő példa eseményindítók csak a blobot, amely a `input` a string "eredeti" kezdetű tároló:

```json
"path": "input/original-{name}",
```
 
Ha a blob neve *eredeti-Blob1.txt*, értékét a `name` függvény a kódban a változó `Blob1`.

### <a name="filter-on-file-type"></a>A fájl típusa szűrése

Az alábbi példa elindítja a kizárólag a *.png* fájlok:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>A fájlnevekben kapcsos zárójelek szűrése

A fájlnevekben kapcsos zárójelek kereséséhez escape a zárójelek két zárójelek használatával. A következő példa szűrőket a blobok neve kapcsos zárójelek rendelkező:

```json
"path": "images/{{20140101}}-{name}",
```

Ha a blob neve *{20140101}-soundfile.mp3*, a `name` változó a funkciókódot érték *soundfile.mp3*. 

### <a name="get-file-name-and-extension"></a>Get-fájl nevét és kiterjesztését

A következő példa bemutatja, hogyan lehet kötést létrehozni a blob-fájl neve és a bővítmény külön-külön:

```json
"path": "input/{blobname}.{blobextension}",
```
Ha a blob neve *eredeti-Blob1.txt*, értékét a `blobname` és `blobextension` változók a funkciókódot *eredeti-Blob1* és *txt*.

## <a name="trigger---metadata"></a>Eseményindító - metaadatok

A blob eseményindító biztosít több metaadat-tulajdonságot. Ezeket a tulajdonságokat meg más kötésekben kötési kifejezés részeként vagy a kód paramétereiben használható. Ezekkel az értékekkel rendelkeznek az azonos szemantikákkal, a [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) típusa.


|Tulajdonság  |Típus  |Leírás  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Az eseményindító blob elérési útja.|
|`Uri`|`System.Uri`|A blob URI-azonosítóját az elsődleges helyre.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|A blob rendszer tulajdonságai. |
|`Metadata` |`IDictionary<string,string>`|A felhasználó által definiált metaadatok a BLOB.|

## <a name="trigger---blob-receipts"></a>Eseményindító - blob visszaigazolások

Az Azure Functions futtatókörnyezettel biztosítja, hogy nincs blob funkció egynél többször meghívása megtörténik a azonos új vagy frissített BLOB. Annak meghatározásához, ha egy adott blobverzió feldolgozása tart fenn *blob-visszaigazolások*.

Az Azure Functions tárolók blob-visszaigazolások nevű tároló *azure-webjobs-állomások* függvény alkalmazás az Azure storage-fiók (határozzák meg az Alkalmazásbeállítás `AzureWebJobsStorage`). Egy blob fogadását rendelkezik a következő információkat:

* A kiváltott függvény ("*&lt;függvény alkalmazás neve >*. Működik.  *&lt;függvény neve >*", például:"MyFunctionApp.Functions.CopyBlob")
* A tároló neve
* A blob típusú ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag (például egy blob verziójának azonosítója: "0x8D1DC6E70A277EF")

Egy blob újrafeldolgozása kényszerítéséhez blob fogadását, hogy a BLOB törlése a *azure-webjobs-állomások* tároló manuálisan.

## <a name="trigger---poison-blobs"></a>Eseményindító - poison blobok

Ha egy adott BLOB egy blob funkció nem sikerül, az Azure Functions újrapróbálkozik, amelyek összesen 5 alkalommal alapértelmezés szerint. 

Minden 5 próbálkozás sikertelen lesz, ha az Azure Functions ad hozzá egy üzenet nevű tároló várólista *webjobs-blobtrigger-poison*. Az elhalt blobok várólista üzenet egy JSON-objektum, amely tartalmazza a következő tulajdonságokkal:

* FunctionId (formátumú  *&lt;függvény alkalmazás neve >*. Működik.  *&lt;függvény neve >*)
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* Blobnév
* ETag (például egy blob verziójának azonosítója: "0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>Eseményindító - konténerek lekérdezése

Ha a figyelt blob tároló több mint 10 000 blobot tartalmaz, a funkciók futásidejű vizsgálatok a naplófájlok új vagy módosított blobok figyelendő. Ez a folyamat késést okozhat. Egy függvény előfordulhat, hogy nem get indulnak el, néhány percig, vagy már a blob létrehozása után. Emellett [tárolási naplófájlokat hoz létre a lehető legjobb rendezését,"a](/rest/api/storageservices/About-Storage-Analytics-Logging) alapján. Nincs nem garantálja, hogy a rendszer rögzíti-e az összes esemény. Bizonyos körülmények között a naplók kimaradhatnak. Ha a gyorsabb és megbízhatóbb blob feldolgozási van szüksége, érdemes létrehozni egy [üzenetsor](../storage/queues/storage-dotnet-how-to-use-queues.md) a blob létrehozásakor. Ezután egy [várólista eseményindító](functions-bindings-storage-queue.md) helyett egy blob eseményindító a blob feldolgozni. Egy másik lehetőség, hogy használja az esemény rács; Tekintse meg az [automatizálás átméretezése feltöltött lemezképeket rácshoz esemény](../event-grid/resize-images-on-storage-blob-upload-event.md).

## <a name="blob-storage-input--output-bindings"></a>A BLOB storage bemeneti és kimeneti kötések

Használja a Blob storage bemeneti, és kimeneti kötések írási és olvasási blobokat.

## <a name="input--output---example"></a>Bemeneti és kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [Lefordított C#](#input--output---c-example)
* [C# parancsfájl](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>Bemeneti és kimeneti - C# – példa

A következő példa egy [előre le fordítva C#](functions-dotnet-class-library.md) függvény, amely egy bemeneti és kimeneti blob két kötés használja. A függvény egy kép blob létrehozását váltja ki a *minta-lemezképek* tároló. Szülőlemezkép kis és közepes méretű másolatot hoz létre. 

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

### <a name="input--output---c-script-example"></a>Bemeneti és kimeneti - C# mintaparancsfájl

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájl és [C# parancsfájl](functions-reference-csharp.md) kódot, amely a kötés használja. A funkció lehetővé teszi a blob egy példányát. A függvény egy üzenetsor-üzenetet, amely tartalmazza a nevét, a BLOB másolása váltja ki. Az új blob neve *{originalblobname}-másolási*.

Az a *function.json* fájl, a `queueTrigger` metaadat-tulajdonságnak a blob nevének megadására szolgál a `path` tulajdonságok:

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

A [konfigurációs](#input--output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```cs
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>Bemeneti és kimeneti - JavaScript – példa

A következő példa bemutatja egy blob eseményindító kötelező egy *function.json* fájl- és [JavaScript-kód] (funkciók-referencia-node.md), amely a kötés használja. A funkció lehetővé teszi a blob egy példányát. A függvény egy üzenetsor-üzenetet, amely tartalmazza a nevét, a BLOB másolása váltja ki. Az új blob neve *{originalblobname}-másolási*.

Az a *function.json* fájl, a `queueTrigger` metaadat-tulajdonságnak a blob nevének megadására szolgál a `path` tulajdonságok:

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

A [konfigurációs](#input--output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes-for-precompiled-c"></a>Bemeneti és kimeneti - attribútumok az előfordított C#

A [előre le fordítva C#](functions-dotnet-class-library.md) funkciók használata a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), amely van megadva a NuGet-csomag [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs).

Az attribútum konstruktora a blob vesz igénybe az elérési utat és egy `FileAccess` paraméter, amely jelzi, olvasási vagy írási, a következő példában látható módon:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
```

Beállíthatja a `Connection` tulajdonság adja meg a tárfiókot, a következő példában látható módon:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
```

Használhatja a `StorageAccount` attribútum segítségével adhatja meg a tárfiók osztály, módszer vagy paraméter szinten. További információkért lásd: [eseményindító - attribútumok az előfordított C#](#trigger---attributes-for-precompiled-c).

## <a name="input--output---configuration"></a>Bemeneti és kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `Blob` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n/a | meg kell `blob`. |
|**iránya** | n/a | Meg kell `in` egy bemeneti kötés vagy egy kimeneti kötés. A kivételeket jeleztük a [használati](#input--output---usage) szakasz. |
|**név** | n/a | A blob függvény kódban jelölő neve.  Beállítása `$return` hivatkozni, a függvény visszatérési értéke.|
|**elérési út** |**BlobPath** | A blob elérési útja. | 
|**kapcsolat** |**Kapcsolat**| A tárolási kapcsolati karakterlánc az ehhez a kötéshez használandó tartalmazó alkalmazásbeállítás neve. Ha az alkalmazás neve "AzureWebJobs" kezdődik, megadhatja a nevét itt csak a maradékot. Ha például `connection` "MyStorage", hogy a Functions futtatókörnyezete keresi, hogy az alkalmazás neve "AzureWebJobsMyStorage." Ha nem adja meg `connection` üres, a Functions futtatókörnyezete használja az alapértelmezett tárolási kapcsolati karakterlánc az nevű Alkalmazásbeállítás `AzureWebJobsStorage`.<br><br>A kapcsolati karakterlánc nem lehet egy általános célú tárfiók olyan [csak a blob storage-fiók](../storage/common/storage-create-storage-account.md#blob-storage-accounts).<br>Helyileg kidolgozása, Alkalmazásbeállítások kísérhet értékeit a [local.settings.json fájl](functions-run-local.md#local-settings-file).|
|n/a | **Access (Hozzáférés)** | Azt jelzi, hogy meg kell olvasása vagy írása. |

## <a name="input--output---usage"></a>Bemeneti és kimeneti - használat

Lefordított C# és C# a parancsfájlt, nyissa meg a blob metódusparaméter használatával `Stream paramName`. A C# parancsfájl `paramName` érték szerepel a `name` tulajdonsága *function.json*. Köthető a következő típusok:

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob`("inout" kötés irányban szükséges *function.json*)
* `CloudBlockBlob`("inout" kötés irányban szükséges *function.json*)
* `CloudPageBlob`("inout" kötés irányban szükséges *function.json*)
* `CloudAppendBlob`("inout" kötés irányban szükséges *function.json*)

Amint, néhány, a következő típusú szükséges egy `inout` irányban kötés *function.json*. Ebben az irányban nem támogatja a szokásos szerkesztő az Azure portálon, így a speciális szerkesztő kell használni.

Ha szöveges BLOB olvas, köthető egy `string` típusa. Ez a típus csak akkor javasolt, ha blob mérete kisebb, mint a teljes blob tartalmát a memóriába betöltött. Általában célszerű használni egy `Stream` vagy `CloudBlockBlob` típusa.

JavaScript, nyissa meg a blob adatainak használatával `context.bindings.<name>`.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ugrás a Blob storage eseményindító használó gyors üzembe helyezés](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
