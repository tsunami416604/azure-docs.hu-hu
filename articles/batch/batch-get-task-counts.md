---
title: A feladatok előrehaladásának figyeléséhez állam - Azure kötegelt feladatok leltár szerint |} Microsoft Docs
description: A feladatok előrehaladásának figyeléséhez az beszerzése feladat száma művelet feladat feladatok száma meghívásával. Egy aktív, futó, és befejezett feladatok, és rendelkezik a sikeres és sikertelen feladatok száma kérheti le.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 08/02/2017
ms.author: danlep
ms.openlocfilehash: bc112ed5b481560362962d6b550d336de6b3d9b4
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30312643"
---
# <a name="count-tasks-by-state-to-monitor-a-jobs-progress-preview"></a>Egy feladat előrehaladást (előzetes verzió) állapota alapján feladatok száma

Az Azure Batch szolgáltatás hatékonyan figyelésére a feladat előrehaladását a feladatok futása. Hívása a [beolvasása feladat száma] [ rest_get_task_counts] hány feladatok egy aktív, futó vagy befejezett állapotban vannak, és hány rendelkezik a művelet sikeres vagy sikertelen volt. Az egyes feladatok száma alapján, több egyszerűen jelenik meg a feladat előrehaladását a felhasználónak, vagy nem várt késedelmeket vagy hibák, amelyek befolyásolhatják a feladatot.

> [!IMPORTANT]
> A feladat száma beolvasása művelet jelenleg előzetes verzióban érhetők, és még nem érhető el az Azure Government, Azure Kína és németországi Azure. 
>
>

## <a name="how-tasks-are-counted"></a>Hogyan feladatok számítanak.

A feladat száma beolvasása művelet visszajelzését feladatok állam, az alábbiak szerint:

- A feladatütemezés akkor számít **aktív** amikor aszinkron, és képes futni, de jelenleg nincs hozzárendelve a számítási csomópont. Egy feladat is számít **aktív** Ha még nem fejeződött be szülő feladat függ. Feladat függőségekkel kapcsolatos további információkért lásd: [feladat függőségek más feladatok függő feladatok futtatásához létrehozása](batch-task-dependencies.md). 
- A feladatütemezés akkor számít **futtató** mikor van rendelve egy számítási csomópont, de még nem fejeződött be. A feladatütemezés akkor számít **futtató** állapotában esetén vagy `preparing` vagy `running`, ahogy azt a [feladat adatainak beolvasása] [ rest_get_task] műveletet.
- A feladatütemezés akkor számít **befejeződött** már nem futtatására alkalmas. Egy feladat számít **befejeződött** még általában fejeződött be sikeresen, vagy sikertelenül befejeződött, és a is kimerítette az Újrapróbálkozási korlát. 

A feladat száma beolvasása műveletet is jelent, hány feladatok sikeres vagy sikertelen volt. Kötegelt határozza meg, hogy a feladat végrehajtása sikeres vagy ellenőrzése sikertelen a **eredmény** tulajdonsága [executionInfo] [https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task#executionInfo] tulajdonság:

    - A feladatütemezés akkor számít **sikeres** Ha a feladat a végrehajtás eredménye `success`.
    - A feladatütemezés akkor számít **sikertelen** Ha a feladat a végrehajtás eredménye `failure`.

További információ a feladat állapota: [feladat adatainak beolvasása][rest_get_task].

A következő .NET példakód bemutatja, hogyan feladat számok állapot beolvasása: 

```csharp
var taskCounts = await batchClient.JobOperations.GetTaskCountsAsync("job-1");

Console.WriteLine("Task count in active state: {0}", taskCounts.Active);
Console.WriteLine("Task count in preparing or running state: {0}", taskCounts.Running);
Console.WriteLine("Task count in completed state: {0}", taskCounts.Completed);
Console.WriteLine("Succeeded task count: {0}", taskCounts.Succeeded);
Console.WriteLine("Failed task count: {0}", taskCounts.Failed);
Console.WriteLine("ValidationStatus: {0}", taskCounts.ValidationStatus);
```

> [!NOTE]
> A többi hasonló mintát és egyéb támogatott nyelvek segítségével feladat számát feladat beolvasása. 
> 
> 

## <a name="consistency-checking-for-task-counts"></a>Konzisztencia-ellenőrzés a feladat összesítése

A Batch szolgáltatás feladat számát összesíti az adatokat gyűjt több részből aszinkron elosztott rendszer által. Győződjön meg arról, hogy helyesek a feladat számát, kötegelt biztosít a állapot összesítése további érvényesítése érdekében végezzen konzisztencia ellenőrzi a rendszer több összetevő ellen. Kötegelt ezek konzisztencia-ellenőrzéseket is végez, mindaddig, amíg a feladat kevesebb, mint 200 000 feladatok szerepelnek. Az, hogy a konzisztencia-ellenőrzés hibákat talál valószínű esetben kötegelt javítja a konzisztencia-ellenőrzés eredménye alapján feladatok száma Get művelet eredménye. A konzisztencia-ellenőrzés egy extra mértéket győződjön meg arról, hogy az ügyfelek, akik az beszerzése feladat száma művelet támaszkodnak beolvasása a megoldáshoz szükséges megfelelő információt.

A **validationStatus** a válaszban a tulajdonság azt jelzi, hogy kötegelt hajtott végre a konzisztencia-ellenőrzést. Ha a köteg nem tudta ellenőrizni állapot csökkenti a tényleges állapotok, a rendszer lefoglalta a **validationStatus** tulajdonsága `unvalidated`. Teljesítményének javítására szolgál, kötegelt nem hajt végre a konzisztencia-ellenőrzést, ha a feldolgozás tartalmaz a több mint 200 000 feladatokat, ezért a **validationStatus** tulajdonság állítható be `unvalidated` ebben az esetben. A feladatok száma azonban nem feltétlenül megfelelő ebben az esetben, még a nagyon kevés adatvesztés nagyon valószínű. 

A feladatok állapota, ha az összesítési folyamat néhány másodpercen belül a módosítási dolgozza fel. A feladat száma beolvasása művelet adott időszakon belül a frissített feladat számát jeleníti meg. Azonban az összesítési csővezeték gyorsítótárbeli sikertelen keresések egy feladat állapotának változását, ha majd ez a változás nincs regisztrálva csak a következő ellenőrzési fázisban. Ebben az időszakban feladat száma miatt a hiányzó esemény némileg pontatlan lehet, de azok a következő érvényesítési fázis a kijavítására sor.

## <a name="best-practices-for-counting-a-jobs-tasks"></a>A feladat tevékenységeit számbavételi ajánlott eljárásai

A feladat száma beolvasása művelet hívása módja a leghatékonyabb állapot szerint a feladat tevékenységeit alapvető számát adja vissza. Kötegelt verziójú 2017-06-01.5.1 használatakor javasoljuk írása vagy frissítésekor. a kódot, és használja a Get feladat száma.

Az beszerzése feladat száma művelet nem érhető el a Batch szolgáltatás verziókban rendszernél korábbi 2017-06-01.5.1. Ha a szolgáltatás egy régebbi verzióját használja, majd lekérdezéssel lista egy feladatot a feladatok száma helyette. További információkért lásd: [létrehozás lekérdezések listáját a Batch-erőforrások hatékony](batch-efficient-list-queries.md).

## <a name="next-steps"></a>További lépések

* A Batch szolgáltatás fogalmairól és funkcióiról további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md) talál. A cikk ismerteti az elsődleges kötegelt erőforrások, például készletek, a számítási csomópontokat, a feladatok és a feladatokat, és a szolgáltatások áttekintését.
* Megismerheti a Batch-kompatibilis alkalmazások [Batch .NET ügyfélkönyvtárral](batch-dotnet-get-started.md) vagy [Python](batch-python-tutorial.md) segítségével való fejlesztésének alapjait. A bevezető cikkeiben végigvezeti egy működő alkalmazást, amely a Batch szolgáltatás használ a munkaterhelés több számítási csomóponton.


[rest_get_task_counts]: https://docs.microsoft.com/rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_task]: https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task
[rest_list_tasks]: https://docs.microsoft.com/rest/api/batchservice/list-the-tasks-associated-with-a-job
