---
title: (Kísérleti funkció) Azure Functions külső fájlkötések
description: Külső fájlkötések használata az Azure Functions szolgáltatásban
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: cshoe
ms.openlocfilehash: 765eab8dfc1163c4d9e0337a1af840278ae1a82c
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546266"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Az Azure Functions külső fájlkötések (kísérleti funkció)
Ez a cikk bemutatja, hogyan módosíthatja a fájlokat az Azure Functions különböző SaaS-szolgáltatók (például a Dropboxot vagy a Google Drive-bA). Az Azure Functions támogatja a-trigger, bemeneti és kimeneti kötések külső fájlok. Ilyen kötést ahhoz a SaaS-szolgáltatók API-kapcsolatok létrehozása, vagy használja a meglévő API-kapcsolatok a Függvényalkalmazás erőforráscsoportból.

> [!IMPORTANT]
> A külső fájlkötések kísérleti, és előfordulhat, hogy soha nem érik el az általánosan elérhető (GA) állapotát. Csak az Azure-ban szerepelnek 1.x, és nem is tervezzük az Azure Functions hozzáadandó 2.x. Az SaaS-szolgáltatók adatokhoz való hozzáférést igénylő forgatókönyvek esetén fontolja meg [logikai alkalmazásokat, amelyek hívásokat indítani olyan funkciók](functions-twitter-email.md). Tekintse meg a [Logic Apps-fájlrendszer-összekötő](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>Rendelkezésre állású kapcsolatok

|Összekötő|Eseményindító|Input (Bemenet)|Kimenet|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/deploy-ftp)|x|x|x
|[Onedrive vállalati verzió](https://onedrive.live.com)|x|x|x
|[OneDrive Vállalati verzió](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google drive-on](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Külső fájl kapcsolatok is használható a [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Eseményindító

A külső fájl eseményindító lehetővé teszi a távoli mappa megfigyelése, és futtassa a függvény kódját, módosítások észlelése esetén.

## <a name="trigger---example"></a>Az eseményindító – példa

Tekintse meg az adott nyelvű példa:

* [C#-szkript](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Eseményindító - C#-szkript példa

Az alábbi példa bemutatja egy kötelező a külső fájl eseményindító egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény naplózza a figyelt mappához hozzáadott minden fájl tartalmát.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Íme a C#-szkriptkódot:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript-példa

Az alábbi példa bemutatja egy kötelező a külső fájl eseményindító egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény naplózza a figyelt mappához hozzáadott minden fájl tartalmát.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|---------|----------------------|
|**type** | Meg kell `apiHubFileTrigger`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | A függvénykód esemény elemet képviselő változó neve. | 
|**kapcsolat**| Adja meg az Alkalmazásbeállítás, amely tárolja a kapcsolati karakterláncot. Az integráció felhasználói felület az Azure Portalon a kapcsolat hozzáadásakor a rendszer automatikusan létrehozza az alkalmazásbeállítást.|
|**path** | A mappa figyelése, és opcionálisan egy mintát.|

### <a name="name-patterns"></a>Név-minták

A fájl neve mintát is megadhat a `path` tulajdonság. A hivatkozott mappa léteznie kell az SaaS-szolgáltatónak.

Példák:

```json
"path": "input/original-{name}",
```

Ezt az elérési utat szeretné nevű fájl található *eredeti-File1.txt* a a *bemeneti* mappát, és az értékét a `name` lenne, a függvény kódját a változó `File1.txt`.

Egy másik példa:

```json
"path": "input/{filename}.{fileextension}",
```

Ezt az elérési utat is keresse meg a nevű fájl *eredeti-File1.txt*, és az értéke a `filename` és `fileextension` változók a függvénykódot lenne *eredeti-File1* és *txt* .

A fájlok a fájl típusa a fájlkiterjesztés rögzített érték használatával korlátozhatja. Példa:

```json
"path": "samples/{name}.png",
```

Ebben az esetben, csak *.png* található fájlokat a *minták* mappa aktiválja a függvényt.

Kapcsos zárójelek minták neve speciális karaktereket is. Fájlnevek, amely a kapcsos zárójelek között van a név megadásához a kapcsos zárójelek közötti duplán.
Példa:

```json
"path": "images/{{20140101}}-{name}",
```

Ezt az elérési utat szeretné nevű fájl található  *{20140101}-soundfile.mp3* a a *lemezképek* mappát, és a `name` a függvénykódot a változó értéke lenne *soundfile.mp3*.

## <a name="trigger---usage"></a>Eseményindító - használat

A C#-függvények, meg kötést létrehozni a bemeneti fájl adatainak használatával egy nevesített paraméter a függvényfej például `<T> <name>`.
Ahol `T` , az adatokat, írja be, hogy szeretné-e az adatok importálása, deszerializálni és `paramName` a megadott név a [JSON-trigger](#trigger). A Node.js-függvények, fér hozzá a bemeneti fájl használatával `context.bindings.<name>`.

A fájl is deszerializálható a következők bármelyikét:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) – JSON-szerializált fájladatok esetén hasznos.
  Ha egy egyéni bemenettípus deklarálhatja (pl. `FooType`), az Azure Functions megkísérli a JSON-adatok deszerializálása be a megadott típusra.
* Karakterlánc - szöveg fájladatok esetén hasznos.

A C#-függvények is a következő típusok kell kötni, és a Functions futtatókörnyezete próbál deszerializálni a fájl adatait, hogy a típus használatával:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

<!--- ## Trigger - file receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->

## <a name="trigger---poison-files"></a>Eseményindító - ártalmas fájlok

Ha egy külső fájl által aktivált függvény nem sikerül, az Azure Functions újrapróbálkozik a függvény legfeljebb 5-ször (beleértve az első próbálkozáskor) alapértelmezés szerint egy adott fájlra vonatkozó.
Ha az összes 5 alkalommal sikertelen, a Functions egy üzenetet ad hozzá nevű üzenetsor-tárolóba *webjobs-apihubtrigger-poison*. Az üzenetsorban található üzenet ártalmas fájlok a következő JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (a következő formátumban  *&lt;függvényalkalmazás neve >*. A műveletek.  *&lt;függvény neve >*)
* Fájltípus
* Mappanév
* Fájlnév
* Az ETag (például egy fájl verziójának azonosítója: "0x8D1DC6E70A277EF")

## <a name="input"></a>Input (Bemenet)

Az Azure külső fájl bemeneti kötés lehetővé teszi fájl egy külső mappából a függvényben használható.

## <a name="input---example"></a>Adjon meg – példa

Tekintse meg az adott nyelvű példa:

* [C#-szkript](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Bemenet – C#-szkript példa

Az alábbi példa bemutatja a külső fájl a bemeneti és kimeneti kötései egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy bemeneti fájlt egy kimeneti fájl másolja.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Íme a C#-szkriptkódot:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Bemenet - JavaScript-példa

Az alábbi példa bemutatja a külső fájl a bemeneti és kimeneti kötései egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy bemeneti fájlt egy kimeneti fájl másolja.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|---------|----------------------|
|**type** | Meg kell `apiHubFile`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | Meg kell `in`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | A függvénykód esemény elemet képviselő változó neve. | 
|**kapcsolat**| Adja meg az Alkalmazásbeállítás, amely tárolja a kapcsolati karakterláncot. Az integráció felhasználói felület az Azure Portalon a kapcsolat hozzáadásakor a rendszer automatikusan létrehozza az alkalmazásbeállítást.|
|**path** | A mappa nevét és a fájl nevét kell tartalmaznia. Például, ha rendelkezik egy [üzenetsor eseményindító](functions-bindings-storage-queue.md) a függvényben használható `"path": "samples-workitems/{queueTrigger}"` átirányítása egy fájlt a `samples-workitems` mappát, amely megegyezik a fájlnév, a trigger üzenetben megadott névvel.   

## <a name="input---usage"></a>Bemenet - használat

A C#-függvények, meg kötést létrehozni a bemeneti fájl adatainak használatával egy nevesített paraméter a függvényfej például `<T> <name>`. `T` az adatok, írja be, hogy szeretné-e az adatok importálása, deszerializálni és `name` a bemeneti kötésnek a megadott név. A Node.js-függvények, fér hozzá a bemeneti fájl használatával `context.bindings.<name>`.

A fájl is deszerializálható a következők bármelyikét:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) – JSON-szerializált fájladatok esetén hasznos.
  Ha egy egyéni bemenettípus deklarálhatja (pl. `InputType`), az Azure Functions megkísérli a JSON-adatok deszerializálása be a megadott típusra.
* Karakterlánc - szöveg fájladatok esetén hasznos.

A C#-függvények is a következő típusok kell kötni, és a Functions futtatókörnyezete próbál deszerializálni a fájl adatait, hogy a típus használatával:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Kimenet

Az Azure külső fájl kimeneti kötés lehetővé teszi fájlok írását a függvény egy külső mappájába.

## <a name="output---example"></a>Kimenete – példa

Tekintse meg a [bemeneti kötéssel például](#input---example).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|---------|----------------------|
|**type** | Meg kell `apiHubFile`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | Meg kell `out`. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | A függvénykód esemény elemet képviselő változó neve. | 
|**kapcsolat**| Adja meg az Alkalmazásbeállítás, amely tárolja a kapcsolati karakterláncot. Az integráció felhasználói felület az Azure Portalon a kapcsolat hozzáadásakor a rendszer automatikusan létrehozza az alkalmazásbeállítást.|
|**path** | A mappa nevét és a fájl nevét kell tartalmaznia. Például, ha rendelkezik egy [üzenetsor eseményindító](functions-bindings-storage-queue.md) a függvényben használható `"path": "samples-workitems/{queueTrigger}"` átirányítása egy fájlt a `samples-workitems` mappát, amely megegyezik a fájlnév, a trigger üzenetben megadott névvel.   

## <a name="output---usage"></a>Kimenet – használat

A C#-függvények, meg kötést létrehozni a kimeneti fájl használatával a megnevezett `out` a függvényfej paramétert, például `out <T> <name>`, ahol `T` , az adatokat, írja be, hogy szeretné-e szerializálni az adatokat, és `name` megadott neve a kimeneti kötés. A Node.js-függvények, hozzáférhet a kimeneti fájl használatával `context.bindings.<name>`.

Írható-e a kimeneti fájlt az alábbiak bármelyikével:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) – JSON-szerializálás esetén hasznos.
  Ha azt deklarálja, hogy egy egyéni kimenet típusa (pl. `out OutputType paramName`), az Azure Functions próbál objektum szerializálható JSON-ba. Ha a kimeneti paraméter értéke null, ha a függvény kilép, a Functions futtatókörnyezete létrehoz egy fájlt, Objekt s hodnotou null.
* Karakterlánc - (`out string paramName`) szöveg fájladatok esetén hasznos. a Functions futtatókörnyezete egy fájlt hoz létre, csak akkor, ha a karakterlánc-paraméter nem null értékű, ha a függvény kilép.

A C#-függvények is készíthető, a következő típusok:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
