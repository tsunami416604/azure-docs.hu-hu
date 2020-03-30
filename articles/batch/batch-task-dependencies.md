---
title: Feladatfüggőségek létrehozása feladatok futtatásához - Azure Batch
description: Hozzon létre olyan feladatokat, amelyek a MapReduce stílus és hasonló big data-számítási feladatok azure batch-ben történő feldolgozásához szükséges egyéb feladatok elvégzésétől függenek.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: b8d12db5-ca30-4c7d-993a-a05af9257210
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ca771117e889afc8e143c4ca4626ab2d3bb4da2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022902"
---
# <a name="create-task-dependencies-to-run-tasks-that-depend-on-other-tasks"></a>Tevékenységfüggőségek létrehozása más feladatoktól függő tevékenységek futtatásához

Tevékenységfüggőségeket határozhat meg, hogy csak a fölérendelt feladat befejezése után futtasson egy feladatot vagy tevékenységcsoportot. Néhány olyan forgatókönyv, ahol a tevékenységfüggőségek hasznosak:

* MapReduce stílusú számítási feladatok a felhőben.
* Azok a feladatok, amelyek adatfeldolgozási feladatait irányított aciklikus grafikonként (DAG) lehet kifejezni.
* A renderelés előtti és utáni folyamatok, ahol minden feladatnak el kell végeznie, mielőtt a következő feladat elkezdődhetne.
* Bármely más feladat, amelyben az alsóbb rétegbeli feladatok a felsőbb rétegbeli feladatok kimenetétől függenek.

A Batch feladatfüggőségek segítségével létrehozhat olyan feladatokat, amelyek végrehajtása számítási csomópontokon egy vagy több szülőfeladat befejezése után van ütemezve. Létrehozhat például egy feladatot, amely egy 3D-film minden egyes képkockát külön, párhuzamos feladatokkal jelenít meg. A végső feladat - az "egyesítési feladat"- csak az összes képkocka sikeres renderelése után egyesíti a renderelt képkockákat a teljes filmbe.

Alapértelmezés szerint a függő feladatok végrehajtása csak a szülőfeladat sikeres befejezése után lesz ütemezve. Megadhat egy függőségi műveletet, amely felülbírálja az alapértelmezett viselkedést, és feladatokat futtat, ha a szülőfeladat sikertelen. A részleteket a [Függőségi műveletek](#dependency-actions) szakaszban találja.  

Létrehozhat olyan tevékenységeket, amelyek az egy-az-egyhez vagy az egy-a-többhöz kapcsolat más tevékenységeitől függenek. Tartományfüggőséget is létrehozhat, ahol egy tevékenység egy adott tevékenységazonosító-tartományon belüli tevékenységcsoport befejezésétől függ. A három alapvető forgatókönyv kombinálásával több-a-többhöz kapcsolatokat hozhat létre.

## <a name="task-dependencies-with-batch-net"></a>Tevékenységfüggőségek a Batch .NET-tel
Ebben a cikkben a Feladatfüggőségek konfigurálásának módját tárgyaljuk a [Batch .NET][net_msdn] függvénytár használatával. Először bemutatjuk, hogyan engedélyezheti a [feladatoktól való függőséget,](#enable-task-dependencies) majd bemutatjuk, hogyan [konfigurálhat függőségekkel rendelkező feladatot.](#create-dependent-tasks) Azt is ismertetjük, hogyan adhat meg függőségi műveletet a függő feladatok futtatásához, ha a szülő meghibásodik. Végül megbeszéljük a [függőségi forgatókönyvek,](#dependency-scenarios) amelyek batch támogatja.

## <a name="enable-task-dependencies"></a>Tevékenységfüggőségek engedélyezése
A Batch-alkalmazásban a tevékenységfüggőségek használatához először konfigurálnia kell a feladatot a tevékenységfüggőségek használatára. A Batch .NET alkalmazásban engedélyezze a [CloudJob][net_cloudjob] szolgáltatásban `true`a [UsesTaskDependencies][net_usestaskdependencies] tulajdonság beállításával:

```csharp
CloudJob unboundJob = batchClient.JobOperations.CreateJob( "job001",
    new PoolInformation { PoolId = "pool001" });

// IMPORTANT: This is REQUIRED for using task dependencies.
unboundJob.UsesTaskDependencies = true;
```

Az előző kódrészletben a "batchClient" a [BatchClient][net_batchclient] osztály egy példánya.

## <a name="create-dependent-tasks"></a>Függő feladatok létrehozása
Ha egy vagy több szülőtevékenység befejezésétől függő feladatot szeretne létrehozni, megadhatja, hogy a tevékenység "függjen" a többi tevékenységtől. A Batch .NET alkalmazásban konfigurálja a [CloudTask .][net_cloudtask] [DependsOn][net_dependson] tulajdonság a [TaskDependencies][net_taskdependencies] osztály egy példányával:

```csharp
// Task 'Flowers' depends on completion of both 'Rain' and 'Sun'
// before it is run.
new CloudTask("Flowers", "cmd.exe /c echo Flowers")
{
    DependsOn = TaskDependencies.OnIds("Rain", "Sun")
},
```

Ez a kódrészlet függő feladatot hoz létre a "Virágok" tevékenységazonosítóval. A "Virágok" feladat az "Eső" és a "Nap" feladatoktól függ. A "Virágok" feladat csak akkor fog futni egy számítási csomóponton, ha az "Eső" és a "Nap" feladatok sikeresen befejeződtek.

> [!NOTE]
> Alapértelmezés szerint egy feladat akkor tekinthető sikeresnek, ha **befejezett** állapotban `0`van, és a **kilépési kódja** . A Batch .NET-ben ez [egy CloudTask -ot][net_cloudtask]jelent. [A][net_taskstate] CloudTask `Completed` TaskExecutionInformation tulajdonságértéke és [tulajdonsága.][net_taskexecutioninformation] [ExitCode][net_exitcode] tulajdonság `0`értéke . Ennek módosításáról a [Függőségi műveletek című](#dependency-actions) szakaszban található.
> 
> 

## <a name="dependency-scenarios"></a>Függőségi forgatókönyvek
Az Azure Batch-ben három alapvető tevékenységfüggőségi forgatókönyv használható: egy az egyhez, egy-a-többhöz és feladatazonosító-tartomány függőség. Ezek kombinálhatók, hogy egy negyedik forgatókönyv, több-a-többhöz.

| Forgatókönyv&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Példa |  |
|:---:| --- | --- |
|  [Egy-az-egyhez](#one-to-one) |*a B* feladat az *A feladattól* függ <p/> *A B feladat* nem lesz ütemezve végrehajtásra, amíg az *A feladat* sikeresen be nem fejeződött |![Diagram: egy az egyhez tevékenységfüggőség][1] |
|  [Egy-a-többhöz](#one-to-many) |A *taskC* a *taskA* és a *taskB* tevékenységtől is függ <p/> *a C feladat* csak akkor lesz ütemezve végrehajtásra, ha mind az *A. feladat,* mind *a B feladat* sikeresen befejeződött |![Diagram: egy-a-többhöz tevékenységfüggőség][2] |
|  [Feladatazonosító tartománya](#task-id-range) |*taskD* számos feladattól függ <p/> *taskD* nem lesz ütemezve végrehajtásra, amíg az *1-10* azonosítóval rendelkező feladatok sikeresen be nem fejeződtek *10* |![Diagram: Tevékenységazonosító-tartomány függősége][3] |

> [!TIP]
> Létrehozhat **több-a-többhöz** kapcsolatokat, például azt, hogy a C, D, E és F feladatok hol függenek az A és B feladatoktól. Ez például olyan párhuzamos előfeldolgozási forgatókönyvekben hasznos, ahol az alsóbb rétegbeli feladatok több upstream feladat kimenetétől függenek.
> 
> Ebben a szakaszban szereplő példákban egy függő feladat csak a szülőfeladatok sikeres befejezése után fut le. Ez a viselkedés a függő feladatok alapértelmezett viselkedése. A függő tevékenységet futtathatja, miután egy szülőfeladat sikertelen, ha megad egy függőségi műveletet az alapértelmezett viselkedés felülbírálásához. A részleteket a [Függőségi műveletek](#dependency-actions) szakaszban találja.

### <a name="one-to-one"></a>Egy-az-egyhez
Egy-az-egyhez kapcsolatban a tevékenység az egyik szülőfeladat sikeres befejezésétől függ. A függőség létrehozásához adjon meg egyetlen feladatazonosítót a [Tevékenységfüggőségek számára.][net_taskdependencies] [OnId][net_onid] statikus metódus a CloudTask [DependsOn][net_dependson] tulajdonságának feltöltésekor. [CloudTask][net_cloudtask]

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
Egy-a-többhöz kapcsolatban a tevékenység több fölérendelt tevékenység befejezésétől függ. A függőség létrehozásához adja meg a Tevékenységfüggőségek feladatazonosítóinak [gyűjteményét.][net_taskdependencies] [A(z) OnIds][net_onids] statikus metódus a [CloudTask][net_cloudtask] [DependsOn][net_dependson] tulajdonságának feltöltésekor.

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

### <a name="task-id-range"></a>Feladatazonosító tartománya
A szülőtevékenységek tartományátó tevékenységek függősége a tartományon belüli azonosítóval kapcsolatos feladatok elvégzésétől függ.
A függőség létrehozásához adja meg a tartomány első és utolsó feladatazonosítóját a [Tevékenységfüggőségek számára.][net_taskdependencies] [OnIdRange][net_onidrange] statikus metódus a CloudTask [DependsOn][net_dependson] tulajdonságának feltöltésekor. [CloudTask][net_cloudtask]

> [!IMPORTANT]
> Ha a függőségekhez feladatazonosító-tartományokat használ, a tartomány csak az egész értékeket jelölő azonosítókkal rendelkező tevékenységeket választja ki. Így a `1..10` tartomány `3` kiválasztja a feladatokat, de `7`nem `5flamingoes`. 
> 
> A kezdő nullák nem jelentősek a tartományfüggőségek kiértékelésekor, így a `4`karakterlánc-azonosítókkal rendelkező feladatok a *tartományon belül* `4` `04` `004` lesznek, és mindegyik feladatként lesz kezelve, így az első, amelyet el kell végezni, kielégíti a függőséget.
> 
> A tartomány minden feladatának meg kell felelnie a függőségnek, akár sikeresen, akár egy **Megfeleltetésre**beállított függőségi művelethez leképezett hiba végrehajtásával. A részleteket a [Függőségi műveletek](#dependency-actions) szakaszban találja.
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

Alapértelmezés szerint egy függő feladat vagy feladatcsoport csak a szülőfeladat sikeres befejezése után fut le. Bizonyos esetekben előfordulhat, hogy függő feladatokat szeretne futtatni, még akkor is, ha a szülőfeladat sikertelen. Az alapértelmezett viselkedést egy függőségi művelet megadásával felülbírálhatja. A függőségi művelet azt határozza meg, hogy egy függő feladat a szülőfeladat sikeressége vagy sikertelensége alapján futtatható-e. 

Tegyük fel például, hogy egy függő tevékenység a felsőfolyási feladat befejezésétől vár adatokat. Ha a felsőbb sugárúti feladat sikertelen, a függő feladat továbbra is futtatható lehet régebbi adatok használatával. Ebben az esetben a függőségi művelet megadhatja, hogy a függő feladat a szülőfeladat hibája ellenére is futtatható legyen.

A függőségi művelet a szülőfeladat kilépési feltételén alapul. Függőségi műveletet a következő kilépési feltételek bármelyikére adhat meg; a .NET esetében a részleteket az [ExitConditions][net_exitconditions] osztályban találja:

- Ha előfeldolgozási hiba történik.
- Fájlfeltöltési hiba esetén. Ha a feladat exitCodes vagy **exitCodeRanges**segítségével megadott kilépési kóddal lép ki, majd fájlfeltöltési hibába ütközik, a kilépési kód által megadott művelet élvez elsőbbséget. **exitCodes**
- Amikor a feladat kilép az **ExitCodes** tulajdonság által meghatározott kilépési kóddal.
- Amikor a feladat kilép egy kilépési kóddal, amely az **ExitCodeRanges** tulajdonság által meghatározott tartományba esik.
- Az alapértelmezett eset, ha a feladat kilépési kóddal lép ki, amelyet nem az **ExitCodes** vagy az **ExitCodeRanges**határoz meg, vagy ha a feladat előfeldolgozási hibával lép ki, és a **PreProcessingError** tulajdonság nincs beállítva, vagy ha a feladat fájlfeltöltési hibával sikertelen, és a **FileUploadError** tulajdonság nincs beállítva. 

Ha függőségi műveletet szeretne megadni a .NET-ben, állítsa be az [ExitOptions kapcsolót.][net_exitoptions] [A dependencyAction][net_dependencyaction] tulajdonság a kilépési feltételhez. A **DependencyAction** tulajdonság két értéket vesz igénybe:

- A **DependencyAction** tulajdonság megfeleltetése azt jelzi, hogy a függő feladatok futtathatók, ha a szülőfeladat egy megadott hibával lép ki. **Satisfy**
- A **DependencyAction** tulajdonság **blokkra** állítása azt jelzi, hogy a függő tevékenységek nem futtathatók.

A **DependencyAction** tulajdonság alapértelmezett beállítása a 0 kilépési kód **megfelel,** az összes többi kilépési feltétel **blokkolása.**

A következő kódrészlet beállítja a **FüggőségiMűvelet** tulajdonságot egy szülőfeladathoz. Ha a szülőfeladat előfeldolgozási hibával vagy a megadott hibakódokkal lép ki, a függő feladat blokkolva lesz. Ha a szülőfeladat bármely más nem nulla hibával lép ki, a függő feladat futtatható.

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
A [TaskDependencies][github_taskdependencies] mintaprojekt az [egyik Az Azure Batch-kód minták][github_samples] a GitHubon. Ez a Visual Studio-megoldás a következőket mutatja be:

- Feladatfüggőség engedélyezése a feladaton
- Más feladatoktól függő feladatok létrehozása
- Ezeket a feladatokat számítási csomópontok készletén hajtsa végre.

## <a name="next-steps"></a>További lépések
### <a name="application-deployment"></a>Alkalmazás központi telepítése
A Batch [alkalmazáscsomagok](batch-application-packages.md) szolgáltatása egyszerű módot biztosít a feladatok által a számítási csomópontokon végrehajtott alkalmazások üzembe helyezésére és verziózására.

### <a name="installing-applications-and-staging-data"></a>Alkalmazások és átmeneti adatok telepítése
Az Azure Batch-fórumban az [alkalmazások telepítése és az átmeneti adatok batch számítási csomópontokon][forum_post] című témakörben olvashat a csomópontok feladatok futtatására való előkészítésének módszereiről. Az Azure Batch-csapat egyik tagja által írt, ez a bejegyzés egy jó primer a különböző módon másolni alkalmazások, feladat bemeneti adatok és egyéb fájlokat a számítási csomópontok.

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
[3]: ./media/batch-task-dependency/03_task_id_range.png "Diagram: tevékenységazonosító tartományfüggősége"
