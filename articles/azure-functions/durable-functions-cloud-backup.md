---
title: Fan-kimenő/fan-a forgatókönyvek a tartós funkciók – Azure
description: Útmutató a fan-kimenő-ventilátor-a forgatókönyv megvalósításához a tartós funkciók bővítményben az Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e7b7b6af1f41eb0077d8a8605eb2a553c251f8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Fan-kimenő/fan-a forgatókönyv tartós funkciókkal – felhő biztonsági mentési – példa

*Fan-kimenő/fan-lévő* végrehajtása egyidejűleg több funkció, és majd hajt végre bizonyos összesítő eredmények szerkezet hivatkozik. Ez a cikk ismerteti egy használó mintaalkalmazás [tartós funkciók](durable-functions-overview.md) egy fan-a/fan-kimenő forgatókönyv végrehajtásához. A minta egy tartós függvény, amely biztonsági mentését mindegyike vagy némelyike, az alkalmazás forráskódjának webhelytartalom az Azure Storage.

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a tartós funkciók](durable-functions-install.md).
* Fejezze be a [Hello feladatütemezési](durable-functions-sequence.md) forgatókönyv.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Ez a példa a funkciók a megadott könyvtár rekurzív módon alatti összes fájlra feltöltése a blob-tárolóba. Akkor is száma töltődtek bájtok teljes száma.

Akkor lehet írni, amely minden, a gondoskodik egy funkcióval. Be kell futtatni a fő probléma **méretezhetőség**. Egy egyetlen függvény végrehajtása csak futtathatja egy virtuális, úgy, hogy egyetlen virtuális gép átviteli korlátozza az átviteli sebesség. Egy másik probléma **megbízhatóság**. Ha egy hiba félúton keresztül, vagy a teljes folyamat több mint 5 percet vesz igénybe, ha a biztonsági mentés sikertelen lehet, részben befejezett állapotban. Majd kellene újra kell indítani.

Robusztusabb megközelítés való írást két rendszeres lenne: egy szeretné számba venni a fájlokat, és vegye fel a fájlneveket annak a várólistára, és egy másik Ehhez olvassa el a sorból, és a fájlok feltöltése a blob storage. Ez nagyobb átviteli sebesség és a megbízhatóságot, de ehhez szükséges telepíteni és kezelni a várólistát. Ennél is fontosabb, jelentős összetettsége megjelent a **felügyeleti állapot** és **koordinációs** Ha azt szeretné, több teendője, például a jelentés a bájtok száma feltöltve.

A tartós funkciók megközelítés lehetővé teszi az összes az említett előnyei és nagyon alacsony többletterhelést okoznak.

## <a name="the-functions"></a>A Funkciók

Ez a cikk ismerteti a mintaalkalmazást a következő funkciókat:

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

Az alábbi szakaszok ismertetik a konfiguráció és a használt kód a C# parancsfájlok. A Visual Studio fejlesztési kód a cikk végén meg.

## <a name="the-cloud-backup-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A felhő biztonsági mentési vezénylési (Visual Studio Code és az Azure portál mintakód)

A `E2_BackupSiteContent` függvény használja a normál *function.json* az orchestrator függvényekhez.

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

A kód, amely megvalósítja az orchestrator függvény itt látható:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak funkciók v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

Az orchestrator függvény alapvetően a következőket teszi:

1. Tart egy `rootDirectory` egy bemeneti paraméter értékével megegyező.
2. Egy függvényt a fájlok rekurzív listájának `rootDirectory`.
3. Lehetővé teszi az egyes fájlok feltöltése az Azure Blob Storage több párhuzamos függvényhívásokat.
4. Minden feltöltések befejezéséhez vár.
5. Az összeg összes bájt, amely az Azure Blob Storage töltődtek adja vissza.

Figyelje meg a `await Task.WhenAll(tasks);` (C#) és `yield context.df.Task.all(tasks);` (JS) sor. Minden a hívások a `E2_CopyFileToBlob` függvény volt *nem* rá várakozni. Ez a szándékos és párhuzamosan futnak. Ha azt át a feladatok tömbje `Task.WhenAll`, azt vissza egy feladat, amelyik nem fejeződött be *mindaddig, amíg a másolási műveletek befejeződtek*. Ha ismeri az a feladat párhuzamos könyvtár (TPL) a .NET, akkor ez nem új Önnek. Az különbség, hogy ezeket a feladatokat egyszerre több virtuális gépeken volt futnia, és a tartós funkciók bővítmény biztosítja, hogy a végpont végrehajtási rugalmasak folyamatok újrahasznosítása.

Feladatok nagyon hasonlóak a tett JavaScript fogalmát. Azonban `Promise.all` van néhány különbség a `Task.WhenAll`. A fogalom `Task.WhenAll` részeként a keresztül legelterjedtebb a `durable-functions` JavaScript modul, és csak azt.

A várakozás után `Task.WhenAll` (vagy a készítésére `context.df.Task.all`), tudjuk, hogy minden függvényhívások befejeződött, és vissza kellett volna értékek biztonsági másolatot a számunkra. Minden egyes `E2_CopyFileToBlob` bájtok száma töltheti fel, így a teljes összeg bájtszáma kiszámítása kell mindazokat az értékeket együtt visszaadni hozzáadása értéket ad vissza.

## <a name="helper-activity-functions"></a>Tevékenység súgófunkciókat

Tevékenység súgófunkciókat, csakúgy, mint a más minták olyan csak rendszeres funkció, amely használja a `activityTrigger` indítás kötés. Például a *function.json* fájlt `E2_GetFileList` tűnik a következőket:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

És itt végrehajtására:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak funkciók v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

JavaScript végrehajtása `E2_GetFileList` használja a `readdirp` rekurzív módon modult, olvassa el a könyvtárstruktúra.

> [!NOTE]
> Miért, nem csupán helyezték ezt a kódot közvetlenül az orchestrator függvény talán kíváncsi. Ön volt, de ez egyik feladatokat az orchestrator az alapvető szabály, amely, hogy azok soha nem kell tennie i/o, beleértve a helyi fájlrendszer elérése megszűnését.

A *function.json* fájlt `E2_CopyFileToBlob` hasonlóképpen egyszerű:

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

A C# megvalósítási egyben közérthető egyszerű. Történik, használhatja néhány speciális szolgáltatások az Azure Functions kötések (Ez azt jelenti, hogy a használatát a `Binder` paraméter), de nem kell foglalkoznia a bemutató céljából adatai.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (csak funkciók v2)

JavaScript végrehajtása nem rendelkezik hozzáféréssel a `Binder` funkció az Azure Functions szolgáltatásról, ezért a [Azure Storage szolgáltatás SDK csomópont](https://github.com/Azure/azure-storage-node) a történik. Vegye figyelembe, hogy az SDK-t igényel egy `AZURE_STORAGE_CONNECTION_STRING` Alkalmazásbeállítás.

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

A megvalósítás betölti a fájlt a lemezről, és aszinkron módon adatfolyamokat be a "biztonsági mentések" tárolóban azonos nevű blob tartalma. A visszatérési érték a tárhelyet, majd az orchestrator függvény által a teljes összeg kiszámításához másolandó bájtok száma.

> [!NOTE]
> Ez az i/o-műveletek történő áthelyezését tökéletes példája egy `activityTrigger` függvény. Nem csak a munkahelyi terjeszthető sok különböző virtuális gépek között, de is kap az ellenőrzőpontok előnyeit a folyamatban. Ha a gazdagép-folyamat bármely okból lekérdezi, tudja, melyik feltöltések már befejeződött.

## <a name="run-the-sample"></a>Minta futtatása

A vezénylési megkezdheti úgy, hogy a következő HTTP POST-kérelmet küld.

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> A `HttpStart` meghívott függvény csak olyan JSON-formátumú tartalom működik. Emiatt a `Content-Type: application/json` fejléc szükség, és a könyvtár elérési útja JSON karakterláncként van kódolva.

A HTTP-kérelmeit eseményindítók a `E2_BackupSiteContent` orchestrator és a karakterlánc megfelel `D:\home\LogFiles` paraméterként. A válasz tartalmazza a hivatkozásra a biztonsági mentési művelet állapotát:

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

Attól függően, hogy hány naplófájlok az függvény alkalmazás van ez a művelet több percet is beletelhet. A legutóbbi állapota kaphat lekérdezése az URL-címet a `Location` az előző HTTP 202-válasz fejléce.

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

Ebben az esetben a függvény futása. A bemeneti orchestrator állapotát és a legutóbbi frissítési időpontja mentett látni áll. Továbbra is használhatja a `Location` fejléc értékei, és kérdezze le a végrehajtására. A "kész állapotú", láthat egy HTTP válasz érték a következőhöz hasonló:

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

Most láthatja, hogy a vezénylési befejeződött, és körülbelül mennyi idő végrehajtásához szükség volt. Értéket is megjelenik a `output` mező, amely azt jelzi, hogy a naplók körülbelül 450 KB töltődtek.

## <a name="visual-studio-sample-code"></a>A Visual Studio mintakód

Íme egy egyetlen C# fájlban található, a Visual Studio-projekt vezénylési:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>További lépések

Ez a minta azt mutatja, a fan-kimenő/fan-a minta megvalósításához. A következő példa bemutatja, hogyan valósítja meg a figyelő minta segítségével [tartós időzítők](durable-functions-timers.md).

> [!div class="nextstepaction"]
> [A figyelő minta futtatásához](durable-functions-monitor.md)
