---
title: Kiugró/ventilátor-forgatókönyvek Durable Functions-Azure-ban
description: Megtudhatja, hogyan valósítható meg a ventilátorok által kipróbálható forgatókönyv a Azure Functions Durable Functions-bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562190"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/ventilátor – forgatókönyv Durable Functions – Felhőbeli biztonsági mentési példa

A *ventilátor ki-* és bekapcsolása a több függvény egyidejű végrehajtásával, majd az eredmények összesítésének végrehajtásával kapcsolatos mintázatra utal. Ez a cikk egy olyan mintát ismertet, amely [Durable Functionst](durable-functions-overview.md) használ egy ventilátor-vagy ventilátor-kijelentkezési forgatókönyv megvalósításához. A minta egy olyan tartós funkció, amely egy alkalmazás összes vagy néhány oldalának tartalmát az Azure Storage-ba készíti.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ebben a példában a függvények a megadott könyvtár összes fájlját feltöltik rekurzív módon a blob Storage-ba. A feltöltött bájtok teljes számát is megszámolják.

Egyetlen függvényt is írhat, amely mindenről gondoskodik. A futtatásának fő problémája a **méretezhetőség**. Egyetlen függvény végrehajtása csak egyetlen virtuális gépen futtatható, így az átviteli sebességet az adott virtuális gép átviteli sebessége fogja korlátozni. Egy másik probléma a **megbízhatóság**. Ha a művelet félúton, vagy ha a teljes folyamat 5 percnél hosszabb időt vesz igénybe, akkor a biztonsági mentés részben befejezett állapotba kerülhet. Ezt követően újra kell indítani.

A robusztusabb megközelítés két normál függvény írásához szükséges: az egyik a fájlok enumerálása és a fájlnevek hozzáadása egy várólistához, a másik pedig a várólistából, és a fájlok feltöltése a blob Storage-ba. Ez a megközelítés jobb az átviteli sebesség és a megbízhatóság szempontjából, de a várólista üzembe helyezéséhez és kezeléséhez szükséges. Ennél is fontosabb, hogy az **állami felügyelet** és a **koordináció** szempontjából jelentős bonyolultságot jelent, ha többet szeretne tenni, például a feltöltött bájtok teljes számát.

A Durable Functions megközelítéssel az összes említett előnyt nagyon alacsony terheléssel ruházhatja fel.

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E2_BackupSiteContent`: egy [Orchestrator-függvény](durable-functions-bindings.md#orchestration-trigger) , amely meghívja a `E2_GetFileList`t a biztonsági mentéshez szükséges fájlok listájának beszerzéséhez, majd meghívja `E2_CopyFileToBlob` az egyes fájlok biztonsági mentését.
* `E2_GetFileList`: egy [tevékenység-függvény](durable-functions-bindings.md#activity-trigger) , amely egy könyvtár fájljainak listáját adja vissza.
* `E2_CopyFileToBlob`: egy tevékenység-függvény, amely egyetlen fájl biztonsági mentését készíti elő az Azure Blob Storageba.

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent Orchestrator függvény

Ez a Orchestrator-függvény lényegében a következő műveleteket végzi el:

1. Bemeneti paraméterként `rootDirectory` értéket vesz fel.
2. Meghív egy függvényt a fájlok rekurzív listájának beolvasásához a `rootDirectory`alatt.
3. Több párhuzamos függvényt hív meg az egyes fájlok Azure Blob Storageba való feltöltéséhez.
4. Megvárja, amíg az összes feltöltés befejeződik.
5. Az Azure Blob Storageba feltöltött összes bájt összegét adja vissza.

# <a name="c"></a>[C#](#tab/csharp)

Itt látható a Orchestrator függvényt megvalósító kód:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

Figyelje meg a `await Task.WhenAll(tasks);` sort. A `E2_CopyFileToBlob` függvény összes egyedi hívása *nem* várt, ami lehetővé teszi, hogy párhuzamosan fussanak. Ha ezt a tömböt átadja a `Task.WhenAll`nak, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet*be nem fejeződik. Ha már ismeri a .NET-ben a feladatok párhuzamos függvénytárát (TPL), akkor ez nem új Önnek. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

A `Task.WhenAll`tól való várakozás után tudjuk, hogy az összes függvény hívása befejeződött, és visszaadott értékeket értünk vissza. A `E2_CopyFileToBlob` minden hívása a feltöltött bájtok számát adja vissza, így a teljes bájtok számának kiszámítása az összes visszaadott érték együttes hozzáadására szolgál.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A függvény a standard *function. JSON* fájlt használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

Itt látható a Orchestrator függvényt megvalósító kód:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Figyelje meg a `yield context.df.Task.all(tasks);` sort. A `E2_CopyFileToBlob` függvény összes egyedi hívása *nem* lett elindítva, ami lehetővé teszi, hogy a párhuzamosan fussanak. Ha ezt a tömböt átadja a `context.df.Task.all`nak, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet*be nem fejeződik. Ha már ismeri a [`Promise.all`t](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) a JavaScriptben, akkor ez nem új Önnek. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

> [!NOTE]
> Habár a feladatok fogalmi hasonlóságot mutatnak a JavaScript-ígéretekhez, a Orchestrator függvények a `context.df.Task.all` és a `context.df.Task.any` helyett a `Promise.all` és a `Promise.race`t használják a feladatok párhuzamos kezeléséhez.

A `context.df.Task.all`ból való kilépést követően tudjuk, hogy az összes függvény hívása befejeződött, és visszaadott értékeket a számunkra. A `E2_CopyFileToBlob` minden hívása a feltöltött bájtok számát adja vissza, így a teljes bájtok számának kiszámítása az összes visszaadott érték együttes hozzáadására szolgál.

---

### <a name="helper-activity-functions"></a>Segítő tevékenység functions

A segítő tevékenység más mintákhoz hasonlóan csak olyan rendszeres függvények, amelyek a `activityTrigger` trigger kötést használják.

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList Activity függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_GetFileList` a *function. JSON* fájl a következőhöz hasonlóan néz ki:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

És itt a megvalósítás:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A függvény a `readdirp` modult (2. x verzió) használja a címtár struktúrájának rekurzív olvasásához.

---

> [!NOTE]
> Lehet, hogy kíváncsi, miért nem tudta közvetlenül a kódot a Orchestrator függvénybe helyezni. Előfordulhat, hogy ez a Orchestrator functions egyik alapvető szabálya, ami azt eredményezi, hogy soha nem végeznek I/O-t, beleértve a helyi fájlrendszer elérését. További információ: [Orchestrator](durable-functions-code-constraints.md).

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob Activity függvény

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> A mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet csomagot.

A függvény a Azure Functions kötések néhány speciális funkcióját (azaz a [`Binder` paraméter](../functions-dotnet-class-library.md#binding-at-runtime)használatát) használja, de nem kell aggódnia a jelen útmutatóban szereplő adatokkal kapcsolatban.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A `E2_CopyFileToBlob` *function. JSON* fájlja hasonlóan egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

A JavaScript-implementáció az [Azure Storage SDK](https://github.com/Azure/azure-storage-node) -t használja a csomóponthoz a fájlok Azure Blob Storageba való feltöltéséhez.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon továbbítja a tartalmat egy azonos nevű blobba a "Backups" tárolóban. A visszaadott érték a tárterületre másolt bájtok száma, amelyet a Orchestrator függvény használ az összesített összeg kiszámításához.

> [!NOTE]
> Ez egy tökéletes példa arra, hogy az I/O-műveletek egy `activityTrigger` függvénybe legyenek áthelyezve. A munka nem csupán számos különböző gépen van elosztva, de a folyamat ellenőrzőpontjának előnyeit is kihasználhatja. Ha a gazdagép folyamata valamilyen okból leáll, tudja, hogy mely feltöltések lettek végrehajtva.

## <a name="run-the-sample"></a>Minta futtatása

A következő HTTP POST-kérelem elküldésével indíthatja el a koordinálást.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A csak a JSON-formátumú tartalommal rendelkező `HttpStart` függvény. Emiatt a `Content-Type: application/json` fejléc szükséges, és a könyvtár elérési útja JSON-karakterláncként van kódolva. Emellett a HTTP-kódrészlet feltételezi, hogy van egy bejegyzés a `host.json` fájlban, amely eltávolítja az alapértelmezett `api/` előtagot az összes HTTP-trigger függvény URL-címéből. A konfigurációhoz tartozó jelölést a minták `host.json` fájljában találja.

Ez a HTTP-kérelem elindítja a `E2_BackupSiteContent` Orchestrator, és paraméterként továbbítja a karakterláncot `D:\home\LogFiles`. A válasz egy hivatkozást tartalmaz a biztonsági mentési művelet állapotának lekéréséhez:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A függvény alkalmazásban található naplófájlok számától függően a művelet végrehajtása több percet is igénybe vehet. A legutóbbi állapotot a korábbi HTTP 202-válasz `Location` fejlécében található URL-cím lekérdezésével szerezheti be.

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

Ebben az esetben a függvény még fut. Megtekintheti a Orchestrator-állapotba mentett bemenetet és az utolsó frissítés időpontját. Továbbra is használhatja a `Location` fejléc értékeit a lekérdezés befejezéséhez. Ha az állapot "befejezett", a következőhöz hasonló HTTP-válaszüzenet jelenik meg:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

Most láthatja, hogy a folyamat elkészült, és körülbelül mennyi időt vett igénybe. A `output` mező értékét is megtekintheti, ami azt jelzi, hogy a naplók körülbelül 450 KB-át lettek feltöltve.

## <a name="next-steps"></a>További lépések

Ez a példa azt mutatja, hogyan lehet megvalósítani a ventilátor-out/Fan-in mintát. A következő minta bemutatja, hogyan valósítható meg a figyelő minta [tartós időzítők](durable-functions-timers.md)használatával.

> [!div class="nextstepaction"]
> [A figyelő minta futtatása](durable-functions-monitor.md)