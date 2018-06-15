---
title: Az Azure Functions (kísérleti) külső fájl kötései
description: Külső fájl kötések az Azure Functions használatával
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/27/2017
ms.author: alkarche
ms.openlocfilehash: 4e9c2c336df465d7488de84bd2a02cc5d9e42f30
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
ms.locfileid: "27607921"
---
# <a name="azure-functions-external-file-bindings-experimental"></a>Azure Functions külső fájl kötések (kísérleti)
Ez a cikk bemutatja, hogyan kezelheti az Azure Functions különböző Szolgáltatottszoftver-szolgáltatók (például a Dropbox vagy a Google-meghajtó) fájlok. Azure Functions támogatja indítás, bemeneti és kimeneti külső fájlokat kötései. Ilyen kötést Szolgáltatottszoftver-szolgáltatókkal API kapcsolatok létrehozása vagy meglévő, a függvény App erőforráscsoportból API-kapcsolat használata.

> [!IMPORTANT]
> A külső fájl kötések kísérleti, és előfordulhat, hogy soha nem érik el az általánosan rendelkezésre álló (GA) állapotát. Csak az Azure-ban szerepelnek 1.x működik, és nem tervezi, adja hozzá az Azure Functions vannak 2.x. A Szolgáltatottszoftver-szolgáltatók adatok elérését igénylő forgatókönyvek esetén érdemes [a logic apps függvényekké hívó](functions-twitter-email.md). Tekintse meg a [Logic Apps fájlrendszer összekötő](../logic-apps/logic-apps-using-file-connector.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="available-file-connections"></a>A fájl elérhető kapcsolatok

|Összekötő|Eseményindító|Input (Bemenet)|Kimenet|
|:-----|:---:|:---:|:---:|
|[Mezőbe](https://www.box.com)|x|x|x
|[Dropbox-bA](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[Onedrive vállalati verzió](https://onedrive.live.com)|x|x|x
|[OneDrive Vállalati verzió](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google-meghajtó](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Külső fájl kapcsolatok is használható a [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="trigger"></a>Eseményindító

A külső fájl eseményindító lehetővé teszi a távoli mappa megfigyelése, és a funkciókódot futtatását észlel.

## <a name="trigger---example"></a>Eseményindító – példa

Tekintse meg a nyelvspecifikus példát:

* [C# parancsfájl](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-script-example"></a>Eseményindító - C# parancsfájl – példa

A következő példa bemutatja egy külső fájl eseményindító kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény minden egyes fájl adnak, akkor a figyelt mappa tartalmát naplózza.

Itt az kötés adatai a *function.json* fájlt:

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

A C# parancsfájl kód itt látható:

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

### <a name="trigger---javascript-example"></a>Eseményindító - JavaScript – példa

A következő példa bemutatja egy külső fájl eseményindító kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény minden egyes fájl adnak, akkor a figyelt mappa tartalmát naplózza.

Itt az kötés adatai a *function.json* fájlt:

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

A JavaScript-kód itt látható:

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

## <a name="trigger---configuration"></a>Eseményindító - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|---------|----------------------|
|**típusa** | meg kell `apiHubFileTrigger`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya** | meg kell `in`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**név** | Esemény-elem funkciókódot jelölő neve. | 
|**kapcsolat**| Az Alkalmazásbeállítás, amely tárolja a kapcsolati karakterlánc azonosítja. Az Alkalmazásbeállítás automatikusan létrejön a kapcsolat az integráció felhasználói felület az Azure-portálon való hozzáadásakor.|
|**elérési út** | A mappa figyelése, és opcionálisan egy mintát.|

### <a name="name-patterns"></a>Név minták

Megadhatja a Fájlnévminta a `path` tulajdonság. A hivatkozott mappa léteznie kell a Szolgáltatottszoftver-szolgáltató.

Példák:

```json
"path": "input/original-{name}",
```

Az elérési út megkereshető egy fájlt *eredeti-File1.txt* a a *bemeneti* mappa, és az értéke a `name` funkciókódot változót lenne `File1.txt`.

Egy másik példa:

```json
"path": "input/{filename}.{fileextension}",
```

Ezt az elérési utat is tenné található nevű fájl *eredeti-File1.txt*, és az értéke a `filename` és `fileextension` funkciókódot változók lenne *eredeti-File1* és *txt* .

A fájlok a fájl típusa a fájlnévkiterjesztéshez rögzített érték használatával korlátozhatja. Példa:

```json
"path": "samples/{name}.png",
```

Ebben az esetben csak a *.png* -fájlok a *minták* mappa indul el, a függvény.

Kapcsos zárójelek speciális karakterek a mintában. Adja meg, amelyeknek a neve kapcsos zárójelek fájlneveket, a duplán a kapcsos zárójelek.
Példa:

```json
"path": "images/{{20140101}}-{name}",
```

Az elérési út megkereshető egy fájlt *{20140101}-soundfile.mp3* a a *képek* mappa, és a `name` változó értékét a funkciókódot lenne *soundfile.mp3*.

## <a name="trigger---usage"></a>Eseményindító - használat

A C# függvények, akkor eszközben csatlakozzon a bemeneti fájl adatainak elnevezett paraméter a függvényaláíráshoz a például `<T> <name>`.
Ha `T` , az adatokat, írja be, hogy szeretné-e deszerializálni az adatokat, és `paramName` a megadott név a [JSON indítás](#trigger). A Node.js funkciókat érheti el a bemeneti fájl adatainak használatával `context.bindings.<name>`.

A fájl is deszerializálható a következő típusok:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) - fájl JSON-szerializált adatoknál lehet hasznos.
  Ha egy egyéni bemeneti típus deklarálhatja (pl. `FooType`), az Azure Functions megkísérli a JSON-adatok deszerializálása be a megadott típus.
* Karakterlánc - szöveg fájladatok esetén hasznos.

A C# funkciók is köthető a következő típusok, és a Functions futtatókörnyezete megkísérli az adott típusú fájl adatok deszerializálása:

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

## <a name="trigger---poison-files"></a>Eseményindító - poison fájlok

Ha egy külső fájl funkció nem sikerül, az Azure Functions újrapróbálkozik a függvény legfeljebb 5-ször (beleértve az első próbálkozás) alapértelmezés szerint egy adott fájlra vonatkozó.
Minden 5 próbálkozás sikertelen lesz, ha funkciókat ad hozzá egy üzenet nevű tároló várólista *webjobs-apihubtrigger-poison*. A várólista poison fájlok üzenet egy JSON-objektum, amely tartalmazza a következő tulajdonságokkal:

* FunctionId (formátumú  *&lt;függvény alkalmazás neve >*. Működik.  *&lt;függvény neve >*)
* Fájltípus
* Mappanév
* Fájlnév
* ETag (például egy fájl verziójának azonosítója: "0x8D1DC6E70A277EF")

## <a name="input"></a>Input (Bemenet)

Az Azure külső fájl bemeneti kötése lehetővé teszi, hogy a fájl egy külső mappából a függvényben használható.

## <a name="input---example"></a>Bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C# parancsfájl](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Bemenet – C# parancsfájl – példa

A következő példa bemutatja külső fájl a bemeneti és kimeneti kötések egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy bemeneti fájl kimeneti fájl másolja.

Itt az kötés adatai a *function.json* fájlt:

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

A C# parancsfájl kód itt látható:

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

### <a name="input---javascript-example"></a>Bemenet – JavaScript – példa

A következő példa bemutatja külső fájl a bemeneti és kimeneti kötések egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy bemeneti fájl kimeneti fájl másolja.

Itt az kötés adatai a *function.json* fájlt:

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

A JavaScript-kód itt látható:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="input---configuration"></a>Adjon meg - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|---------|----------------------|
|**típusa** | meg kell `apiHubFile`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya** | meg kell `in`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**név** | Esemény-elem funkciókódot jelölő neve. | 
|**kapcsolat**| Az Alkalmazásbeállítás, amely tárolja a kapcsolati karakterlánc azonosítja. Az Alkalmazásbeállítás automatikusan létrejön a kapcsolat az integráció felhasználói felület az Azure-portálon való hozzáadásakor.|
|**elérési út** | A mappa nevét és a fájl nevét kell tartalmaznia. Ha például egy [várólista eseményindító](functions-bindings-storage-queue.md) a függvényben használható `"path": "samples-workitems/{queueTrigger}"` egy fájlra mutasson a `samples-workitems` eseményindító üzenetben megadott fájlnév egyező nevű mappa.   

## <a name="input---usage"></a>Bemenet - használat

A C# függvények, akkor eszközben csatlakozzon a bemeneti fájl adatainak elnevezett paraméter a függvényaláíráshoz a például `<T> <name>`. `T`az adattípus, hogy szeretné-e deszerializálni az adatokat, és `name` bemeneti kötésben megadott neve. A Node.js funkciókat érheti el a bemeneti fájl adatainak használatával `context.bindings.<name>`.

A fájl is deszerializálható a következő típusok:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) - fájl JSON-szerializált adatoknál lehet hasznos.
  Ha egy egyéni bemeneti típus deklarálhatja (pl. `InputType`), az Azure Functions megkísérli a JSON-adatok deszerializálása be a megadott típus.
* Karakterlánc - szöveg fájladatok esetén hasznos.

A C# funkciók is köthető a következő típusok, és a Functions futtatókörnyezete megkísérli az adott típusú fájl adatok deszerializálása:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="output"></a>Kimenet

Az Azure külső fájl kimeneti kötés lehetővé teszi a fájlok írása a függvény egy külső mappájába.

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a [bemeneti kötése például](#input---example).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájlt.

|Function.JSON tulajdonság | Leírás|
|---------|---------|----------------------|
|**típusa** | meg kell `apiHubFile`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**iránya** | meg kell `out`. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**név** | Esemény-elem funkciókódot jelölő neve. | 
|**kapcsolat**| Az Alkalmazásbeállítás, amely tárolja a kapcsolati karakterlánc azonosítja. Az Alkalmazásbeállítás automatikusan létrejön a kapcsolat az integráció felhasználói felület az Azure-portálon való hozzáadásakor.|
|**elérési út** | A mappa nevét és a fájl nevét kell tartalmaznia. Ha például egy [várólista eseményindító](functions-bindings-storage-queue.md) a függvényben használható `"path": "samples-workitems/{queueTrigger}"` egy fájlra mutasson a `samples-workitems` eseményindító üzenetben megadott fájlnév egyező nevű mappa.   

## <a name="output---usage"></a>Kimeneti - használat

A C# függvények, akkor eszközben csatlakozzon a kimeneti fájl a megnevezett `out` a függvényaláíráshoz a paraméter, például `out <T> <name>`, ahol `T` , az adatokat, írja be, hogy szeretné-e szerializálni az adatokat, és `name` megadott neve a kimeneti kötése. A Node.js funkciókat érheti el a kimeneti fájl használatával `context.bindings.<name>`.

A kimeneti fájlba, a következő típusok használatával írhat:

* Bármely [objektum](https://msdn.microsoft.com/library/system.object.aspx) – a JSON-szerializálás hasznos.
  Ha egy egyéni kimeneti típust deklarálhatja (pl. `out OutputType paramName`), az Azure Functions megkísérli objektum szerializálása a JSON-ba. A kimeneti paraméter értéke null, ha a függvény lép ki, ha a Functions futtatókörnyezete null objektumot, létrehoz egy fájlt.
* Karakterlánc - (`out string paramName`) szöveges fájl adatoknál lehet hasznos. a Functions futtatókörnyezete létrehoz egy fájlt, csak akkor, ha a karakterlánc-paraméter null értékű akkor, ha kilép, a függvény.

A C# funkciók is a kimenetre küldheti a következő típusok egyikének sem:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
