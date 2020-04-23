---
title: Az Azure Blob-tároló eseményindítója az Azure Functionshez
description: Ismerje meg, hogyan futtathat egy Azure-függvényt az Azure Blob tárolási adatainak változásakor.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 61fbaf37577efdab0b147d437ae78fc4df0764cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084957"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Az Azure Blob-tároló eseményindítója az Azure Functionshez

A Blob storage eseményindító elindul egy függvényt, amikor egy új vagy frissített blob észlelése. A blob tartalma a [függvény bemeneteként](./functions-bindings-storage-blob-input.md)szolgál.

Az Azure Blob storage-eseményindító általános célú tárfiókot igényel. Blob-csak fiók használatához, vagy ha az alkalmazás speciális igényekkel rendelkezik, tekintse át az ezt az eseményindítót használó alternatívákat.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-storage-blob.md)olvashat.

## <a name="alternatives"></a>Alternatív megoldások

### <a name="event-grid-trigger"></a>Eseményrács eseményindítója

Az [Event Grid eseményindító](functions-bindings-event-grid.md) is rendelkezik beépített támogatást [blob események.](../storage/blobs/storage-blob-event-overview.md) A Blob storage-eseményindító helyett használjon Eseményrácsot a következő esetekben:

- **Csak blob-storage fiókok:** [Blob csak tárfiókok](../storage/common/storage-account-overview.md#types-of-storage-accounts) támogatottak blob bemeneti és kimeneti kötések, de nem blob eseményindítók.

- **Nagy léptékű:** Nagy méretű lehet lazán definiálható tárolók, amelyek több mint 100 000 blobok bennük vagy tárfiókok, amelyek másodpercenként több mint 100 blob frissítések et.

- **Késés minimalizálása:** Ha a függvényalkalmazás a felhasználási terv, akár 10 perces késéssel dolgozhatja fel az új blobok, ha egy függvényalkalmazás tétlen. A késés elkerülése érdekében átválthat egy App Service-csomagra, amelyen az Mindig be van kapcsolva. Az [Event Grid eseményindítót](functions-bindings-event-grid.md) is használhat a Blob storage-fiókjával. Például tekintse meg az [Event Grid oktatóanyagát.](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)

Tekintse meg a [kép átméretezése eseményrács](../event-grid/resize-images-on-storage-blob-upload-event.md) bemutató egy Event Grid példa.

### <a name="queue-storage-trigger"></a>Queue Storage-eseményindító

A blobok feldolgozásának másik megközelítése a létrehozott vagy módosított bloboknak megfelelő várólista-üzenetek írása, majd [a várólista-tárolási eseményindító](./functions-bindings-storage-queue.md) használata a feldolgozás megkezdéséhez.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely naplót ír, amikor egy blobot ad nak hozzá vagy frissítenek a `samples-workitems` tárolóban.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A `{name}` blob eseményindító `samples-workitems/{name}` elérési útján lévő karakterlánc létrehoz egy [kötési kifejezést,](./functions-bindings-expressions-patterns.md) amely a függvénykódban használható az eseményindító blob fájlnevének eléréséhez. További információ: [Blob name patterns](#blob-name-patterns) később ebben a cikkben.

Az attribútumról `BlobTrigger` további információt az [attribútumok és a hozzájegyzetek című témakörben talál.](#attributes-and-annotations)

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy blob-eseményindító-kötést mutat be egy *function.json* fájlban és a kötést használó kódban. A függvény naplót ír, amikor egy blobot ad nak hozzá vagy frissítenek a `samples-workitems` [tárolóban.](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)

A *function.json* fájlban a kötési adatok:

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

A `{name}` blob eseményindító `samples-workitems/{name}` elérési útján lévő karakterlánc létrehoz egy [kötési kifejezést,](./functions-bindings-expressions-patterns.md) amely a függvénykódban használható az eseményindító blob fájlnevének eléréséhez. További információ: [Blob name patterns](#blob-name-patterns) később ebben a cikkben.

A *function.json* fájltulajdonságairól a [Konfiguráció](#configuration) szakasz ismerteti ezeket a tulajdonságokat című témakörben talál.

Itt a C# script kódot, `Stream`amely kötődik a:

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Itt a C# script kódot, `CloudBlockBlob`amely kötődik a:

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő példa egy blob-eseményindító-kötést mutat be egy *function.json* fájlban és [javascript-kódot,](functions-reference-node.md) amely a kötést használja. A függvény naplót ír, ha egy blob `samples-workitems` ot ad hozzá vagy frissít a tárolóban.

Itt a *function.json* fájl:

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

A `{name}` blob eseményindító `samples-workitems/{name}` elérési útján lévő karakterlánc létrehoz egy [kötési kifejezést,](./functions-bindings-expressions-patterns.md) amely a függvénykódban használható az eseményindító blob fájlnevének eléréséhez. További információ: [Blob name patterns](#blob-name-patterns) később ebben a cikkben.

A *function.json* fájltulajdonságairól a [Konfiguráció](#configuration) szakasz ismerteti ezeket a tulajdonságokat című témakörben talál.

Itt a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

A következő példa egy blob-eseményindító tincsét mutatja be egy *function.json* fájlban és [a Python-kódot,](functions-reference-python.md) amely a kötést használja. A függvény naplót ír, amikor egy blobot ad nak hozzá vagy frissítenek a `samples-workitems` [tárolóban.](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)

Itt a *function.json* fájl:

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

A `{name}` blob eseményindító `samples-workitems/{name}` elérési útján lévő karakterlánc létrehoz egy [kötési kifejezést,](./functions-bindings-expressions-patterns.md) amely a függvénykódban használható az eseményindító blob fájlnevének eléréséhez. További információ: [Blob name patterns](#blob-name-patterns) később ebben a cikkben.

A *function.json* fájltulajdonságairól a [Konfiguráció](#configuration) szakasz ismerteti ezeket a tulajdonságokat című témakörben talál.

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

# <a name="java"></a>[Java](#tab/java)

Ez a függvény naplót ír, ha egy `myblob` blob ot ad nak hozzá vagy frissítenek a tárolóban.

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

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)a következő attribútumokkal konfigurálhat egy blobeseményindítót:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Az attribútum konstruktora egy elérési út karakterláncot vesz igénybe, amely jelzi a figyelni kívánt tárolót és adott esetben egy [blobnév-mintát.](#blob-name-patterns) Például:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  Beállíthatja, `Connection` hogy a tulajdonság adja meg a tárfiókot használni, ahogy az a következő példában látható:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  A teljes példa: [Trigger .](#example)

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módja a használandó tárfiók megadásának. A konstruktor egy tárolási kapcsolati karakterláncot tartalmazó alkalmazásbeállítás nevét veszi fel. Az attribútum a paraméter, a metódus vagy az osztály szintjén alkalmazható. A következő példa az osztályszintet és a metódusszintet mutatja be:

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

A felhasználandó tárfiókot a következő sorrendben határozzuk meg:

* Az `BlobTrigger` attribútum tulajdona. `Connection`
* Az `StorageAccount` `BlobTrigger` attribútummal azonos paraméterre alkalmazott attribútum.
* A `StorageAccount` függvényre alkalmazott attribútum.
* Az `StorageAccount` osztályra alkalmazott attribútum.
* A függvényalkalmazás alapértelmezett tárfiókja ("AzureWebJobsStorage" alkalmazásbeállítás).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

Az `@BlobTrigger` attribútum segítségével hozzáférést biztosít a blob, amely elindította a függvényt. A részleteket lásd az [eseményindító példában.](#example)

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `BlobTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `blobTrigger`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction** | n/a | A beállításnak `in`a beállítására kell beállítható. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. Kivételek vannak feljegyezve a [használati](#usage) szakaszban. |
|**név** | n/a | A blobot a függvénykódban jelölő változó neve. |
|**Elérési út** | **BlobPath** |A figyelni figyelt [tároló.](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources)  Lehet, hogy egy [blob név mintát](#blob-name-patterns). |
|**Kapcsolat** | **Kapcsolat** | A kötéshez használandó Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például "MyStorage" beállítást ad meg, `connection` a Functions futásidejű megkeresi az "AzureWebJobsMyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.<br><br>A kapcsolati karakterláncnak általános célú tárfiókhoz kell tartasztódnia, nem [blobtár-fiókhoz.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Blob-adatok `context.bindings.<NAME>` elérése `<NAME>` a *function.json*ban megadott értéknek megfelelő helyen.

# <a name="python"></a>[Python](#tab/python)

Blob-adatok elérése [az InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)néven beírt paraméteren keresztül. A részleteket lásd az [eseményindító példában.](#example)

# <a name="java"></a>[Java](#tab/java)

Az `@BlobTrigger` attribútum segítségével hozzáférést biztosít a blob, amely elindította a függvényt. A részleteket lásd az [eseményindító példában.](#example)

---

## <a name="blob-name-patterns"></a>Blob névmintái

Megadhat egy blob névmintát `path` a tulajdonságban a *function.json* vagy az `BlobTrigger` attribútum konstruktor. A névminta lehet [szűrő vagy kötési kifejezés.](./functions-bindings-expressions-patterns.md) A következő szakaszok példákat mutatnak be.

### <a name="get-file-name-and-extension"></a>Fájlnév és kiterjesztés beszerezése

A következő példa bemutatja, hogyan lehet külön-külön kötődni a blobfájl nevéhez és kiterjesztéséhez:

```json
"path": "input/{blobname}.{blobextension}",
```

Ha a blob neve *eredeti-Blob1.txt,* `blobname` a `blobextension` függvénykódban lévő és változók értékei *eredeti Blob1* és *txt*.

### <a name="filter-on-blob-name"></a>Szűrés a blob nevére

A következő példa csak az `input` "eredeti-" karakterlánccal kezdődő tárolóban lévő blobokon aktiválódik:

```json
"path": "input/original-{name}",
```

Ha a blob neve *eredeti Blob1.txt,* `name` a változó értéke `Blob1`a függvénykódban.

### <a name="filter-on-file-type"></a>Szűrés fájltípusra

A következő példa csak *.png* fájlokesetén aktiválódik:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Kapcsos zárójelek szűrése fájlnevekben

Ha a fájlnevekben kapcsos zárójeleket szeretne keresni, két kapcsos zárójel használatával hagyja ki a kapcsos zárójeleket. A következő példa szűri azokat a blobokat, amelyek nek kapcsos zárójelek vannak a nevében:

```json
"path": "images/{{20140101}}-{name}",
```

Ha a blob neve `name` * {20140101}-soundfile.mp3*, a függvénykódban a változó értéke *soundfile.mp3*.

## <a name="metadata"></a>Metaadatok

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

A metaadatok pythonban nem érhetők el.

# <a name="java"></a>[Java](#tab/java)

A metaadatok java nyelven nem érhetők el.

---

## <a name="blob-receipts"></a>Blob-visszaigazolások

Az Azure Functions futásidejű biztosítja, hogy egyetlen blob eseményindító függvény nem lesz meghívva többször az új vagy frissített blob. Annak megállapításához, hogy egy adott blobverzió feldolgozása folyamatban van-e, fenntartja a *blobnyugtákat.*

Az Azure Functions az *Azure-webjobs-hosts* nevű tárolóban tárolja a blobnyugtákat a függvényalkalmazás (az alkalmazásbeállítás `AzureWebJobsStorage`által meghatározott) Azure storage-fiókban. A blobnyugta a következő adatokkal rendelkezik:

* Az aktivált függvény ("*&lt;függvényalkalmazás neve>. * Funkciók. függvénynév>", például: "MyFunctionApp.Functions.CopyBlob") * &lt; *
* A tároló neve
* A blob típusa ("BlockBlob" vagy "PageBlob")
* A blob neve
* Az ETag (blob verzióazonosítója, például: "0x8D1DC6E70A277EF")

Egy blob újrafeldolgozásának kényszerítéséhez törölje a blob blobnyugta az adott blob az *azure-webjobs-hosts* tároló manuálisan. Előfordulhat, hogy az újrafeldolgozás nem következik be azonnal, akkor garantáltan egy későbbi időpontban fordul elő. A *scaninfo* blob az *azure-webjobs-hosts/blobscaninfo* azonnal újrafeldolgozható. A `LatestScan` tulajdonság után utoljára módosított időbélyeggel rendelkező blobok újra bekerülnek a rendszerbe.

## <a name="poison-blobs"></a>Méreg foltok

Ha egy blob eseményindító függvény sikertelen egy adott blob, az Azure Functions újrapróbálkozik, amely alapértelmezés szerint összesen 5-ször működik.

Ha mind az 5 próbálkozás sikertelen, az Azure Functions egy *webjobs-blobtrigger-poison*nevű tárolóvárólistához ad hozzá egy üzenetet. Az újrapróbálkozások maximális száma konfigurálható. Ugyanaz tasza tokravári a rendszer a poison blob és a poison queue üzenetkezeléséhez. A méregblobok várólista-üzenete egy JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (a * &lt;formátum függvényalkalmazás neve>. * Funkciók. függvény neve>) * &lt; *
* BlobType ("BlockBlob" vagy "PageBlob")
* ContainerName
* BlobName (BlobName)
* ETag (blob verzióazonosítója, például: "0x8D1DC6E70A277EF")

## <a name="concurrency-and-memory-usage"></a>Egyidejűség és memóriahasználat

A blob eseményindító egy várósort használ belsőleg, így az egyidejű függvénymeghívások maximális számát a [host.json várólisták konfigurációja szabályozza.](functions-host-json.md#queues) Az alapértelmezett beállítások 24 meghívásra korlátozzák az egyidejűséget. Ez a korlát külön-külön vonatkozik minden egyes függvény, amely egy blob eseményindító.

[A felhasználási terv](functions-scale.md#how-the-consumption-and-premium-plans-work) egy virtuális gépen (VM) lévő függvényalkalmazást 1,5 GB memóriára korlátozza. A memóriát minden egyidejűleg végrehajtó függvénypéldány és maga a Functions futásidejű használja. Ha egy blob által aktivált függvény betölti a teljes blobot a memóriába, a függvény által csak a blobok számára használt maximális memória 24 * maximális blobméret. Például egy függvényalkalmazás három blob-aktivált függvények és az alapértelmezett beállításokat kellene egy maximális virtuális gép egyidejűség3*24 = 72 függvény meghívása.

JavaScript és Java függvények töltse be a teljes blob ot `string`a `Byte[]`memóriába, és a C# függvények ezt teszik, ha a , vagy POCO-hoz kötődik.

## <a name="polling"></a>Szavazó

A lekérdezés hibridként működik a naplók ellenőrzése és a rendszeres tárolók vizsgálata között. Blobok beolvasott csoportokban 10 000 egy időben egy folytatási jogkivonat ot használ az intervallumok között.

> [!WARNING]
> Emellett [a tárolási naplók jönnek létre a "legjobb erőfeszítés"](/rest/api/storageservices/About-Storage-Analytics-Logging) alapján. Nincs garancia arra, hogy minden eseményt rögzítenek. Bizonyos körülmények között előfordulhat, hogy a naplók kimaradnak.
> 
> Ha gyorsabb vagy megbízhatóbb blobfeldolgozásra van szüksége, fontolja meg egy [várólista-üzenet](../storage/queues/storage-dotnet-how-to-use-queues.md) létrehozását a blob létrehozásakor. Ezután a blob eseményindító helyett [használjon várólista-eseményindítót](functions-bindings-storage-queue.md) a blob feldolgozásához. Egy másik lehetőség az Event Grid használata; lásd a bemutató [Automatizálja átméretezés feltöltött képek et Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="next-steps"></a>További lépések

- [Blob-tárolási adatok olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
- [Blob-tárolási adatok írása egy függvényből](./functions-bindings-storage-blob-output.md)
