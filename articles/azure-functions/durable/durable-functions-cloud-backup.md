---
title: Kiugró/ventilátor-forgatókönyvek Durable Functions-Azure-ban
description: Megtudhatja, hogyan valósítható meg a ventilátorok által kipróbálható forgatókönyv a Azure Functions Durable Functions-bővítményében.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 81c1279670e786ddaa03946869773121a859d3b7
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735238"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-out/ventilátor – forgatókönyv Durable Functions – Felhőbeli biztonsági mentési példa

A *ventilátor ki-* és bekapcsolása a több függvény egyidejű végrehajtásával, majd az eredmények összesítésének végrehajtásával kapcsolatos mintázatra utal. Ez a cikk egy olyan mintát ismertet, amely [Durable Functionst](durable-functions-overview.md) használ egy ventilátor-vagy ventilátor-kijelentkezési forgatókönyv megvalósításához. A minta egy olyan tartós funkció, amely egy alkalmazás összes vagy néhány oldalának tartalmát az Azure Storage-ba készíti.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ebben a példában a függvények a megadott könyvtár összes fájlját feltöltik rekurzív módon a blob Storage-ba. A feltöltött bájtok teljes számát is megszámolják.

Egyetlen függvényt is írhat, amely mindenről gondoskodik. A futtatásának fő problémája a **méretezhetőség**. Egyetlen függvény végrehajtása csak egyetlen virtuális gépen futtatható, így az átviteli sebességet az adott virtuális gép átviteli sebessége fogja korlátozni. Egy másik probléma a **megbízhatóság**. Ha a futási idő félúton, vagy ha a teljes folyamat 5 percnél hosszabb időt vesz igénybe, akkor a biztonsági mentés részben befejezett állapotban is meghiúsulhat. Ezt követően újra kell indítani.

A robusztusabb megközelítés két normál függvény írásához szükséges: az egyik a fájlok enumerálása és a fájlnevek hozzáadása egy várólistához, a másik pedig a várólistából, és a fájlok feltöltése a blob Storage-ba. Ez jobb az átviteli sebesség és a megbízhatóság szempontjából, de a várólista üzembe helyezéséhez és kezeléséhez szükséges. Ennél is fontosabb, hogy az **állami felügyelet** és a **koordináció** szempontjából jelentős bonyolultságot jelent, ha többet szeretne tenni, például a feltöltött bájtok teljes számát.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Ez a Orchestrator-függvény lényegében a következő műveleteket végzi el:

1. Bemeneti paraméterként egy `rootDirectory` értéket vesz fel.
2. Meghív egy függvényt a fájlok rekurzív listájának beolvasásához `rootDirectory`a alatt.
3. Több párhuzamos függvényt hív meg az egyes fájlok Azure Blob Storageba való feltöltéséhez.
4. Megvárja, amíg az összes feltöltés befejeződik.
5. Az Azure Blob Storageba feltöltött összes bájt összegét adja vissza.

Figyelje meg `await Task.WhenAll(tasks);` aC#() `yield context.df.Task.all(tasks);` és a (JavaScript) sorokat. A `E2_CopyFileToBlob` függvény minden egyes hívása *nem* várt. Ez szándékos, hogy lehetővé tegye, hogy párhuzamosan fussanak. Ha a feladatok `Task.WhenAll` tömbjét (C#) vagy `context.df.Task.all` (JavaScript) adja át, egy olyan feladatot kapunk vissza, amely nem fejeződött be, *amíg az összes másolási művelet*be nem fejeződik. Ha már ismeri a (tpl) feladatot a .net-ben vagy [`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) a JavaScriptben, akkor ez nem új Önnek. A különbség az, hogy ezek a feladatok egyszerre több virtuális gépen is futhatnak, és a Durable Functions bővítmény biztosítja, hogy a végpontok közötti végrehajtás rugalmasan dolgozza fel az újrahasznosítás folyamatát.

> [!NOTE]
> Habár a feladatok fogalmi hasonlóságot mutatnak a JavaScript-ígéretekhez `context.df.Task.all` , `context.df.Task.any` a Orchestrator `Promise.all` függvényeknek a `Promise.race` és a helyett a feladatok párhuzamos kell használniuk.

A várttól ( `Task.WhenAll` vagy abból származó `context.df.Task.all`hozamtól) való várakozás után tudjuk, hogy minden függvény hívása befejeződött, és visszaadott értékeket. Minden hívás, `E2_CopyFileToBlob` amely a feltöltött bájtok számát adja vissza, így a teljes bájtok számának kiszámítása az összes visszaadott érték együttes hozzáadására szolgál.

## <a name="helper-activity-functions"></a>Segítő tevékenység functions

A segítő tevékenység más mintákhoz hasonlóan csak olyan rendszeres függvények, amelyek az `activityTrigger` trigger kötését használják. A *function. JSON* fájl `E2_GetFileList` például a következőhöz hasonlóan néz ki:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

És itt a megvalósítás:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

A JavaScript-implementációja `E2_GetFileList` a `readdirp` modul használatával rekurzív módon olvassa be a címtár-struktúrát.

> [!NOTE]
> Lehet, hogy kíváncsi, miért nem tudta közvetlenül a kódot a Orchestrator függvénybe helyezni. Előfordulhat, hogy ez a Orchestrator functions egyik alapvető szabálya, ami azt eredményezi, hogy soha nem végeznek I/O-t, beleértve a helyi fájlrendszer elérését.

A *function. JSON* fájl `E2_CopyFileToBlob` hasonlóan egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A C# megvalósítás is elég egyszerű. Az Azure functions-kötések (azaz a `Binder` paraméter használata) speciális funkcióinak használata történik, de nem kell aggódnia ezen útmutató céljára.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2. x függvény)

A JavaScript-implementáció nem fér hozzá a `Binder` Azure functions funkcióhoz, így a [csomóponthoz tartozó Azure Storage SDK](https://github.com/Azure/azure-storage-node) -val rendelkezik.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon továbbítja a tartalmat egy azonos nevű blobba a "Backups" tárolóban. A visszaadott érték a tárterületre másolt bájtok száma, amelyet a Orchestrator függvény használ az összesített összeg kiszámításához.

> [!NOTE]
> Ez egy tökéletes példa arra, hogy az I/O-műveletek `activityTrigger` egy függvénybe legyenek áthelyezve. A munka nem csupán a különböző virtuális gépek között terjeszthető, de a folyamat ellenőrzőpontjának előnyeit is kihasználhatja. Ha a gazdagép folyamata valamilyen okból leáll, tudja, hogy mely feltöltések lettek végrehajtva.

## <a name="run-the-sample"></a>Minta futtatása

A következő HTTP POST-kérelem elküldésével indíthatja el a koordinálást.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A `HttpStart` meghívó függvény csak JSON-formátumú tartalommal működik. Emiatt a `Content-Type: application/json` fejléc megadása kötelező, a könyvtár elérési útja pedig JSON-karakterláncként van kódolva. Emellett a http-kódrészlet feltételezi, hogy van egy bejegyzés `host.json` a fájlban, amely eltávolítja `api/` az alapértelmezett előtagot a http-trigger összes funkciójának URL-címéről. A konfigurációhoz `host.json` tartozó jelölést a mintákban található fájlban találja.

Ez a HTTP-kérelem `E2_BackupSiteContent` elindítja a Orchestrator, és `D:\home\LogFiles` paraméterként továbbítja a karakterláncot. A válasz egy hivatkozást tartalmaz a biztonsági mentési művelet állapotának lekéréséhez:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

A függvény alkalmazásban található naplófájlok számától függően a művelet végrehajtása több percet is igénybe vehet. A legutóbbi állapotot a korábbi http 202-válasz `Location` fejlécében lévő URL-cím lekérdezésével érheti el.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

Ebben az esetben a függvény még fut. Megtekintheti a Orchestrator-állapotba mentett bemenetet és az utolsó frissítés időpontját. Továbbra is használhatja a `Location` fejléc értékeit a lekérdezés befejezéséhez. Ha az állapot "befejezett", a következőhöz hasonló HTTP-válaszüzenet jelenik meg:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Most láthatja, hogy a folyamat elkészült, és körülbelül mennyi időt vett igénybe. Megtekintheti a `output` mező értékét is, amely azt jelzi, hogy a rendszer körülbelül 450 kb-ot töltött fel a naplókban.

## <a name="visual-studio-sample-code"></a>Visual Studio-mintakód

Íme egy Visual Studio-projekt egyetlen C# fájlja:

> [!NOTE]
> Az alábbi mintakód futtatásához telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Storage` Nuget csomagot.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>További lépések

Ez a példa azt mutatja, hogyan lehet megvalósítani a ventilátor-out/Fan-in mintát. A következő minta bemutatja, hogyan valósítható meg a figyelő minta [tartós időzítők](durable-functions-timers.md)használatával.

> [!div class="nextstepaction"]
> [A figyelő minta futtatása](durable-functions-monitor.md)