---
title: Az Azure Blob storage kimeneti kötése az Azure Functions-hez
description: Ismerje meg, hogyan biztosíthat Azure Blob-tárolási adatokat egy Azure-függvényszámára.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: c6e15c9a99a78f0f3637f718b35462fe49fd5ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277244"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Az Azure Blob storage kimeneti kötése az Azure Functions-hez

A kimeneti kötés lehetővé teszi a blobtárolási adatok módosítását és törlését egy Azure-függvényben.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-storage-blob.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvény,](functions-dotnet-class-library.md) amely egy blob-eseményindítót és két kimeneti blob-kötést használ. A függvényt egy lemezképblob létrehozása váltja ki a *mintarendszer-tárolóban.* Kis és közepes méretű másolatokat hoz létre a képblobról.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

<!--Same example for input and output. -->

A következő példa blob bemeneti és kimeneti kötéseket mutat be egy *function.json* fájlban és a [C# parancsfájl (.csx)](functions-reference-csharp.md) kódban, amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti. A függvényt egy várólista-üzenet váltja ki, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname}-Copy*.

A *function.json* fájlban `queueTrigger` a metaadat-tulajdonság a blob `path` nevének megadására szolgál a tulajdonságokban:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

<!--Same example for input and output. -->

A következő példa blob bemeneti és kimeneti kötéseket mutat be egy *function.json* fájlban és a kötéseket használó [JavaScript-kódban.](functions-reference-node.md) A függvény másolatot készít egy blobról. A függvényt egy várólista-üzenet váltja ki, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname}-Copy*.

A *function.json* fájlban `queueTrigger` a metaadat-tulajdonság a blob `path` nevének megadására szolgál a tulajdonságokban:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

A következő példa blob bemeneti és kimeneti kötéseket mutat be egy *function.json* fájlban és a kötéseket használó [Python-kódban.](functions-reference-python.md) A függvény másolatot készít egy blobról. A függvényt egy várólista-üzenet váltja ki, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname}-Copy*.

A *function.json* fájlban `queueTrigger` a metaadat-tulajdonság a blob `path` nevének megadására szolgál a tulajdonságokban:

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-eseményindító a OutputBinding használatával](#http-trigger-using-outputbinding-java)
* [Várólista-eseményindító függvény visszatérési értékének használatával](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-eseményindító, OutputBinding (Java) használatával

 A következő példa egy Java `HttpTrigger` függvényt mutat be, amely a jegyzetet használja egy blobtárolóban lévő fájl nevét tartalmazó paraméter fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és a `byte[]`tartalmát a függvénybe továbbítja. A `BlobOutput` jegyzet a programhoz `OutputBinding outputItem`kötődik, amelyet a függvény a bemeneti blob tartalmának a beállított tárolóba való írására használ.

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

#### <a name="queue-trigger-using-function-return-value-java"></a>Várólista-eseményindító függvény visszatérési értékének használatával (Java)

 A következő példa egy Java-függvényt mutat be, amely a `QueueTrigger` jegyzetet használja egy blobtárolóban lévő fájl nevét tartalmazó üzenet fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és a `byte[]`tartalmát a függvénybe továbbítja. A `BlobOutput` jegyzet a függvény visszatérési értékéhez kötődik, amelyet a futásidejű a bemeneti blob tartalmának a beállított tárolóba való írásához használ.

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

 A [Java függvények futásidejű függvénytárban](/java/api/overview/azure/functions/runtime)használja a `@BlobOutput` funkcióparaméterek et, amelyek értékét a blob storage-ban lévő objektumba írná.  A paramétertípusnak `OutputBinding<T>`a : -nak kell lennie, ahol T bármely natív Java típus vagy POJO.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [BlobAttribute attribútumot.](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)

Az attribútum konstruktora a blob `FileAccess` elérési útját és az olvasást vagy írást jelző paramétert veszi át, ahogy az a következő példában látható:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

Beállíthatja, `Connection` hogy a tulajdonság adja meg a tárfiókot használni, ahogy az a következő példában látható:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

Az `@BlobOutput` attribútum hozzáférést biztosít a blobhoz, amely elindította a függvényt. Ha bájttömböt használ az attribútumhoz, `binary`állítsa a . `dataType` A részleteket lásd a [kimeneti példában.](#example)

---

A teljes példa: [Kimenet i](#example).

Az `StorageAccount` attribútum segítségével megadhatja a tárfiókot osztály, metódus vagy paraméter szinten. További információ: [Trigger - attributes](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Blob` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `blob`a beállítására kell beállítható. |
|**direction** | n/a | Kimeneti `out` kötéshez be kell állítani. Kivételek vannak feljegyezve a [használati](#usage) szakaszban. |
|**név** | n/a | A blobot a függvénykódban jelölő változó neve.  A `$return` függvény visszatérési értékére való hivatkozás beállítása.|
|**Elérési út** |**BlobPath** | A blob tároló elérési útja. |
|**Kapcsolat** |**Kapcsolat**| A kötéshez használandó Storage-kapcsolati karakterláncot tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például "MyStorage" beállítást ad meg, `connection` a Functions futásidejű megkeresi az "AzureWebJobsMyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.<br><br>A kapcsolati karakterláncnak általános célú tárfiókhoz kell tartasztható, nem [csak blobalapú tárfiókhoz.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|n/a | **Hozzáférés** | Azt jelzi, hogy olvasni vagy írni fog.Indicates whether you will be reading or writing. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

JavaScript-ben a blobadatok `context.bindings.<name from function.json>`elérése a használatával.

# <a name="python"></a>[Python](#tab/python)

A függvényparamétereket a következő típusokként deklarálhatja a blobstorage-ba:

* Karakterláncok`func.Out(str)`
* Adatfolyamok`func.Out(func.InputStream)`

A részleteket lásd a [kimeneti példában.](#example)

# <a name="java"></a>[Java](#tab/java)

Az `@BlobOutput` attribútum hozzáférést biztosít a blobhoz, amely elindította a függvényt. Ha bájttömböt használ az attribútumhoz, `binary`állítsa a . `dataType` A részleteket lásd a [kimeneti példában.](#example)

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszaküldési kódok

| Kötés |  Referencia |
|---|---|
| Blob | [Blob-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| Blob, Tábla, Várólista |  [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Tábla, Várólista |  [hibaelhárítással](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>További lépések

- [Függvény futtatása blobtárolási adatok változásakor](./functions-bindings-storage-blob-trigger.md)
- [Blob-tárolási adatok olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
