---
title: "Az Azure Functions külső fájl kötések (előzetes verzió) |} Microsoft Docs"
description: "Külső fájl kötések az Azure Functions használatával"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 7e3b396d290212d3875385521bd7ae92da196b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Az Azure Functions külső fájl kötések (előzetes verzió)
Ez a cikk bemutatja, hogyan különböző Szolgáltatottszoftver-szolgáltatók (például a onedrive vállalati verzió, a Dropbox) való konfigurál a beépített kötések okhoz függvényen belül. Az Azure functions támogatja indítás, bemeneti és kimeneti külső fájl kötései.

A kötés szolgáltatók SaaS API-kapcsolatot hoz létre, vagy használja a meglévő API-kapcsolatok a függvény App erőforráscsoportból.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Támogatott fájl kapcsolatok

|összekötő|Eseményindító|Input (Bemenet)|Kimenet|
|:-----|:---:|:---:|:---:|
|[Mezőbe](https://www.box.com)|x|x|x
|[Dropbox-bA](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[Onedrive vállalati verzió](https://onedrive.live.com)|x|x|x
|[OneDrive Vállalati verzió](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google-meghajtó](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Külső fájl kapcsolatok is használható a [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list)

## <a name="external-file-trigger-binding"></a>Külső fájl kötés indítás

Az Azure külső fájl eseményindító lehetővé teszi a távoli mappa megfigyelése, és futtassa a funkciókódot, amikor észlel.

A külső fájl eseményindító használja a következő JSON-objektumok a `bindings` function.json tömbje

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

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

<a name="receipts"></a>

<!--- ### File receipts
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
<a name="poison"></a>

### <a name="handling-poison-files"></a>Az elhalt fájlokat
Ha egy külső fájl funkció nem sikerül, az Azure Functions újrapróbálkozik a függvény legfeljebb 5-ször (beleértve az első próbálkozás) alapértelmezés szerint egy adott fájlra vonatkozó.
Minden 5 próbálkozás sikertelen lesz, ha funkciókat ad hozzá egy üzenet nevű tároló várólista *webjobs-apihubtrigger-poison*. A várólista poison fájlok üzenet egy JSON-objektum, amely tartalmazza a következő tulajdonságokkal:

* FunctionId (formátumú  *&lt;függvény alkalmazás neve >*. Működik.  *&lt;függvény neve >*)
* Fájltípus
* Mappanév
* Fájlnév
* ETag (például egy fájl verziójának azonosítója: "0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Eseményindító kihasználtsága
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

## <a name="trigger-sample"></a>Eseményindító minta
Tegyük fel, a következő function.json, amely meghatározza egy külső fájl eseményindító:

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

Tekintse meg a nyelvspecifikus mintát, amelyre bejelentkezik a figyelt mappa hozzáadott minden fájl tartalmát.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>A C# eseményindító-használat #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js eseményindító használata

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Külső fájl bemeneti kötése
Az Azure külső fájl bemeneti kötése lehetővé teszi, hogy a fájl egy külső mappából a függvényben használható.

A külső fájl bemenete egy olyan függvényt használja a következő JSON-objektumok a `bindings` function.json tömbje:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Vegye figyelembe a következőket:

* `path`a mappa nevét és a fájl nevét kell tartalmaznia. Ha például egy [várólista eseményindító](functions-bindings-storage-queue.md) a függvényben használható `"path": "samples-workitems/{queueTrigger}"` egy fájlra mutasson a `samples-workitems` eseményindító üzenetben megadott fájlnév egyező nevű mappa.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Bemeneti kihasználtsága
A C# függvények, akkor eszközben csatlakozzon a bemeneti fájl adatainak elnevezett paraméter a függvényaláíráshoz a például `<T> <name>`.
Ha `T` , az adatokat, írja be, hogy szeretné-e deszerializálni az adatokat, és `paramName` a megadott név a [kötés bemeneti](#input). A Node.js funkciókat érheti el a bemeneti fájl adatainak használatával `context.bindings.<name>`.

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


<a name="output"></a>

## <a name="external-file-output-binding"></a>Külső fájl kimeneti kötése
Az Azure külső fájl kimeneti kötés lehetővé teszi a fájlok írása a függvény egy külső mappájába.

A külső függvényt használja a következő JSON-objektumok a kimeneti fájl a `bindings` function.json tömbje:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Vegye figyelembe a következőket:

* `path`a mappa nevét és a fájlnév írni kell tartalmaznia. Ha például egy [várólista eseményindító](functions-bindings-storage-queue.md) a függvényben használható `"path": "samples-workitems/{queueTrigger}"` egy fájlra mutasson a `samples-workitems` eseményindító üzenetben megadott fájlnév egyező nevű mappa.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Kimeneti használata
A C# függvények, akkor eszközben csatlakozzon a kimeneti fájl a megnevezett `out` a függvényaláíráshoz a paraméter, például `out <T> <name>`, ahol `T` , az adatokat, írja be, hogy szeretné-e szerializálni az adatokat, és `paramName` megadott neve a [kimeneti kötése](#output). A Node.js funkciókat érheti el a kimeneti fájl használatával `context.bindings.<name>`.

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

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Bemeneti + kimeneti minta
Tegyük fel, hogy a következő function.json rendelkezik, amely meghatározza egy [tároló várólista eseményindító](functions-bindings-storage-queue.md), a külső fájlban adja meg, és a külső fájlban kimeneti:

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

Tekintse meg a nyelvspecifikus mintát, amely a bemeneti fájl másolása a kimeneti fájl.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>A C# szintaxis #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>A node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
