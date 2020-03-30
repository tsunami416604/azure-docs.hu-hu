---
title: A tartós funkciókban lévő fan-out/fan-in forgatókönyvek – Azure
description: Ismerje meg, hogyan valósíthat meg egy fan-out-fan-in forgatókönyvet az Azure Functions durable functions bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562190"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/fan-in forgatókönyv a durable functions-ben - Felhőalapú biztonsági mentés

*Fan-out /fan-in* utal, hogy a minta végrehajtása több funkciót egyidejűleg, majd bizonyos összesítést az eredményeket. Ez a cikk ismerteti a mintát, amely [tartós függvények](durable-functions-overview.md) segítségével valósítja meg a fan-in/fan-out forgatókönyv megvalósítása. A minta egy tartós függvény, amely biztonsági másolatot ad az alkalmazás webhelyének tartalmáról az Azure Storage-ba.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ebben a példában a függvények feltölti az összes fájlt egy megadott könyvtár alatt rekurzívan blob storage. A feltöltött bájtok teljes számát is megszámolják.

Lehetséges, hogy írjon egy funkció, amely gondoskodik mindenről. A fő probléma, amit befut a **skálázhatóság**. Egyetlen függvény végrehajtása csak egyetlen virtuális gépen futtatható, így az átviteli lesz korlátozva az átviteli keresztül, hogy egyetlen virtuális gép. Egy másik probléma a **megbízhatóság**. Ha a hiba félúton van, vagy ha a teljes folyamat több mint 5 percet vesz igénybe, a biztonsági mentés sikertelen lehet egy részben befejezett állapotban. Ezt követően újra kell indítani.

Egy robusztusabb megközelítés lenne írni két rendszeres függvények: az egyik lenne számba a fájlokat, és adja hozzá a fájlneveket egy várólistába, és egy másik lenne olvasni a várólistából, és töltse fel a fájlokat blob storage. Ez a megközelítés jobb az átviteli és megbízhatósági, de megköveteli, hogy kiépítése és kezelése egy várólistában. Ennél is fontosabb, hogy jelentős összetettség kerül bevezetésre az **államirányítás** és **a koordináció** szempontjából, ha többet szeretne tenni, például jelentse a feltöltött bájtok teljes számát.

A Durable Functions megközelítés az összes említett előnyt nagyon alacsony rezsivel biztosítja.

## <a name="the-functions"></a>A funkciók

Ez a cikk a mintaalkalmazás következő funkcióit ismerteti:

* `E2_BackupSiteContent`: Olyan [orchestrator függvény,](durable-functions-bindings.md#orchestration-trigger) amely a biztonsági másolatot tartalmazó fájlok listájának beszerzésére kéri, `E2_GetFileList` majd minden `E2_CopyFileToBlob` fájl biztonsági biztonsági eléglesz.
* `E2_GetFileList`: Olyan [tevékenységfüggvény,](durable-functions-bindings.md#activity-trigger) amely a könyvtárban lévő fájlok listáját adja vissza.
* `E2_CopyFileToBlob`: Olyan tevékenységfüggvény, amely egyetlen fájlról biztonsági másolatot ad az Azure Blob Storage-ba.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent orchestrator függvény

Ez az orchestrator függvény lényegében a következőket teszi:

1. Egy `rootDirectory` értéket vesz fel bemeneti paraméterként.
2. Megad egy függvényt, hogy rekurzív fájlok listáját a alatt `rootDirectory`kapja meg.
3. Több párhuzamos függvényhívásokat hoz létre, hogy minden fájlt feltölthetaz Azure Blob Storage-ba.
4. Megvárja, amíg az összes feltöltés befejeződik.
5. Az Azure Blob Storage-ba feltöltött összes bájt összegét adja vissza.

# <a name="c"></a>[C #](#tab/csharp)

Itt van a kód, amely megvalósítja az orchestrator függvény:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Figyeld `await Task.WhenAll(tasks);` meg a vonalat. A funkció minden `E2_CopyFileToBlob` egyes hívása *nem* várt, ami lehetővé teszi számukra, hogy párhuzamosan fussanak. Amikor átadjuk ezt a `Task.WhenAll`feladattömböt, visszakapunk egy feladatot, amely nem fejeződik be, *amíg az összes másolási művelet el nem fejeződik.* Ha ismeri a .NET feladatpárhuzamos könyvtárát, akkor ez nem újdonság az Ön számára. A különbség az, hogy ezek a feladatok egyidejűleg több virtuális gépen is futtathatók, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmas legyen a folyamat-újrahasznosítással szemben.

Várakozás után `Task.WhenAll`a , tudjuk, hogy minden függvény hívások befejeződött, és visszaértékeket vissza hozzánk. Minden feltöltött `E2_CopyFileToBlob` bájt számát visszaadó minden hívás visszaadja, így az összes bájtszám kiszámítása az összes visszatérési érték összeadásának kérdése.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A függvény a standard *function.json* függvényt használja az orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Itt van a kód, amely megvalósítja az orchestrator függvény:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Figyeld `yield context.df.Task.all(tasks);` meg a vonalat. A függvény minden `E2_CopyFileToBlob` egyes hívása *nem* volt megadva, ami lehetővé teszi számukra, hogy párhuzamosan fussanak. Amikor átadjuk ezt a `context.df.Task.all`feladattömböt, visszakapunk egy feladatot, amely nem fejeződik be, *amíg az összes másolási művelet el nem fejeződik.* Ha ismeri [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) a JavaScript, akkor ez nem új az Ön számára. A különbség az, hogy ezek a feladatok egyidejűleg több virtuális gépen is futtathatók, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmas legyen a folyamat-újrahasznosítással szemben.

> [!NOTE]
> Bár a feladatok fogalmilag hasonlóak a JavaScript-ígéretekhez, az orchestrator függvényeket a feladatpárhuzamosítás `context.df.Task.all` helyett `context.df.Task.any` `Promise.all` és `Promise.race` azok kezelésére kell használni.

Miután engedett `context.df.Task.all`a , tudjuk, hogy minden függvény hívások befejeződött, és visszaértékeket vissza hozzánk. Minden feltöltött `E2_CopyFileToBlob` bájt számát visszaadó minden hívás visszaadja, így az összes bájtszám kiszámítása az összes visszatérési érték összeadásának kérdése.

---

### <a name="helper-activity-functions"></a>Segítő tevékenységfüggvényei

A segítő tevékenység függvények, mint más minták, csak `activityTrigger` rendszeres függvények, amelyek a trigger kötés.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList tevékenység függvénye

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[Javascript](#tab/javascript)

A *function.json* `E2_GetFileList` fájl a következőképpen néz ki:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

És itt van a végrehajtás:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A függvény `readdirp` a modult (2.x verzió) használja a könyvtárszerkezet rekurzív olvasásához.

---

> [!NOTE]
> Lehet, hogy vajon miért nem tudta csak azért, hogy ezt a kódot közvetlenül az orchestrator funkciót. Lehet, de ez megtörné az orchestrator függvények egyik alapvető szabályát, ami az, hogy soha ne tegyenek I/O-t, beleértve a helyi fájlrendszer-hozzáférést is. További információ: [Orchestrator függvénykód-megkötések](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob tevékenység függvénye

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> A mintakód `Microsoft.Azure.WebJobs.Extensions.Storage` futtatásához telepítenie kell a NuGet csomagot.

A függvény az Azure Functions-kötések néhány speciális funkcióját [ `Binder` ](../functions-dotnet-class-library.md#binding-at-runtime)(azaz a paraméter használatát) használja, de nem kell aggódnia ezek miatt az adatok miatt a forgatókönyv céljából.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A *function.json* `E2_CopyFileToBlob` fájl hasonlóan egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A JavaScript-implementáció az [Azure Storage SDK for Node](https://github.com/Azure/azure-storage-node) segítségével tölti fel a fájlokat az Azure Blob Storage-ba.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon streameli a tartalmat egy blob az azonos nevű a "biztonsági mentések" tárolóban. A visszatérési érték a tárolóba másolt bájtok száma, amelyet ezután az orchestrator függvény az összesített összeg kiszámításához használ.

> [!NOTE]
> Ez egy tökéletes példa arra, hogy az `activityTrigger` I/O-műveleteket függvénybe helyezi át. Nem csak a munka lehet osztani a különböző gépek, de akkor is kap az előnyeit ellenőrzőpontok a haladást. Ha a gazdafolyamat bármilyen okból leáll, tudni adhatja, hogy mely feltöltések fejeződtek be.

## <a name="run-the-sample"></a>Minta futtatása

A vezénylést a következő HTTP POST-kérelem elküldésével indíthatja el.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A `HttpStart` megnevezett függvény csak JSON-formátumú tartalommal működik. Emiatt a `Content-Type: application/json` fejléc szükséges, és a címtár elérési útja JSON-karakterláncként van kódolva. Ezenkívül a HTTP-kódrészlet feltételezi, hogy `host.json` van egy bejegyzés `api/` a fájlban, amely eltávolítja az alapértelmezett előtagot az összes HTTP trigger függvény URL-címéről. Ennek a konfigurációnak a jelölését `host.json` a mintákban lévő fájlban találja.

Ez a HTTP-kérelem elindítja az `E2_BackupSiteContent` orchestratort, és paraméterként adja át a karakterláncot. `D:\home\LogFiles` A válasz egy hivatkozást biztosít a biztonsági mentési művelet állapotának leválasztásához:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Attól függően, hogy hány naplófájl van a függvényalkalmazásban, ez a művelet több percet is igénybe vehet. A legújabb állapotot az előző HTTP `Location` 202 válasz fejlécében található URL-cím lekérdezésével kaphatja meg.

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

Ebben az esetben a függvény még mindig fut. Láthatja az orchestrator-állapotba mentett bemenetet és az utolsó frissített időt. A fejlécértékeket `Location` továbbra is használhatja a lekérdezéshez a befejezéshez. Ha az állapot "Befejezett", a következőhöz hasonló HTTP-válaszérték jelenik meg:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Most láthatja, hogy a vezénylés befejeződött, és körülbelül mennyi időt vett igénybe. A `output` mező értéke is megjelenik, amely azt jelzi, hogy körülbelül 450 KB naplót töltöttek fel.

## <a name="next-steps"></a>További lépések

Ez a minta megmutatta, hogyan kell végrehajtani a fan-out / fan-in mintát. A következő minta bemutatja, hogyan valósítható meg a monitor minta [tartós időzítők](durable-functions-timers.md)használatával.

> [!div class="nextstepaction"]
> [A figyelőminta futtatása](durable-functions-monitor.md)