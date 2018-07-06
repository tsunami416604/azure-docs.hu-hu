---
title: Feladat-függőségek használatára más feladatok – Azure Batch megvalósításának alapján feladatok |} A Microsoft Docs
description: Egyéb feladatok MapReduce stílus és hasonló big Data típusú adatok feldolgozásához megvalósításának függő feladatok létrehozása az Azure Batch számítási feladatokat.
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
ms.openlocfilehash: 6a9b44ed56774466bae2f0f5d48b5e012382721b
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865233"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Hozzon létre a tevékenységfüggőségek, amely más tevékenységektől függő feladatok

A feladat vagy a feladatok futtatásához csak egy fölérendelt tevékenység befejezése után tevékenységfüggőségek határozhatja meg. Egyes forgatókönyvekben, ahol tevékenységfüggőségek hasznosak a következők:

* A felhőalapú számítási feladatokat a MapReduce-stílusú.
* Feladatok, amelyek adatfeldolgozási feladatok egy irányított aciklikus diagramhoz (DAG) jelöl.
* Üzem előtti renderelési és utáni renderelési folyamatok, ahol minden feladatot kell végeznie a következő feladat megkezdése előtt.
* Bármely egyéb feladat, amelyben feladat-végrehajtást függő felsőbb szintű feladatok kimenete.

A Batch tevékenységfüggőségekkel létrehozhat egy vagy több szülő feladat befejezése után a számítási csomópontokon végrehajtásra ütemezett feladatok. Ha például egy feladatot, amely egy 3D film külön, a párhuzamos feladatokat az egyes keret rendereli is létrehozhat. A végső. feladat – az "merge feladat"--összevonása a megjelenített keretek a teljes filmben csak azt követően minden keretek megjelenített sikeresen megtörtént.

Alapértelmezés szerint a függő tevékenységek ütemezve lesznek a végrehajtási csak akkor, ha a szülő feladat sikeresen befejeződött. Megadhat egy függőségi művelet felülírhatja az alapértelmezett viselkedést, és feladatokat futtathat, ha a szülő feladat meghiúsul. Tekintse meg a [függőségi műveletek](#dependency-actions) című szakasz részletezi.  

Létrehozhat egy-az-egyhez vagy egy-a-többhöz kapcsolat más feladatok függő feladatok. Ahol egy feladatot a tevékenységazonosítók adott tartományon belül feladatok csoport elvégzésétől függ tartomány függőséget is létrehozhat. Ezek három alapvető forgatókönyv létrehozása több-a-többhöz kapcsolatok kombinálhatók.

## <a name="task-dependencies-with-batch-net"></a>Tevékenységfüggőségeket a Batch .NET használatával
Ebben a cikkben bemutatjuk, hogyan lehet beállítani tevékenységfüggőségek a [Batch .NET] [ net_msdn] könyvtár. Először bemutatják, hogyan való [tevékenységfüggőség engedélyezése](#enable-task-dependencies) a feladatok a, és ezután bemutatja hogyan [konfigurálhat függőségekkel rendelkező](#create-dependent-tasks). Azt is ismertetjük, hogyan függő feladatok futtatásához, ha a szülő sikertelen függőségi művelet megadása. Végül bemutatjuk a [függőségi esetekben](#dependency-scenarios) , amely a Batch támogatja.

## <a name="enable-task-dependencies"></a>Tevékenységfüggőségek engedélyezése
A Batch-alkalmazás tevékenységfüggőségek használatához először konfigurálnia kell a feladat feladat-függőségek használatára. A Batch .NET-ben, engedélyezi azt az a [CloudJob] [ net_cloudjob] beállításával a [UsesTaskDependencies] [ net_usestaskdependencies] tulajdonságot `true`:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Az előző kódrészletben "batchClient" egy példányát a [BatchClient] [ net_batchclient] osztály.

## <a name="create-dependent-tasks"></a>Függő tevékenységek létrehozása
Hozzon létre egy feladatot, amely egy vagy több szülő tevékenységek elvégzésétől függ, megadhatja, hogy a feladat "attól függ," a más feladatokat. A Batch .NET-ben, konfigurálja a [CloudTask][net_cloudtask].[ DependsOn] [ net_dependson] tulajdonságot, amelyben egy példányát a [TaskDependencies] [ net_taskdependencies] osztály:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ez a kódrészlet létrehoz egy függő feladat "Virágok" Azonosítójú feladatot tartalmazó. A "Virágok" feladat "Esőfelhő" és "Sun" feladatok függ. A feladat "virágok" csak "Esőfelhő" és "Sun" sikeresen lefutott-e feladatok után egy számítási csomóponton futtatandó lesz ütemezve.

> [!NOTE]
> Alapértelmezés szerint a feladat sikeresen befejeződött, amikor tekinthető a **befejeződött** állapota és a hozzá tartozó **kilépési kód** van `0`. A Batch .NET-ben, ez azt jelenti, hogy egy [CloudTask][net_cloudtask].[ Állapot] [ net_taskstate] tulajdonságának értéke `Completed` és a CloudTask [TaskExecutionInformation][net_taskexecutioninformation].[ ExitCode] [ net_exitcode] tulajdonság értéke `0`. A módosíthatja, olvassa el a [függőségi műveletek](#dependency-actions) szakaszban.
> 
> 

## <a name="dependency-scenarios"></a>Függőségi esetekben
Három is használhatja az Azure Batch alapvető feladat függőségi forgatókönyv:-az-egyhez, egy-a-többhöz és Tevékenységazonosító tartomány függőség. Ezek adjon meg egy negyedik forgatókönyv, több-a-többhöz érdekében is kombinálhatók.

| A forgatókönyv&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Példa |  |
|:---:| --- | --- |
|  [One-to-one](#one-to-one) |*taskB* függ *taskA* <p/> *taskB* a végrehajtása nem sikerült ütemezni *taskA* sikeresen befejeződött |![Ábra:-az-egyhez tevékenységfüggőség][1] |
|  [-A-többhöz](#one-to-many) |A *taskC* a *taskA* és a *taskB* tevékenységtől is függ <p/> *taskC* végrehajtás, amíg nem sikerült ütemezni *taskA* és *taskB* sikeresen befejeződött |![Ábra: egy-a-többhöz tevékenységfüggőség][2] |
|  [A feladat tartomány](#task-id-range) |*taskD* feladatok számos függ <p/> *taskD* az azonosítók a feladatok végrehajtása nem sikerült ütemezni *1* keresztül *10* sikeresen befejeződött |![Ábra: Feladat azonosítója tartomány függőség][3] |

> [!TIP]
> Létrehozhat **több-a-többhöz** kapcsolatok, például a, C, D, E, és F egyes feladatok függ-e a és b feladatok Ez akkor hasznos, például a párhuzamos működésű előfeldolgozási forgatókönyvek, ahol a feladat függ-e több felsőbb rétegbeli tevékenység kimenetét.
> 
> Ebben a szakaszban a példákban egy függő feladat fut, csak azt követően a szülő tevékenység sikeresen befejeződik. Ez a viselkedés egy függő feladat az alapértelmezett viselkedést. Függő tevékenység után egy fölérendelt tevékenység meghiúsul egy függőségi művelet felülírhatja az alapértelmezett viselkedést megadásával futtathatja. Tekintse meg a [függőségi műveletek](#dependency-actions) című szakasz részletezi.

### <a name="one-to-one"></a>-Az-egyhez
Egy-az-egyhez kapcsolat, a feladat egy fölérendelt tevékenység sikeres befejezésének függ. A függőség létrehozásához adja meg egy egyetlen a [TaskDependencies][net_taskdependencies].[ OnId] [ net_onid] statická metoda feltöltésekor a [DependsOn] [ net_dependson] tulajdonsága [CloudTask] [ net_cloudtask].

```csharp
// Task 'taskA' doesn't depend on any other tasks
new CloudTask("taskA", "cmd.exe /c echo taskA"),

// Task 'taskB' depends on completion of task 'taskA'
new CloudTask("taskB", "cmd.exe /c echo taskB")
{
    DependsOn = TaskDependencies.OnId("taskA")
},
```

### <a name="one-to-many"></a>-A-többhöz
Egy-a-többhöz kapcsolat egy feladat több szülő feladat befejezése után függ. A függőség létrehozásához adja meg a feladat azonosítóinak gyűjteménye a [TaskDependencies][net_taskdependencies].[ OnIds] [ net_onids] statická metoda feltöltésekor a [DependsOn] [ net_dependson] tulajdonsága [CloudTask] [ net_cloudtask].

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

### <a name="task-id-range"></a>A feladat tartomány
Egy függőség a szülő feladatok számos, a feladat egy tartományon belül esik olyan feladatok befejezése után függ.
A függőség létrehozása, adja meg az első és utolsó tevékenység-azonosítók és közé a [TaskDependencies][net_taskdependencies].[ OnIdRange] [ net_onidrange] statická metoda feltöltésekor a [DependsOn] [ net_dependson] tulajdonsága [CloudTask] [ net_cloudtask].

> [!IMPORTANT]
> A függőségek tevékenység azonosítója címtartományok használatakor azonosítóit jelölő egész számok feladatok csak a tartomány által lesz kiválasztva. Ezért a tartomány `1..10` választ ki a feladatok `3` és `7`, azonban nem `5flamingoes`. 
> 
> Nullákat esetén nem áll funkcionalitás kiértékelése tartomány függőségeket, így a feladatokat a karakterlánc-azonosítók `4`, `04` és `004` lesz, minden *belül* , és a tartomány összes tekintendők feladat`4`, így az elsőt végrehajtásához eleget tesz a függőséget.
> 
> A tartomány minden feladatot meg kell felelniük a függőség, folyamat sikeres végrehajtása vagy befejezése, amely le van képezve egy függőségi beállítású műveletet hibával **Satisfy**. Tekintse meg a [függőségi műveletek](#dependency-actions) című szakasz részletezi.
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

## <a name="dependency-actions"></a>Függőségi műveletek

Alapértelmezés szerint egy függő feladat vagy feladatokhoz fut csak akkor, ha egy fölérendelt tevékenység sikeresen befejeződött. Bizonyos esetekben érdemes függő feladatok futtatásához, még akkor is, ha a szülő feladat meghiúsul. Az alapértelmezett viselkedést felülírhatja egy függőségi művelet megadásával. Egy függőségi művelet meghatározza, hogy egy függő feladat futtatásához a jogosult sikerességét vagy sikertelenségét a szülőfeladat alapján. 

Tegyük fel, hogy egy függő feladat befejezése után a felsőbb rétegbeli tevékenység adatainak vár. Ha a felsőbb rétegbeli tevékenység meghiúsul, a függő tevékenység még lehet tudni futtatni a régebbi adatok használatával. Ebben az esetben egy függőségi művelet is adja meg, hogy a függő tevékenység annak ellenére, hogy a hiba a szülőfeladat futtatására alkalmas.

Egy függőségi művelet a fölérendelt tevékenységhez egy kilépési feltétel alapján történik. Megadhat egy függőségi műveletet a következő kilépési feltételek bármelyikének a .NET-hez, lásd: a [ExitConditions] [ net_exitconditions] osztály kapcsolatos részletek:

- Ha egy előfeldolgozási hiba történik.
- Fájlfeltöltési hiba bekövetkezésekor. Ha a feladat-n keresztül megadott kilépési kóddal kilép **exitCodes** vagy **exitCodeRanges**, és majd a fájl feltöltési hiba, a kilépési kód által meghatározott műveletet ütközik élvez elsőbbséget.
- A feladat által meghatározott kilépési kóddal kilép, amikor a **ExitCodes** tulajdonság.
- Ha a feladat kilépési kódot, amely egy megadott tartományon belüli lép a **ExitCodeRanges** tulajdonság.
- Az alapértelmezett esetben, ha a feladat által nem meghatározott kilépési kóddal kilép **ExitCodes** vagy **ExitCodeRanges**, vagy ha a feladat egy előfeldolgozási hiba miatt kilép, és a **PreProcessingError** tulajdonság nincs beállítva, vagy ha a feladat sikertelen lesz a fájl feltöltési hiba és a **FileUploadError** tulajdonság nincs beállítva. 

Adjon meg egy függőségi műveletet a .NET-ben, állítsa be a [ExitOptions][net_exitoptions].[ DependencyAction] [ net_dependencyaction] kilépési feltétel tulajdonság. A **DependencyAction** tulajdonság két érték egyikét veheti fel:

- Beállítás a **DependencyAction** tulajdonságot **Satisfy** azt jelzi, hogy a függő feladatok futtatását, ha a megadott hiba miatt kilép a szülőfeladat jogosult.
- Beállítás a **DependencyAction** tulajdonságot **blokk** azt jelzi, hogy a függő tevékenységek nem jogosultak futtatásához.

Alapértelmezés szerint a **DependencyAction** tulajdonság **Satisfy** a kilépési kód: 0, és **blokk** az összes többi kilépési feltétel.

A következő kódrészlet csoportok kód a **DependencyAction** tulajdonság egy fölérendelt tevékenységhez. Ha egy előfeldolgozási hiba miatt kilép a szülőfeladat, vagy az adott hibakódok, a függő feladat le van tiltva. A szülőfeladat bármely más nullától eltérő hiba miatt kilép, a függő tevékenység futtatására alkalmas.

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
A [TaskDependencies] [ github_taskdependencies] mintaprojektet az egyik a [Azure Batch-kódminta] [ github_samples] a Githubon. A Visual Studio-megoldást mutat be:

- A tevékenységfüggőség feladat engedélyezése
- Egyéb feladatok függő feladatok létrehozása
- Hogyan hajtható végre ezeket a feladatokat a számítási csomópontok készletén.

## <a name="next-steps"></a>További lépések
### <a name="application-deployment"></a>Alkalmazás központi telepítése
A [alkalmazáscsomagok](batch-application-packages.md) egyszerű megoldást kínál a Batch szolgáltatás egyaránt üzembe helyezése és az alkalmazásokat, amelyek a tevékenységek lefutnak a számítási csomópontok verziója.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások telepítése és az adatok
Lásd: [alkalmazások telepítéséről és az adatok a Batch számítási csomópontokon] [ forum_post] áttekintését a metódusok a csomópontok, feladatok futtatása Azure Batch fórumban. Az Azure Batch-csapat tagjainak egyike által írt, a bejegyzés a sokféleképpen lehet alkalmazásokat, a feladat bemeneti adatokat és egyéb fájlokat másolni a számítási csomópontok jó alapfogalmainak ismertetését.

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

[1]: ./media/batch-task-dependency/01_one_to_one.png "Ábra: egy az egyhez típusú függőség"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Ábra: egy-a-többhöz függőség"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Ábra: feladat azonosítója tartomány függőség"
