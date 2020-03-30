---
title: Az Azure Blob storage bemeneti kötése az Azure Functionshez
description: Ismerje meg, hogyan biztosíthat Azure Blob-tárolási adatokat egy Azure-függvényszámára.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202150"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Az Azure Blob storage bemeneti kötése az Azure Functionshez

A bemeneti kötés lehetővé teszi, hogy a blob storage-adatok olvasása egy Azure-függvény bemenetként.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-storage-blob.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvény,](functions-dotnet-class-library.md) amely egy várólista-eseményindítót és egy bemeneti blob-kötést használ. A várólista-üzenet tartalmazza a blob nevét, és a függvény naplózza a blob méretét.

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

A [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a Python kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-eseményindító, blobnév kinézete a lekérdezési karakterláncból](#http-trigger-look-up-blob-name-from-query-string)
* [Várólista-eseményindító, blobnevének fogadása a várólista-üzenetből](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-eseményindító, blobnév kinézete a lekérdezési karakterláncból

 A következő példa egy Java `HttpTrigger` függvényt mutat be, amely a jegyzetet használja egy blobtárolóban lévő fájl nevét tartalmazó paraméter fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és a `byte[]`tartalmát a függvénybe továbbítja.

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

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Várólista-eseményindító, blobnevének fogadása a várólista-üzenetből

 A következő példa egy Java-függvényt mutat be, amely a `QueueTrigger` jegyzetet használja egy blobtárolóban lévő fájl nevét tartalmazó üzenet fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és a `byte[]`tartalmát a függvénybe továbbítja.

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

A [Java függvények futásidejű függvénytárban](/java/api/overview/azure/functions/runtime)használja a `@BlobInput` jegyzeta paramétereket, amelyek értéke egy blobból származik.  Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [BlobAttribute attribútumot.](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)

Az attribútum konstruktora a blob `FileAccess` elérési útját és az olvasást vagy írást jelző paramétert veszi át, ahogy az a következő példában látható:

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

Beállíthatja, `Connection` hogy a tulajdonság adja meg a tárfiókot használni, ahogy az a következő példában látható:

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

Az `StorageAccount` attribútum segítségével megadhatja a tárfiókot osztály, metódus vagy paraméter szinten. További információ: [Trigger - attribútumok és jegyzetek](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

Az `@BlobInput` attribútum hozzáférést biztosít a blobhoz, amely elindította a függvényt. Ha bájttömböt használ az attribútumhoz, `binary`állítsa a . `dataType` A részleteket lásd a [bemeneti példában.](#example)

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `Blob` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | A beállításnak `blob`a beállítására kell beállítható. |
|**direction** | n/a | A beállításnak `in`a beállítására kell beállítható. Kivételek vannak feljegyezve a [használati](#usage) szakaszban. |
|**név** | n/a | A blobot a függvénykódban jelölő változó neve.|
|**Elérési út** |**BlobPath** | A blob elérési útja. |
|**Kapcsolat** |**Kapcsolat**| A kötéshez használandó [Storage-kapcsolati karakterláncot](../storage/common/storage-configure-connection-string.md) tartalmazó alkalmazásbeállítás neve. Ha az alkalmazásbeállítás neve "AzureWebJobs" programmal kezdődik, itt csak a név fennmaradó részét adhatja meg. Ha például "MyStorage" beállítást állít be, `connection` a Functions futásidejű megkeresi az "AzureWebJobsMyStorage" nevű alkalmazásbeállítást. Ha üresen hagyja, `connection` a Functions futásidejű az alapértelmezett Storage-kapcsolati karakterláncot használja a neve súgás `AzureWebJobsStorage`alkalmazásbeállításban.<br><br>A kapcsolati karakterláncnak általános célú tárfiókhoz kell tartasztható, nem [csak blobalapú tárfiókhoz.](../storage/common/storage-account-overview.md#types-of-storage-accounts)|
|n/a | **Hozzáférés** | Azt jelzi, hogy olvasni vagy írni fog.Indicates whether you will be reading or writing. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

Blob-adatok `context.bindings.<NAME>` elérése `<NAME>` a *function.json*ban megadott értéknek megfelelő helyen.

# <a name="python"></a>[Python](#tab/python)

Blob-adatok elérése [az InputStream](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python)néven beírt paraméteren keresztül. A részleteket lásd a [bemeneti példában.](#example)

# <a name="java"></a>[Java](#tab/java)

Az `@BlobInput` attribútum hozzáférést biztosít a blobhoz, amely elindította a függvényt. Ha bájttömböt használ az attribútumhoz, `binary`állítsa a . `dataType` A részleteket lásd a [bemeneti példában.](#example)

---

## <a name="next-steps"></a>További lépések

- [Függvény futtatása blobtárolási adatok változásakor](./functions-bindings-storage-blob-trigger.md)
- [Blob-tárolási adatok írása egy függvényből](./functions-bindings-storage-blob-output.md)
