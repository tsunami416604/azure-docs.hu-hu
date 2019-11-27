---
title: Kiugró/ventilátor-forgatókönyvek Durable Functions-Azure-ban
description: Megtudhatja, hogyan valósítható meg a ventilátorok által kipróbálható forgatókönyv a Azure Functions Durable Functions-bővítményében.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: a87a4edd544c2f7d8ff9c6415df2f2dda125f2bf
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232997"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/ventilátor – forgatókönyv Durable Functions – Felhőbeli biztonsági mentési példa

A *ventilátor ki-* és bekapcsolása a több függvény egyidejű végrehajtásával, majd az eredmények összesítésének végrehajtásával kapcsolatos mintázatra utal. Ez a cikk egy olyan mintát ismertet, amely [Durable Functionst](durable-functions-overview.md) használ egy ventilátor-vagy ventilátor-kijelentkezési forgatókönyv megvalósításához. A minta egy olyan tartós funkció, amely egy alkalmazás összes vagy néhány oldalának tartalmát az Azure Storage-ba készíti.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ebben a példában a függvények a megadott könyvtár összes fájlját feltöltik rekurzív módon a blob Storage-ba. A feltöltött bájtok teljes számát is megszámolják.

Egyetlen függvényt is írhat, amely mindenről gondoskodik. A futtatásának fő problémája a **méretezhetőség**. Egyetlen függvény végrehajtása csak egyetlen virtuális gépen futtatható, így az átviteli sebességet az adott virtuális gép átviteli sebessége fogja korlátozni. Egy másik probléma a **megbízhatóság**. Ha a művelet félúton, vagy ha a teljes folyamat 5 percnél hosszabb időt vesz igénybe, akkor a biztonsági mentés részben befejezett állapotba kerülhet. Ezt követően újra kell indítani.

A robusztusabb megközelítés két normál függvény írásához szükséges: az egyik a fájlok enumerálása és a fájlnevek hozzáadása egy várólistához, a másik pedig a várólistából, és a fájlok feltöltése a blob Storage-ba. Ez a megközelítés jobb az átviteli sebesség és a megbízhatóság szempontjából, de a várólista üzembe helyezéséhez és kezeléséhez szükséges. Ennél is fontosabb, hogy az **állami felügyelet** és a **koordináció** szempontjából jelentős bonyolultságot jelent, ha többet szeretne tenni, például a feltöltött bájtok teljes számát.

A Durable Functions megközelítéssel az összes említett előnyt nagyon alacsony terheléssel ruházhatja fel.

## <a name="the-functions"></a>A függvények

Ez a cikk a minta alkalmazás következő funkcióit ismerteti:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

A következő szakasz ismerteti a C# parancsfájlok futtatásához használt konfigurációt és kódot. A Visual Studio-fejlesztés kódja a cikk végén látható.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A Cloud Backup-előkészítés (Visual Studio Code és Azure Portal mintakód)

A `E2_BackupSiteContent` függvény a standard *function. JSON* fájlt használja a Orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Itt látható a Orchestrator függvényt megvalósító kód:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Ez a Orchestrator-függvény lényegében a következő műveleteket végzi el:

1. Bemeneti paraméterként `rootDirectory` értéket vesz fel.
2. Meghív egy függvényt a fájlok rekurzív listájának beolvasásához a `rootDirectory`alatt.
3. Több párhuzamos függvényt hív meg az egyes fájlok Azure Blob Storageba való feltöltéséhez.
4. Megvárja, amíg az összes feltöltés befejeződik.
5. Az Azure Blob Storageba feltöltött összes bájt összegét adja vissza.

Figyelje meg a `await Task.WhenAll(tasks);`C#() és a `yield context.df.Task.all(tasks);` (JavaScript) sorokat. A `E2_CopyFileToBlob` függvény összes egyedi hívása *nem* várt, ami lehetővé teszi, hogy párhuzamosan fussanak. Ha a feladatok tömbjét `Task.WhenAll` (C#) vagy `context.df.Task.all` (JavaScript) értékre adja át, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet*be nem fejeződik. Ha ismeri a .NET-ben vagy a [`Promise.all`ban](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) a JavaScriptben a feladat Parallel Library-t (tpl), akkor ez nem új az Ön számára. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

> [!NOTE]
> Habár a feladatok fogalmi hasonlóságot mutatnak a JavaScript-ígéretekhez, a Orchestrator függvények a `context.df.Task.all` és a `context.df.Task.any` helyett a `Promise.all` és a `Promise.race`t használják a feladatok párhuzamos kezeléséhez.

A `Task.WhenAll`tól való várakozást követően (vagy a `context.df.Task.all`ból való betöltés után) tudjuk, hogy az összes függvény hívása befejeződött, és visszaadott értékeket vissza hozzánk. A `E2_CopyFileToBlob` minden hívása a feltöltött bájtok számát adja vissza, így a teljes bájtok számának kiszámítása az összes visszaadott érték együttes hozzáadására szolgál.

## <a name="helper-activity-functions"></a>Segítő tevékenység functions

A segítő tevékenység más mintákhoz hasonlóan csak olyan rendszeres függvények, amelyek a `activityTrigger` trigger kötést használják. A `E2_GetFileList` a *function. JSON* fájl például a következőhöz hasonlóan néz ki:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

És itt a megvalósítás:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A `E2_GetFileList` JavaScript-implementációja a `readdirp` modul használatával rekurzív módon olvassa be a címtár-struktúrát.

> [!NOTE]
> Lehet, hogy kíváncsi, miért nem tudta közvetlenül a kódot a Orchestrator függvénybe helyezni. Előfordulhat, hogy ez a Orchestrator functions egyik alapvető szabálya, ami azt eredményezi, hogy soha nem végeznek I/O-t, beleértve a helyi fájlrendszer elérését.

A `E2_CopyFileToBlob` *function. JSON* fájlja hasonlóan egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A C# megvalósítás is egyszerű. A Azure Functions kötések (azaz a `Binder` paraméter használata) speciális funkcióinak használata történik, de nem kell aggódnia a jelen útmutatóban szereplő adatokkal kapcsolatban.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-20-only"></a>JavaScript (csak functions 2,0)

A JavaScript-implementáció nem fér hozzá a Azure Functions `Binder` szolgáltatásához, ezért a [csomóponthoz tartozó Azure Storage SDK](https://github.com/Azure/azure-storage-node) -t veszi igénybe.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon továbbítja a tartalmat egy azonos nevű blobba a "Backups" tárolóban. A visszaadott érték a tárterületre másolt bájtok száma, amelyet a Orchestrator függvény használ az összesített összeg kiszámításához.

> [!NOTE]
> Ez egy tökéletes példa arra, hogy az I/O-műveletek egy `activityTrigger` függvénybe legyenek áthelyezve. A munka nem csupán a különböző virtuális gépek között terjeszthető, de a folyamat ellenőrzőpontjának előnyeit is kihasználhatja. Ha a gazdagép folyamata valamilyen okból leáll, tudja, hogy mely feltöltések lettek végrehajtva.

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

## <a name="visual-studio-sample-code"></a>Visual Studio-mintakód

Íme egy Visual Studio-projekt egyetlen C# fájlja:

> [!NOTE]
> Az alábbi mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet csomagot.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>Következő lépések

Ez a példa azt mutatja, hogyan lehet megvalósítani a ventilátor-out/Fan-in mintát. A következő minta bemutatja, hogyan valósítható meg a figyelő minta [tartós időzítők](durable-functions-timers.md)használatával.

> [!div class="nextstepaction"]
> [A figyelő minta futtatása](durable-functions-monitor.md)