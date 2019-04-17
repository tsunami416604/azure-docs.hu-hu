---
title: Fan-kimenő/fan-alatt található forgatókönyvek Durable Functions – Azure
description: Megtudhatja, hogyan fan-kimenő ventilátor-be a forgatókönyv megvalósításához a Durable Functions bővítmény az Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0bef5f1b64ec9f322070ba5c36cab138c7327da2
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608503"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-kimenő/fan-alatt forgatókönyv Durable Functions – felhőalapú biztonsági mentési példa

*Fan-kimenő/fan-alatt* a minta több függvények egyidejű végrehajtása, és majd az eredmények alapján hajt végre bizonyos összesítési hivatkozik. Ez a cikk azt ismerteti, használó minta [Durable Functions](durable-functions-overview.md) fan-a/fan-kimenő forgatókönyv megvalósításához. A minta, akkor egy tartós függvény, amely egy része az alkalmazás tartalmát vagy Azure Storage-bA.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ebben a példában a functions összes fájlt a megadott könyvtár rekurzív feltöltése a blob storage-bA. Akkor is számítanak által feltöltött bájtok teljes száma.

Egyetlen függvény, amely gondoskodik mindenről írása lehetőség. A fő probléma, be kell futtatni, **méretezhetőség**. Egy adott függvény végrehajtása csak futtathatja egyetlen virtuális Gépet, így az átviteli sebesség az átviteli sebességet, hogy egyetlen virtuális gép korlátozzuk. Egy másik probléma **megbízhatóság**. Ha egy hiba midway keresztül, vagy a teljes folyamatot a több mint 5 percet vesz igénybe, ha a biztonsági mentés részben végrehajtva állapotban volt sikertelen. Ezután kellene újra kell indítani.

Két normál funkciók írhat egy robusztusabb megközelítés lenne: egy lenne számba venni a fájlokat, és vegye fel egy üzenetsorba, és egy másik ehhez olvasása az üzenetsorból, és a fájlok feltöltése a blob storage. Ez nagyobb átviteli sebesség és a megbízhatóságot, de azt kell kiépítenie és kezelnie egy üzenetsorba. Ami még fontosabb, jelentős összetettségét, hogy vezetett be **felügyeleti állapot** és **koordináció** szeretne több teendője, ha például a jelentés bájtok teljes száma feltöltött.

A tartós függvények megközelítést biztosít említett előnyöket, a nagyon alacsony többletterhelést okoznak.

## <a name="the-functions"></a>Az funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Az alábbi szakaszok ismertetik a konfiguráció és a használt kódot C# részig. A Visual Studio fejlesztési kód a cikk végén található meg.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A felhőalapú biztonsági mentési vezénylési (Visual Studio Code és az Azure portal minta kód)

A `E2_BackupSiteContent` a funkció a standard *function.json* az orchestrator-funkciók.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

Íme a kódot, amely megvalósítja az orchestrator-funkció:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Ez a függvény az orchestrator lényegében a következőket teszi:

1. Vesz igénybe egy `rootDirectory` érték bemeneti paraméterként.
2. Meghív egy függvényt a fájlok listáját a rekurzív `rootDirectory`.
3. Minden fájl feltöltése az Azure Blob Storage-bA több párhuzamos függvény-hívást hajt végre.
4. Megvárja, amíg befejeződik az összes feltöltések.
5. Az összeg, amely az Azure Blob Storage feltöltött összes bájt adja vissza.

Figyelje meg a `await Task.WhenAll(tasks);` (C#) és `yield context.df.Task.all(tasks);` (JavaScript) sorokat. Minden az egyes hívások a `E2_CopyFileToBlob` függvény is *nem* várni. Ez szándékosan így, párhuzamos futtatását engedélyezze. Amikor adjuk át a feladatok a tömb `Task.WhenAll` (C#) vagy `context.df.Task.all` (JavaScript), kapunk vissza egy feladatot, amely nem befejezéséhez *mindaddig, amíg az összes másolási műveletek végre lettek hajtva*. Ha ismeri az a tevékenység párhuzamos könyvtár (TPL) a .NET-ben vagy [ `Promise.all` ](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) javascriptben, majd ez nem ismeri még. A különbség az, hogy ezeket a feladatokat több virtuális gépen egyidejűleg fut., és a Durable Functions bővítmény biztosítja, hogy a teljes körű végrehajtási képes legyen ellenállni a folyamat újrahasznosítását.

> [!NOTE]
> Bár a feladatok JavaScript ígéreteinket tárolókéhoz hasonló, az orchestrator funkciók használjon `context.df.Task.all` és `context.df.Task.any` helyett `Promise.all` és `Promise.race` feladat ezerszer kezeléséhez.

A várakozás után `Task.WhenAll` (vagy az elsőbbséget adó `context.df.Task.all`), tudjuk, hogy minden függvényhívások befejezése, és vissza értékek biztonsági velünk a kapcsolatot. Egyes hívások `E2_CopyFileToBlob` feltöltött bájtok száma, így teljes bájtszáma összeg kiszámításának pár hozzáadása mindazokat az értékeket együtt vissza értéket ad vissza.

## <a name="helper-activity-functions"></a>Segédfüggvények tevékenység

Tevékenység segédfüggvények más minták, mint a rendszer csak normál funkciók, amelyek használják a `activityTrigger` kötés aktiválásához. Ha például a *function.json* fájlt `E2_GetFileList` a következőképpen néz:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

Ez pedig végrehajtása:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

JavaScript végrehajtása `E2_GetFileList` használja a `readdirp` rekurzív módon modult, olvassa el a könyvtárstruktúra.

> [!NOTE]
> Felmerülhet, miért nem csupán mindössze ezt a kódot közvetlenül az orchestrator-funkció. Próbálja meg, de ez megtörnék egyik alapvető szabály az orchestrator-függvény, amely, hogy azok soha nem kell tennie i/o, így helyi fájlrendszer elérése.

A *function.json* fájlt `E2_CopyFileToBlob` hasonlóképpen egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A C# megvalósítása nagyon is viszonylag egyszerű. Történik, néhány speciális funkcióját az Azure Functions-bindings (használatát, a `Binder` paraméter), de nem kell aggódnia a ezeket az adatokat, ez a bemutató céljából.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (csak 2.x függvények)

JavaScript végrehajtása nem rendelkezik hozzáféréssel a `Binder` funkció az Azure Functions, ezért a [Azure Storage SDK a Node](https://github.com/Azure/azure-storage-node) lép a helyére.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon adatfolyamok tartalmát, az azonos nevű, a "biztonsági mentések" tárolóban lévő blobok. A visszaadott érték másolva, majd használja az orchestrator-funkció az összesített összeg számítási bájtok száma.

> [!NOTE]
> Ez a tökéletes áthelyezi az i/o-műveletek, például egy `activityTrigger` függvény. Nem csak a munkahelyi szét lehetnek osztva több különböző virtuális gépet, de érhet el az ellenőrzőpontok előnyeit a folyamat állapotát. Ha a gazdagép lekéri folyamatot bármilyen okból, tudja, hogy melyik feltöltések már végrehajtotta.

## <a name="run-the-sample"></a>Minta futtatása

A vezénylési elkezdheti a következő HTTP POST-kérelem küldésével.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A `HttpStart` meghívott függvény csak a JSON-formátumú tartalommal működik. Ebből kifolyólag a `Content-Type: application/json` fejlécét kötelező megadni, és a egy JSON-karakterlánc kódolása a könyvtár elérési útja. Továbbá, HTTP a kódtöredék azt feltételezi, hogy a bejegyzés a `host.json` fájlt, amely eltávolítja az alapértelmezett `api/` előtag a HTTP eseményindító függvény URL-címekhez. Ezt a konfigurációt a jelölőnyelvi megtalálja a `host.json` a mintákban található fájl.

Ez a HTTP-kérés eseményindítók a `E2_BackupSiteContent` orchestrator, és átadja a karakterlánc `D:\home\LogFiles` paraméterként. A válasz egy hivatkozást kínál a biztonsági mentési művelet állapotának lekérése:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Hány naplófájlok rendelkezik a függvényalkalmazásban, attól függően a művelet több percet is igénybe. A legutóbbi állapot megkaphassa URL-cím lekérdezése a `Location` az előző HTTP 202-válasz fejléce.

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

Ebben az esetben a függvény futása. Ön láthatja a bemenet, amely az orchestrator-állapot és a legutóbbi frissítés ideje mentése megtörtént. Használja továbbra is a `Location` fejlécértékeket befejezési lekérdezésére. A "Befejezve", amikor egy HTTP válasz a következőhöz hasonló érték jelenik meg:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Most láthatja, hogy a vezénylési befejeződött, és körülbelül mennyi ideig tartott végrehajtásához. Egy értéket is megjelenik a `output` mező, amely azt jelzi, hogy mintegy 450 KB méretű naplók feltöltve.

## <a name="visual-studio-sample-code"></a>A Visual Studio-mintakód

Az orchestration-fájlként egyetlen C# Visual Studio-projektet a következő:

> [!NOTE]
> Telepítenie kell a `Microsoft.Azure.WebJobs.Extensions.Storage` Nuget-csomagot az alábbi mintakód futtatásához.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>További lépések

Ez a minta azt mutatja, a fan-kimenő/fan-a minta megvalósítása. A következő példa bemutatja, hogyan megvalósításához, a figyelő a minta használatával [tartós időzítők](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [A figyelő minta futtatása](durable-functions-monitor.md)
