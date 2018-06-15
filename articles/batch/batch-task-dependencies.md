---
title: Feladat függőségek befejezésekor, más feladatok – Azure Batch-alapú feladatok futtatásához használt |} Microsoft Docs
description: Egyéb feladatok MapReduce stílus és hasonló big Data típusú adatok feldolgozása megvalósításának függő feladatok létrehozása az Azure Batch munkaterhelések.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ba85e075c39251b0b3d7c4b8bc3f8d53a1afadf7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30316817"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>A feladat függőségek más feladatok függő feladatok futtatásához létrehozása

A feladat vagy a feladatok futtatásához csak egy szülő feladat befejeződése után feladat Függőségek meghatározásához. Bizonyos esetekben, ahol feladat függőségek hasznosak a következők:

* MapReduce-stílusú számítási feladatok felhőben.
* Feladatok, amelyek adatfeldolgozási feladatok irányított aciklikus diagramhoz (DAG) jelöl.
* Megjelenítés előtti és utáni megjelenítési folyamatok, ahol minden feladat befejezése után megkezdődhet az a következő feladathoz.
* Bármely más feladat, amelyben alárendelt feladatok függ a felsőbb rétegbeli feladatok kimenete.

Kötegelt feladat függőségek egy vagy több szülő feladat befejezése után a számítási csomópontok végrehajtásra ütemezett feladatok hozhat létre. Például létrehozhat egy feladatot jeleníti meg az egyes különálló, párhuzamos feladatok 3D film keretében. A végleges. feladat – a "merge feladat"--összevonása a megjelenített keretek azokat a teljes movie csak azt követően minden keretek megjelenítése sikeresen megtörtént.

Alapértelmezés szerint a függő feladatok ütemezése a végrehajtás csak a szülő feladat sikeres befejeződése után. Megadhat egy függőségi művelet felülbírálhatja az alapértelmezett viselkedést, és a szülő feladat sikertelensége feladatok futtatásához. Tekintse meg a [függőségi műveletek](#dependency-actions) című szakaszban talál információt.  

Létrehozhat egy az egyhez típusú vagy egy-a-többhöz kapcsolat más feladatok függő feladatok. Ha egy feladat függ-e a tevékenységazonosítók adott tartományon belüli feladatok csoport létrehozása után tartomány függőség is létrehozhat. A több-a-többhöz kapcsolatok létrehozásához három alapvető forgatókönyv kombinálhatja.

## <a name="task-dependencies-with-batch-net"></a>A Batch .NET tevékenység függőségei
Ez a cikk arról lesz szó tevékenység függőségei konfigurálásával használatával a [Batch .NET] [ net_msdn] könyvtárban. Először megmutatjuk, hogyan való [feladatütemezés-függőség engedélyezéséhez](#enable-task-dependencies) a feladatokra, és bemutatják, majd hogyan [feladat konfigurálása függőségekkel rendelkező](#create-dependent-tasks). Azt is leírják, hogyan függő feladatok futtatásához, ha a szülő sikertelen függőség műveletek végrehajtásához. Végezetül arról lesz szó a [függőségi forgatókönyvek](#dependency-scenarios) , amely támogatja a kötegelt.

## <a name="enable-task-dependencies"></a>A feladat függőségek engedélyezése
A kötegelt kérelem feladat függőségek használatához először konfigurálnia kell a feladat feladat függőségek használatára. A Batch .NET engedélyezni a [CloudJob] [ net_cloudjob] úgy, hogy a [UsesTaskDependencies] [ net_usestaskdependencies] tulajdonságot `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

A fenti kódrészletben "batchClient" példánya: a [BatchClient] [ net_batchclient] osztály.

## <a name="create-dependent-tasks"></a>Függő tevékenységek létrehozása
Hozzon létre egy feladatot, amely egy vagy több szülő feladat befejezésére függ, megadhatja, hogy a feladat "függ" más feladatok. A Batch .NET, konfigurálja a [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] tulajdonság egy példányát a [TaskDependencies] [ net_taskdependencies] osztály:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

A kódrészlet egy függő feladat tevékenység azonosítója "Virág" hoz létre. A "Virág" tevékenység "Eső" és "Sun" feladatok függ. A feladat "virág" futtatásához a számítási csomóponton csak a "Eső" és "Sun" sikeresen befejeződött feladatok után ütemezi.

> [!NOTE]
> A feladat sikeresen befejeződött, ha a tekinti a **befejeződött** állapot és a **kilépési kód** van `0`. A Batch .NET, ez azt jelenti, hogy egy [CloudTask][net_cloudtask].[ Állapot] [ net_taskstate] tulajdonság értékének `Completed` és a CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] tulajdonság értéke `0`.
> 
> 

## <a name="dependency-scenarios"></a>A függőségi forgatókönyvek
Alapszintű feladat függőségi három forgatókönyv esetén használhatja az Azure Batch:-az-egyhez, egy-a-többhöz és Feladatazonosítót között függőség. Ezek kombinálható egy negyedik forgatókönyv esetén a több-a-többhöz biztosításához.

| A forgatókönyv&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Példa |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*taskB* függ *taskA* <p/> *taskB* végrehajtásra, amíg nem ütemezi *taskA* sikeresen befejeződött |![Ábra: a feladat egy az egyhez típusú függőség][1] |
|  [One-to-many](#one-to-many) |A *taskC* a *taskA* és a *taskB* tevékenységtől is függ <p/> *taskC* végrehajtásra, amíg nem ütemezi *taskA* és *taskB* sikeresen befejeződött |![Ábra: egy-a-többhöz feladat függőség][2] |
|  [A feladat Azonosítótartományának kezdete](#task-id-range) |*taskD* feladatok számos függ <p/> *taskD* végrehajtásra, amíg a feladatok-azonosítók nem ütemezi *1* keresztül *10* sikeresen befejeződött |![Ábra: Feladat azonosítója tartomány függőség][3] |

> [!TIP]
> Létrehozhat **több-a-többhöz** kapcsolatokat, például ha C, D, E, és F egyes feladatok függ-e A és b feladatok Ez akkor hasznos, ahol az alsóbb rétegbeli feladatok függ-e több felsőbb szintű tevékenység kimenete párhuzamos működésű előfeldolgozási forgatókönyvek például a.
> 
> Ebben a szakaszban szereplő példák, a függő fusson a feladat csak azt követően a szülő feladat sikeresen befejeződik. Ez a viselkedés az alapértelmezett működést egy függő feladat. Egy függő feladat futtatása után egy szülő feladatot nem sikerül, egy függőségi művelet felülbírálhatja az alapértelmezett viselkedést megadásával lehetséges. Tekintse meg a [függőségi műveletek](#dependency-actions) című szakaszban talál információt.

### <a name="one-to-one"></a>-Az-egyhez
Egy az egyhez kapcsolat, a feladat egy szülő tevékenység sikeres befejezésének függ. A függőség létrehozásához adja meg egy egyetlen a [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] statikus metódus be a [DependsOn] [ net_dependson] tulajdonsága [CloudTask][net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>Egy-a-többhöz
Egy-a-többhöz kapcsolat, a feladat több szülő feladat befejezésére függ. A függőség létrehozásához adja meg a feladat azonosítók gyűjteménye a [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] statikus metódus be a [DependsOn] [ net_dependson] tulajdonsága [CloudTask][net_cloudtask].

```csharp
// 'Rain' and 'Sun' don't depend on any other tasks
new CloudTask("Rain", "cmd.exe /c echo Rain"),
new CloudTask("Sun", "cmd.exe /c echo Sun"),

// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
``` 

### <a name="task-id-range"></a>A feladat Azonosítótartományának kezdete
A szülő feladatok széles függ, a feladat függ a találhatók, amelynek azonosítók széles feladatok befejezését.
A függőség létrehozásához adja meg az első és utolsó tevékenység-azonosítók és a [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] statikus metódus be a [DependsOn] [ net_dependson] tulajdonsága [CloudTask][net_cloudtask].

> [!IMPORTANT]
> A függőségek, a tartományba azonosítókat tevékenység azonosítója címtartományok használatakor *kell* egész értékek ábrázolásai karakterlánc lehet.
> 
> A tartomány minden tevékenység a függőséget meg kell felelnie a folyamat sikeres végrehajtása vagy beállítása függőségi művelethez hozzárendelt hibával befejezése **Satisfy**. Tekintse meg a [függőségi műveletek](#dependency-actions) című szakaszban talál információt.
>
>

```csharp
// Tasks 1, 2, and 3 don't depend on any other tasks. Because
// we will be using them for a task range dependency, we must
// specify string representations of integers as their ids.
new CloudTask("1", "cmd.exe /c echo 1"),
new CloudTask("2", "cmd.exe /c echo 2"),
new CloudTask("3", "cmd.exe /c echo 3"),

// Task 4 depends on a range of tasks, 1 through 3
new CloudTask("4", "cmd.exe /c echo 4")
{
    // To use a range of tasks, their ids must be integer values.
    // Note that we pass integers as parameters to TaskIdRange,
    // but their ids (above) are string representations of the ids.
    DependsOn = TaskDependencies.OnIdRange(1, 3)
},
```

## <a name="dependency-actions"></a>A függőségi műveletek

Alapértelmezés szerint egy függő feladat vagy feladatokhoz fut csak egy szülő feladat sikeres befejeződése után. Bizonyos esetekben érdemes lehet a függő feladatok futtatásához, még akkor is, ha a szülő feladat sikertelen. Az alapértelmezett viselkedést felülírhatja egy függőségi művelet megadásával. A függőség művelet határozza meg, hogy egy függő feladat futtatásához, jogosult sikerességét vagy sikertelenségét a szülőtevékenység alapján. 

Tegyük fel, hogy a függő feladatok adatait a fölérendelt feladatok végrehajtása vár. A felsőbb szintű tevékenység sikertelen lesz, ha a függő feladat még lehet tudni futtatni a régebbi adatokat használ. Ebben az esetben egy függőségi művelet is adja meg, hogy a függő tevékenység annak ellenére, hogy a hiba a szülőtevékenység futtatására alkalmas.

A függőség művelet egy kilépési feltétel, a szülő tevékenység alapul. Megadhat egy függőségi művelet a következő kilépési feltételek; a .NET-hez, tekintse meg a [ExitConditions] [ net_exitconditions] osztály további részletek:

- Ha egy előre feldolgozási hiba történik.
- Hiba akkor fordul elő, amikor egy fájl feltöltése. Ha a feladat keresztül megadott kilépési kóddal kilép **exitCodes** vagy **exitCodeRanges**, és ezután ütközik a fájlfeltöltés hiba, a kilépési kóddal megadott művelet élvez elsőbbséget.
- Ha a feladat által megadott kilépési kóddal kilép a **ExitCodes** tulajdonság.
- Ha a feladat, amely a megadott tartományba esik kilépési kóddal kilép a **ExitCodeRanges** tulajdonság.
- Az alapértelmezett esetben, ha a feladat kilép, nem definiált kilépési kóddal **ExitCodes** vagy **ExitCodeRanges**, vagy ha a feladat egy előre feldolgozási hiba miatt kilép és a **PreProcessingError** tulajdonsága nincs beállítva, vagy ha a feladat sikertelen a fájl feltöltése hiba és a **FileUploadError** tulajdonsága nincs beállítva. 

Adja meg, egy függőségi műveletet a .NET, állítsa be a [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] a kilépési feltétel tulajdonság. A **DependencyAction** tulajdonság szükséges egy vagy két értéket:

- Beállítás a **DependencyAction** tulajdonságot **Satisfy** azt jelzi, hogy a függő feladatok futtatását, ha a megadott hiba miatt kilép a szülőtevékenység jogosult.
- Beállítás a **DependencyAction** tulajdonságot **blokk** jelzi, hogy a függő feladatok futtatására alkalmas.

Alapértelmezés szerint a **DependencyAction** tulajdonság **Satisfy** a kilépési kód: 0, és **blokk** az egyéb kilépési feltételeit.

A következő kódot részlet beállítása a **DependencyAction** tulajdonság a szülő feladathoz. A szülőtevékenység előre feldolgozási hiba miatt, vagy a megadott hibakódok kilép, ha a függő tevékenység le van tiltva. A szülőtevékenység más nem nulla hiba miatt kilép, a függő tevékenység futtatására alkalmas.

```csharp
// Task A is the parent task.
new CloudTask("A", "cmd.exe /c echo A")
{
    // Specify exit conditions for task A and their dependency actions.
    ExitConditions = new ExitConditions
    {
        // If task A exits with a pre-processing error, block any downstream tasks (in this example, task B).
        PreProcessingError = new ExitOptions
        {
            DependencyAction = DependencyAction.Block
        },
        // If task A exits with the specified error codes, block any downstream tasks (in this example, task B).
        ExitCodes = new List<ExitCodeMapping>
        {
            new ExitCodeMapping(10, new ExitOptions() { DependencyAction = DependencyAction.Block }),
            new ExitCodeMapping(20, new ExitOptions() { DependencyAction = DependencyAction.Block })
        },
        // If task A succeeds or fails with any other error, any downstream tasks become eligible to run 
        // (in this example, task B).
        Default = new ExitOptions
        {
            DependencyAction = DependencyAction.Satisfy
        }
    }
},
// Task B depends on task A. Whether it becomes eligible to run depends on how task A exits.
new CloudTask("B", "cmd.exe /c echo B")
{
    DependsOn = TaskDependencies.OnId("A")
},
```

## <a name="code-sample"></a>Kódminta
A [TaskDependencies] [ github_taskdependencies] mintaprojektet egyike a [Azure Batch-Kódminták] [ github_samples] a Githubon. A Visual Studio megoldás mutatja be:

- Egy feladat függőség feladat engedélyezése
- Egyéb feladatok függő feladatok létrehozása
- Megtudhatja, hogyan hajtható végre ezeket a feladatokat a számítási csomópontok készlete.

## <a name="next-steps"></a>További lépések
### <a name="application-deployment"></a>Alkalmazás központi telepítése
A [alkalmazáscsomagok](batch-application-packages.md) kötegelt funkciójával egyszerűen mindkét központi telepítése és az alkalmazásokat, amelyek a feladatok végrehajtása a számítási csomópontok verziója.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások telepítése és átmeneti adatokat
Lásd: [alkalmazás telepítését, és átmeneti adatokat a Batch számítási csomópontjain] [ forum_post] az az Azure Batch fórum feladatok futtatásához a csomópontok előkészítése módszerei áttekintését. Az Azure Batch csapattagok egyik írták, a feladás egy vagy több egy jó ismertetése az alkalmazások, a tevékenység bemeneti adatokat és egyéb fájlokat másolni a számítási csomópontok különböző módokon.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_taskdependencies]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_dependson]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.dependson.aspx
[net_exitcode]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.exitcode.aspx
[net_exitconditions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitconditions
[net_exitoptions]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions#Microsoft_Azure_Batch_ExitOptions_DependencyAction
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagramja: egy az egyhez típusú függőség"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Ábra: egy-a-többhöz függőség"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Ábra: feladat azonosítója tartomány függőség"
