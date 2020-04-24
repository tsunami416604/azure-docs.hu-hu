---
title: Feladat függőségeinek létrehozása a feladatok futtatásához – Azure Batch
description: Olyan feladatokat hozhat létre, amelyek a MapReduce stílusának és a hasonló big data munkaterheléseknek a Azure Batch-ban történő feldolgozásához szükségesek.
ms.topic: article
ms.date: 05/22/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9b3bc37a3d004f077e2e780d096b7bb2a8e5f773
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116485"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tevékenységek függőségeinek létrehozása a más feladatoktól függő feladatok futtatásához

Megadhatja a tevékenységek függőségeit, hogy csak a fölérendelt feladat befejeződése után futtasson feladatot vagy feladatokat. Néhány esetben a feladatok függőségei hasznosak lehetnek:

* MapReduce-stílusú számítási feladatok a felhőben.
* Azok a feladatok, amelyek adatfeldolgozási feladatait irányított aciklikus gráfként (DAG) lehet kifejezni.
* A renderelés előtti és utáni folyamatokat, ahol minden tevékenységnek a következő feladat megkezdése előtt végre kell hajtania.
* Minden egyéb feladat, amelyben az alárendelt feladatok a felsőbb rétegbeli tevékenységek kimenetének függvényei.

A Batch-feladatok függőségeivel olyan feladatokat hozhat létre, amelyek egy vagy több szülő feladat befejezése után a számítási csomópontokon való végrehajtásra vannak ütemezve. Létrehozhat például egy olyan feladatot, amely egy 3D-film egyes kereteit különálló, párhuzamos feladatokkal jeleníti meg. Végső feladat – az "egyesítési feladat" – a megjelenített kereteket csak akkor egyesíti a teljes filmben, ha az összes keret sikeresen meg lett jelenítve.

Alapértelmezés szerint a függő tevékenységek csak a fölérendelt feladat sikeres befejezése után lesznek ütemezve végrehajtásra. Megadhat egy függőségi műveletet, amely felülbírálja az alapértelmezett viselkedést, és futtatja a feladatokat, ha a fölérendelt feladat meghiúsul. A részletekért tekintse meg a [függőségi műveletek](#dependency-actions) szakaszt.  

Létrehozhat olyan feladatokat, amelyek az egy-az-egyhez vagy egy-a-többhöz kapcsolat más feladataitól függenek. Létrehozhat olyan tartomány-függőséget is, amelyben a feladatok egy adott munkacsoportnak a tevékenységek egy adott tartományán belüli befejeződésétől függenek. Ezt a három alapvető forgatókönyvet kombinálhatja több-a-többhöz kapcsolat létrehozásához.

## <a name="task-dependencies-with-batch-net"></a>Feladatok függőségei a Batch .NET-tel
Ebből a cikkből megtudhatja, hogyan konfigurálhatja a feladatok függőségeit a [Batch .net][net_msdn] -kódtár használatával. Először bemutatjuk, hogyan lehet engedélyezni a feladatok [függőségét](#enable-task-dependencies) a feladatokon, majd bemutatjuk, hogyan [konfigurálhatja a feladatokat függőségekkel](#create-dependent-tasks). Azt is leírjuk, hogyan adhat meg függőségi műveletet a függő feladatok futtatásához, ha a szülő meghibásodik. Végezetül megbeszéljük a Batch által támogatott [függőségi helyzeteket](#dependency-scenarios) .

## <a name="enable-task-dependencies"></a>Feladat függőségeinek engedélyezése
A feladatok függőségeinek a Batch-alkalmazásban való használatához először a feladat-függőségek használatára kell konfigurálnia a feladatot. A Batch .NET-ben engedélyezze a [CloudJob][net_cloudjob] a [UsesTaskDependencies][net_usestaskdependencies] tulajdonságának beállításához a `true`következőre:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Az előző kódrészletben a "batchClient" a [batchClient][net_batchclient] osztály egy példánya.

## <a name="create-dependent-tasks"></a>Függő feladatok létrehozása
Egy vagy több fölérendelt feladat befejeződésétől függő feladat létrehozásához megadhatja, hogy a feladat a többi feladattól függ. A Batch .NET-ben konfigurálja a [CloudTask][net_cloudtask]. [DependsOn][net_dependson] tulajdonság a [TaskDependencies][net_taskdependencies] osztály egy példányával:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ez a kódrészlet létrehoz egy függő feladatot a "Flowers" AZONOSÍTÓJÚ feladathoz. A "virágok" feladat az "Rain" és a "Sun" tevékenységtől függ. A "Flowers" feladat csak akkor lesz ütemezve a számítási csomópontokon való futtatásra, ha az "Rain" és a "Sun" feladatok végrehajtása sikeresen befejeződött.

> [!NOTE]
> Alapértelmezés szerint a rendszer úgy tekinti a feladatot, hogy a **befejezett** állapotban van, és a **kilépési kódja** is `0`sikeres. A Batch .NET-ben ez egy [CloudTask][net_cloudtask]jelent. Az [állapot][net_taskstate] tulajdonság értéke `Completed` és a CloudTask [TaskExecutionInformation][net_taskexecutioninformation]. [ExitCode][net_exitcode] A `0`ExitCode tulajdonság értéke:. Ennek módjáról a [függőségi műveletek](#dependency-actions) című szakaszban olvashat.
> 
> 

## <a name="dependency-scenarios"></a>Függőségi helyzetek
Három alapvető feladat-függőségi forgatókönyv használható Azure Batchban: egy-az-egyhez, egy-a-többhöz és a feladat-azonosító tartomány függőségei. Ezek kombinálhatók úgy, hogy egy negyedik forgatókönyvet, több a többhöz.

| Forgatókönyv&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Példa |  |
|:---:| --- | --- |
|  [Egy-az-egyhez](#one-to-one) |a *taskB* a *Taska* függvénytől függ <p/> a *taskB* nem lesz ütemezve végrehajtásra, amíg a *Taska* sikeresen nem fejeződött be. |![Diagram: egy-az-egyhez típusú feladat-függőség][1] |
|  [Egy-a-többhöz](#one-to-many) |A *taskC* a *taskA* és a *taskB* tevékenységtől is függ <p/> a *taskC* nem lesz ütemezve a végrehajtáshoz, amíg a *taske* és a *taskB* sikeresen nem fejeződött be. |![Diagram: egy-a-többhöz feladat-függőség][2] |
|  [Feladat-azonosító tartománya](#task-id-range) |a *feladat* számos feladattól függ <p/> a *feladat* nem lesz ütemezve végrehajtásra, amíg az *1* – *10* azonosítójú feladatok sikeresen befejeződtek. |![Ábra: a feladat-azonosító tartományának függősége][3] |

> [!TIP]
> **Több-a-többhöz** kapcsolatot is létrehozhat, például a C, D, E és F feladatok mindegyike az a és B feladatoktól függ. Ez hasznos lehet például olyan párhuzamos előfeldolgozási forgatókönyvekben, ahol az alsóbb rétegbeli tevékenységek több felsőbb rétegbeli feladat kimenetének függvényei.
> 
> Az ebben a szakaszban szereplő példákban egy függő feladat csak a fölérendelt feladatok sikeres befejeződése után fut le. Ez a viselkedés egy függő feladat alapértelmezett viselkedése. A fölérendelt feladat végrehajtása után futtathat egy függő feladatot egy függőségi művelet megadásával, amely felülbírálja az alapértelmezett viselkedést. A részletekért tekintse meg a [függőségi műveletek](#dependency-actions) szakaszt.

### <a name="one-to-one"></a>Egy-az-egyhez
Egy-az-egyhez kapcsolat esetén a feladat egy fölérendelt feladat sikeres befejezését határozza meg. A függőség létrehozásához adjon meg egy feladat-azonosítót a [TaskDependencies][net_taskdependencies]. [OnId][net_onid] statikus metódus a [CloudTask][net_cloudtask] [DependsOn][net_dependson] tulajdonságának feltöltésekor.

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
Egy-a-többhöz kapcsolat esetén a feladatok több szülő feladat befejeződésétől függenek. A függőség létrehozásához adja meg a [TaskDependencies][net_taskdependencies]gyűjteményét. [OnIds][net_onids] statikus metódus a [CloudTask][net_cloudtask] [DependsOn][net_dependson] tulajdonságának feltöltésekor.

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

### <a name="task-id-range"></a>Feladat-azonosító tartománya
A fölérendelt feladatok számos függősége esetén a feladatok attól függnek, hogy az azonosítók milyen tartományon belül találhatók.
A függőség létrehozásához adja meg a tartomány első és utolsó feladatának azonosítóját a [TaskDependencies][net_taskdependencies]. [OnIdRange][net_onidrange] statikus metódus a [CloudTask][net_cloudtask] [DependsOn][net_dependson] tulajdonságának feltöltésekor.

> [!IMPORTANT]
> Ha a függőségek esetében feladatsor-tartományt használ, csak az egész értékeket megadó feladatokat fogja kiválasztani a tartomány. Így a tartomány `1..10` kiválaszthatja `3` a `7`feladatokat, de `5flamingoes`nem. 
> 
> A bevezető nullák nem jelentősek a tartomány függőségeinek kiértékelése során, így `4`a `04` karakterlánc `004` -azonosítókkal rendelkező tevékenységek, és mind a tartományon *belül* lesznek, `4`és az összes feladatot feladatnak tekinti, így az első befejezési művelet megfelel a függőségnek.
> 
> A tartomány minden tevékenységének meg kell felelnie a függőségnek, akár a sikeres befejezést követően, akár egy, a függőségi művelethez hozzárendelt hibával, amely **megfelel a követelményeknek**. A részletekért tekintse meg a [függőségi műveletek](#dependency-actions) szakaszt.
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

Alapértelmezés szerint a függő feladat vagy a tevékenységek halmaza csak a fölérendelt feladat sikeres befejeződése után fut. Bizonyos helyzetekben érdemes lehet függő feladatokat futtatni, még akkor is, ha a fölérendelt feladat meghiúsul. A függőségi műveletek megadásával felülbírálhatja az alapértelmezett viselkedést. A függőségi műveletek határozzák meg, hogy egy függő feladat futtatható-e a fölérendelt feladat sikeressége vagy meghibásodása alapján. 

Tegyük fel például, hogy egy függő feladat a felsőbb rétegbeli feladat befejezésekor vár az adatokra. Ha a felsőbb rétegbeli feladat meghiúsul, előfordulhat, hogy a függő feladat továbbra is futtatható a régebbi adatok használatával. Ebben az esetben a függőségi művelet megadhatja, hogy a függő tevékenység a fölérendelt feladat meghibásodása ellenére is futtatható legyen.

A függőségi műveletek a fölérendelt feladat kilépési feltételén alapulnak. A következő kilépési feltételek bármelyikéhez megadhat függőségi műveletet: a .NET esetében a részletekért tekintse meg a [ExitConditions][net_exitconditions] osztályt:

- Előfeldolgozási hiba esetén.
- Fájl feltöltésekor felmerülő hiba esetén. Ha a feladat kilép egy **exitCodes** -vagy **exitCodeRanges**-n keresztül megadott kilépési kóddal, és a fájl feltöltési hibába ütközik, a kilépési kód által megadott művelet elsőbbséget élvez.
- Ha a feladat kilép a **ExitCodes** tulajdonság által meghatározott kilépési kóddal.
- Ha a feladat kilép egy kilépési kóddal, amely a **ExitCodeRanges** tulajdonság által megadott tartományon belül esik.
- Az alapértelmezett eset, ha a feladat kilép egy **ExitCodes** vagy **ExitCodeRanges**által nem definiált kilépési kóddal, vagy ha a feladat egy előfeldolgozási hiba miatt kilép, és a **PreProcessingError** tulajdonság nincs beállítva, vagy ha a feladat meghiúsul, és a **FileUploadError** tulajdonság nincs beállítva. 

Ha függőségi műveletet szeretne megadni a .NET-ben, állítsa be a [ExitOptions][net_exitoptions]. A kilépési feltétel [DependencyAction][net_dependencyaction] tulajdonsága. A **DependencyAction** tulajdonság két érték egyikét veszi figyelembe:

- Ha a **DependencyAction** tulajdonságot a **megfelelő** értékre állítja, azt jelzi, hogy a függő tevékenységek jogosultak a futtatására, ha a fölérendelt feladat megadott hibával kilép.
- Ha a **DependencyAction** tulajdonságot a **Letiltás** értékre állítja, azt jelzi, hogy a függő tevékenységek nem jogosultak a futtatásra.

A **DependencyAction** tulajdonság alapértelmezett beállítása a 0. kilépési kód és az összes többi kilépési feltétel **blokkolását** is **kielégíti** .

A következő kódrészlet beállítja a **DependencyAction** tulajdonságot egy fölérendelt feladathoz. Ha a fölérendelt feladat egy előfeldolgozási hibával vagy a megadott hibakódokkal kilép, a függő feladat blokkolva lesz. Ha a fölérendelt feladat bármilyen más nullától eltérő hibával kilép, a függő feladat jogosult a futtatásra.

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
A [TaskDependencies][github_taskdependencies] minta projekt a githubon lévő [Azure batch Code-minták][github_samples] egyike. Ez a Visual Studio-megoldás a következőket mutatja be:

- Feladat-függőség engedélyezése egy feladathoz
- Egyéb feladatoktól függő feladatok létrehozása
- A feladatok végrehajtása számítási csomópontok készletén.

## <a name="next-steps"></a>További lépések
### <a name="application-deployment"></a>Alkalmazás központi telepítése
A Batch [alkalmazáscsomag](batch-application-packages.md) szolgáltatásának segítségével egyszerűen telepítheti és futtathatja a tevékenységek által a számítási csomópontokon végrehajtott alkalmazásokat.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások telepítése és az előkészítési adatgyűjtés
A csomópontok feladatok futtatására való felkészülési módszereinek áttekintését lásd: [alkalmazások telepítése és az adatok előkészítése a Batch számítási csomópontjain][forum_post] a Azure batch fórumban. A Azure Batch csapat tagjainak egyike írta, ez a bejegyzés egy jó alapozó az alkalmazások másolásának különböző módjairól, a feladatok bemeneti adatairól és más fájlokról a számítási csomópontok számára.

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
[net_dependencyaction]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.exitoptions
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_onid]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onid.aspx
[net_onids]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onids.aspx
[net_onidrange]: https://msdn.microsoft.com/library/microsoft.azure.batch.taskdependencies.onidrange.aspx
[net_taskexecutioninformation]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskexecutioninformation.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_usestaskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.usestaskdependencies.aspx
[net_taskdependencies]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskdependencies.aspx

[1]: ./media/batch-task-dependency/01_one_to_one.png "Diagram: egy-az-egyhez függőség"
[2]: ./media/batch-task-dependency/02_one_to_many.png "Diagram: egy-a-többhöz függőség"
[3]: ./media/batch-task-dependency/03_task_id_range.png "Ábra: a feladat-azonosító tartományának függősége"
