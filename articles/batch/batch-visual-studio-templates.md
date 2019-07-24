---
title: Megoldások létrehozása Visual Studio-sablonokkal – Azure Batch | Microsoft Docs
description: Ismerje meg, hogy a Visual Studio Project sablonjai hogyan segíthetnek a nagy számítási igényű munkaterhelések megvalósításában és futtatásában Azure Batchon.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bb4c71f2c7f42ef599796bc380bb7a9f35b8c64e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322774"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>A Visual Studio Project templates használata a Batch-megoldások beindítására

A Batch **Manager** és a **Task processzor Visual Studio-sablonjai** olyan kódot biztosítanak, amely a lehető legkevesebb erőfeszítéssel segíti a nagy számítási igényű munkaterhelések megvalósítását és futtatását a batchben. Ez a dokumentum ismerteti ezeket a sablonokat, és útmutatást nyújt a használatáról.

> [!IMPORTANT]
> Ez a cikk csak a két sablonra vonatkozó információkat tárgyalja, és feltételezi, hogy ismeri a Batch szolgáltatást és a hozzá kapcsolódó főbb fogalmakat: készletek, számítási csomópontok, feladatok és feladatok, Feladatkezelő feladatai, környezeti változók és egyéb releváns információk. További információkat a [Azure batch](batch-technical-overview.md) és a Batch-funkciók áttekintésében találhat a [fejlesztők számára](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Áttekintés
A Feladatkezelő és a feldolgozói sablonok használatával két hasznos összetevő hozható létre:

* Egy Feladatkezelő feladat, amely egy olyan feladat-elválasztó feladatot valósít meg, amely egyszerre több, egymástól függetlenül futtatható, párhuzamosan futtatott feladatot képes feltörni.
* Egy olyan feldolgozói processzor, amely az alkalmazás parancssorának előfeldolgozására és feldolgozására használható.

Például egy mozgókép-renderelési forgatókönyvben a feladat elválasztója egyetlen mozgóképes feladatot vált ki több száz vagy több ezer különálló feladatba, amelyek külön-külön dolgozzák fel az egyes képkockákat. Ennek megfelelően a feladat-feldolgozó meghívja a renderelési alkalmazást, valamint az egyes keretek megjelenítéséhez szükséges összes függő folyamatot, valamint a további műveleteket (például a megjelenített keret tárolási helyre történő másolását).

> [!NOTE]
> A Feladatkezelő és a feladat-feldolgozó sablonok egymástól függetlenek, így a számítási feladat követelményeitől és a beállításoktól függően mindkettőt vagy csak az egyiket használhatja.
> 
> 

Ahogy az alábbi ábrán is látható, az ezeket a sablonokat használó számítási feladatok három fázison haladnak át:

1. Az ügyfél kódja (például alkalmazás, webszolgáltatás stb.) elküld egy feladatot a Batch szolgáltatásnak az Azure-ban, amely a Feladatkezelő program feladat-kezelő feladatát adja meg.
2. A Batch szolgáltatás futtatja a Feladatkezelő feladatot egy számítási csomóponton, és a feladat-elválasztó a megadott számú feladathoz tartozó processzor-feladatot a feladat-elválasztó kód paramétereinek és specifikációinak megfelelően annyi számítási csomóponton indítja el, amennyi szükséges.
3. A feladat-feldolgozói feladatok egymástól függetlenül futnak párhuzamosan a bemeneti adatok feldolgozásához és a kimeneti adatok létrehozásához.

![Ábra, amely azt mutatja, hogy az ügyfél kódja hogyan kommunikál a Batch szolgáltatással][diagram01]

## <a name="prerequisites"></a>Előfeltételek
A Batch-sablonok használatához a következőkre lesz szüksége:

* Egy számítógép, amelyen telepítve van a Visual Studio 2015. A Batch-sablonok jelenleg csak a Visual Studio 2015 esetén támogatottak.
* A Visual Studio [Galleryben][vs_gallery] Visual Studio-bővítményként elérhető batch-sablonok. A sablonok kétféleképpen szerezhetők be:
  
  * Telepítse a sablonokat a Visual Studio **Extensions and Updates (bővítmények és frissítések** ) párbeszédpanelén (további információt a [Visual Studio-bővítmények keresése és használata][vs_find_use_ext]című témakörben talál). A **bővítmények és frissítések** párbeszédpanelen keresse meg és töltse le a következő két bővítményt:
    
    * Azure Batch Job Manager és Job Splitter
    * Azure Batch feladat processzora
  * Töltse le a sablonokat a Visual studióhoz készült online galériából: [Microsoft Azure Batch Project-sablonok][vs_gallery_templates]
* Ha azt tervezi, hogy az [alkalmazáscsomag](batch-application-packages.md) funkció használatával telepíti a Feladatkezelő és a feladat processzorát a Batch számítási csomópontjain, egy Storage-fiókot kell összekapcsolnia a Batch-fiókkal.

## <a name="preparation"></a>Előkészítés
Javasoljuk, hogy hozzon létre egy olyan megoldást, amely tartalmazhatja a Feladatkezelőt, valamint a feladatait, mivel ez megkönnyíti a kód megosztását a Feladatkezelő és a feldolgozói programok között. A megoldás létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual studiót, és válassza a **fájl** > **új** > **projekt**lehetőséget.
2. A **sablonok**területen bontsa ki a **többi projekttípus**csomópontot, kattintson a **Visual Studio Solutions**elemre, majd válassza az **üres megoldás**elemet.
3. Írjon be egy nevet, amely leírja az alkalmazást és a megoldás célját (például "LitwareBatchTaskPrograms").
4. Az új megoldás létrehozásához kattintson **az OK**gombra.

## <a name="job-manager-template"></a>Job Manager-sablon
A Feladatkezelő sablon segítséget nyújt egy Feladatkezelő-feladat megvalósításához, amely a következő műveleteket hajthatja végre:

* Feladat felosztása több feladatba.
* Küldje el ezeket a feladatokat a kötegen való futtatáshoz.

> [!NOTE]
> A Feladatkezelő feladataival kapcsolatos további információkért lásd: [a Batch funkcióinak áttekintése fejlesztők](batch-api-basics.md#job-manager-task)számára.
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Feladatkezelő létrehozása sablon használatával
Ha a korábban létrehozott megoldáshoz szeretne felvenni egy Feladatkezelőt, kövesse az alábbi lépéseket:

1. Nyissa meg meglévő megoldását a Visual Studióban.
2. A megoldáskezelő kattintson a jobb gombbal a megoldásra, majd kattintson az**új projekt** **hozzáadása** > lehetőségre.
3. A **vizualizáció C#** alatt kattintson a **felhő**elemre, majd kattintson **a Feladatkezelő Azure batch Job Splitter**elemre.
4. Adjon meg egy nevet, amely leírja az alkalmazást, és azonosítja a projektet a feladatkezelőként (például "LitwareJobManager").
5. A projekt létrehozásához kattintson **az OK**gombra.
6. Végül hozza létre a projektet a Visual studióhoz, és töltse be az összes hivatkozott NuGet-csomagot, és ellenőrizze, hogy a projekt érvényes-e a módosítás megkezdése előtt.

### <a name="job-manager-template-files-and-their-purpose"></a>A Feladatkezelő sablon fájljai és azok célja
Amikor a Feladatkezelő sablonnal hoz létre egy projektet, három kódrészletet hoz létre:

* A fő programfájl (Program.cs). Ez tartalmazza a program belépési pontját és a legfelső szintű kivételek kezelését. Általában nem kell módosítania ezt.
* A keretrendszer könyvtára. Ez tartalmazza azokat a fájlokat, amelyekért a Feladatkezelő program végrehajtja a "szabványos" munkát, kicsomagolja a paramétereket, felveszi a tevékenységeket a Batch-feladatba stb. Általában nem kell módosítania ezeket a fájlokat.
* A Job Splitter fájlja (JobSplitter.cs). Itt helyezheti el az alkalmazás-specifikus logikát a feladatok tevékenységekbe való felosztásához.

Természetesen szükség szerint további fájlokat is hozzáadhat a feladat-elválasztó kódjának támogatásához, a feladat felosztási logikájának összetettsége alapján.

A sablon emellett szabványos .NET-projektfájlok (például. csproj-fájl, app. config, packages. config stb.) is létrehozható.

A szakasz további része a különböző fájlokat és a kód szerkezetét írja le, és bemutatja, hogy az egyes osztályok hogyan működik.

![A Visual Studio Megoldáskezelő a Feladatkezelő sablon megoldását mutatja][solution_explorer01]

**Keretrendszer fájljai**

* `Configuration.cs`: A feladat konfigurációs adatainak, például a Batch-fiók adatainak, a társított Storage-fiók hitelesítő adatainak, a feladat-és tevékenységadatok és a feladat paramétereinek betöltését foglalja magában. Hozzáférést biztosít a Batch által definiált környezeti változókhoz is (lásd a feladatok környezeti beállításait a Batch dokumentációjában) a Configuration. EnvironmentVariable osztály segítségével.
* `IConfiguration.cs`: Elvonta a konfigurációs osztály megvalósítását, így a feladatok elválasztóját a hamis vagy a Mock konfigurációs objektum használatával is tesztelheti.
* `JobManager.cs`: A Job Manager program összetevőinek összehangolása. Feladata a feladatok elválasztójának inicializálása, a feladat elválasztójának meghívása, valamint a feladat elválasztója által visszaadott feladatok elküldése a feladathoz.
* `JobManagerException.cs`: Olyan hibát jelöl, amely megköveteli, hogy a Feladatkezelő leálljon. A JobManagerException a "várt" hibák becsomagolására szolgál, ha a megszüntetés részeként meghatározott diagnosztikai információk is megadhatók.
* `TaskSubmitter.cs`: Ez az osztály felelős a feladat-elválasztó által a Batch-feladathoz visszaadott feladatok hozzáadásához. A JobManager osztály a feladatok sorozatát összesíti a feladathoz kapcsolódó hatékony, de a feladat időbeli feltételeit tartalmazó kötegekben, majd meghívja a TaskSubmitter. SubmitTasks-t az egyes kötegek hátterében lévő szálon.

**Feladatok elválasztója**

`JobSplitter.cs`: Ez az osztály alkalmazásspecifikus logikát tartalmaz a feladat tevékenységekbe való felosztásához. A keretrendszer meghívja a JobSplitter. Split metódust a feladatok olyan sorrendjének beszerzéséhez, amelyet a metódus ad vissza a feladathoz. Ez az az osztály, amelybe be kell szúrnia a feladatokhoz tartozó logikát. A felosztott metódus implementálása olyan CloudTask-példányok sorozatának visszaadásához, amelyek azokat a feladatokat jelölik, amelyekben a feladatot particionálni szeretné.

**Standard .NET parancssori projektfájlok**

* `App.config`: Standard .NET-alkalmazás konfigurációs fájlja.
* `Packages.config`: Standard NuGet csomag-függőségi fájl.
* `Program.cs`: A program belépési pontját és a legfelső szintű kivételek kezelését tartalmazza.

### <a name="implementing-the-job-splitter"></a>A feladatok elválasztójának implementálása
Amikor megnyitja a Feladatkezelő sablonjának projektjét, a projekt alapértelmezés szerint meg lesz nyitva a JobSplitter.cs fájljában. A feladathoz tartozó feladatokhoz a felosztott () metódust az alábbi ábrán látható módon implementálhatja:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> A `Split()` metódus megjegyzésekkel ellátott szakasza a Feladatkezelő sablon kódjának egyetlen szakasza, amelynek a módosítására szolgál, ha hozzáadja a logikát a feladatok különböző feladatokba való felosztásához. Ha módosítani szeretné a sablon egy másik szakaszát, győződjön meg arról, hogy ismeri a Batch működését, és próbálkozzon a [Batch-kódok][github_samples]néhány mintájának használatával.
> 
> 

A Split () implementációja a következőhöz fér hozzá:

* A feladatok paraméterei a `_parameters` mezőn keresztül.
* A feladatot jelképező CloudJob objektum a `_job` mezőn keresztül.
* A Feladatkezelő feladatot jelképező CloudTask objektum a `_jobManagerTask` mezőn keresztül.

A `Split()` megvalósításnak nem kell közvetlenül felvennie a feladatokat a feladatba. Ehelyett a kódnak CloudTask-objektumok sorozatot kell visszaadnia, és ezeket a rendszer automatikusan hozzáadja a feladatokhoz a feladatok elválasztóját meghívó keretrendszer osztályai alapján. Gyakori, hogy az iteráció C#(`yield return`) szolgáltatást használja a feladatok elválasztóinak megvalósításához, mivel ez lehetővé teszi, hogy a tevékenységek a lehető leggyorsabban fussanak, és ne kelljen várni az összes feladat kiszámítására.

**Sikertelen feladatok elválasztója**

Ha a feladatok elválasztója hibát észlel, akkor a következők egyikét kell tennie:

* A sorozatot az C# `yield break` utasítással szakíthatja meg, ebben az esetben a Feladatkezelő sikeresként lesz kezelve; vagy
* Kivételt képeznek, ebben az esetben a Feladatkezelő sikertelenként lesz kezelve, és az ügyfél konfigurálásának módjától függően újrapróbálkozhat.

Mindkét esetben a feladat-elválasztó által visszaadott feladatok és a Batch-feladathoz való hozzáadás is jogosult a futtatásra. Ha nem szeretné, hogy ez történjen, a következőket teheti:

* A feladatoknak a feladatok elválasztóból való visszatérése előtt leáll
* A teljes feladat gyűjtésének megfogalmazása, `ICollection<CloudTask>` `IList<CloudTask>` mielőtt visszaadná (azaz a feladat-elválasztó megvalósítását egy C# iteráció használatával)
* Feladatok függőségeinek használata az összes feladat végrehajtásához a Feladatkezelő sikeres befejezésének függvényében

**Feladatkezelő-újrapróbálkozások**

Ha a Feladatkezelő sikertelen, előfordulhat, hogy a Batch szolgáltatás újrapróbálkozik az ügyfél újrapróbálkozási beállításaitól függően. Általában ez azért biztonságos, mert amikor a keretrendszer felveszi a feladatokat a feladatba, figyelmen kívül hagyja a már létező feladatokat. Ha azonban a feladatok kiszámítása költséges, előfordulhat, hogy nem kívánja felszámítani a feladathoz már hozzáadott feladatok újraszámításának költségeit. Ezzel szemben, ha az újbóli Futtatás nem garantált, hogy ugyanazokat a feladatokat hozza létre, akkor a "duplikált elemek mellőzése" viselkedés nem fog berúgni. Ezekben az esetekben meg kell terveznie a feladatok elválasztóját, hogy észlelje a már elvégzett munkát, és ne ismételje meg, például egy CloudJob. ListTasks elvégzésével a feladatok elkezdése előtt.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kilépési kódok és kivételek a Feladatkezelő sablonban
A kilépési kódok és kivételek biztosítják a programok futtatásának eredményét, és segíthetnek azonosítani a program végrehajtásával kapcsolatos problémákat. A Feladatkezelő sablonja az ebben a szakaszban ismertetett kilépési kódokat és kivételeket implementálja.

A Feladatkezelő sablonnal megvalósított Feladatkezelő-feladatok három lehetséges kilépési kódot adhatnak vissza:

| Kód | Leírás |
| --- | --- |
| 0 |A Feladatkezelő sikeresen befejeződött. A feladat-elválasztó kódja befejeződött, és az összes feladat hozzá lett adva a feladathoz. |
| 1 |A Feladatkezelő feladat sikertelen volt, kivétel történt a program "várt" részében. A kivételt egy diagnosztikai adatokat tartalmazó JobManagerException fordították le, és ahol lehetséges, a hiba megoldására vonatkozó javaslatokat. |
| 2 |A Feladatkezelő feladat sikertelen volt, mert "váratlan" kivétel történt. A kivétel a standard kimenetre lett naplózva, de a Feladatkezelő nem tudta felvenni a további diagnosztikai vagy szervizelési információkat. |

A Feladatkezelő feladatának meghibásodása esetén előfordulhat, hogy egyes feladatok még a hiba bekövetkezése előtt hozzá lettek adva a szolgáltatáshoz. Ezek a feladatok a szokásos módon fognak futni. A kód elérési útjának tárgyalásához tekintse meg a fenti "feladatok felosztása sikertelen" című témakört.

A kivételek által visszaadott összes információ az StdOut. txt és a stderr. txt fájlba íródik. További [információ: hibakezelés](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Ügyfelekkel kapcsolatos megfontolások
Ez a szakasz néhány ügyfél-megvalósítási követelményt ismertet, amikor a sablon alapján meghívja a Feladatkezelőt. A paraméterek és környezeti beállítások átadásának részleteiért lásd: [paraméterek és környezeti változók továbbítása az ügyfél kódjából](#pass-environment-settings) .

**Kötelező hitelesítő adatok**

Ahhoz, hogy feladatokat lehessen felvenni a Azure Batch feladatba, a Feladatkezelő feladat megköveteli a Azure Batch-fiók URL-címét és kulcsát. Ezeket a YOUR_BATCH_URL és a YOUR_BATCH_KEY nevű környezeti változóban kell átadni. Ezeket megadhatja a Feladatkezelő feladat környezeti beállításainál. Például egy C# ügyfélben:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Tárolási hitelesítő adatok**

Általában az ügyfélnek nem kell megadnia a társított Storage-fiók hitelesítő adatait a Feladatkezelő feladathoz, mert (a) a legtöbb Feladatkezelő nem szükséges explicit módon hozzáférni a társított Storage-fiókhoz, és (b) a társított Storage-fiókot gyakran az összes feladathoz nyújtják a feladatokhoz tartozó általános környezet beállítása. Ha nem biztosítja a társított Storage-fiókot a közös környezet beállításain keresztül, és a Feladatkezelő hozzáférést igényel a társított tárolóhoz, akkor a következő módon kell megadnia a társított tároló hitelesítő adatait:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Feladat-kezelő feladat beállításai**

Az ügyfélnek **false**értékre kell állítania a Feladatkezelő *killJobOnCompletion* jelzőjét.

Általában biztonságos, ha az ügyfél **hamis**értékre állítja a *runExclusive* .

Az ügyfélnek a *resourceFiles* vagy a *applicationPackageReferences* gyűjteményt kell használnia ahhoz, hogy a Feladatkezelő végrehajtható fájlja (és a szükséges DLL-ek) telepítve legyen a számítási csomóponton.

Alapértelmezés szerint a Feladatkezelő nem próbálkozik újra, ha az sikertelen. A Feladatkezelő logikától függően előfordulhat, hogy az ügyfél engedélyezni szeretné az újrapróbálkozásokat a /megkötések*maxTaskRetryCount*.

**Feladatok beállításai**

Ha a feladatok elválasztója függőségekkel rendelkező feladatokat bocsát ki, az ügyfélnek igaz értékre kell állítania a feladat usesTaskDependencies.

A Job Splitter modellben szokatlan, hogy az ügyfelek feladatokat kívánnak hozzáadni a feladatokhoz, és meghaladják a feladatok elválasztóját. Az ügyfélnek ezért általában a **terminatejob**kell beállítania a feladatokhoz tartozó *onAllTasksComplete* .

## <a name="task-processor-template"></a>Feladat processzorának sablonja
A feladat-feldolgozó sablon segítséget nyújt egy olyan feladathoz, amely a következő műveleteket hajthatja végre:

* Állítsa be az egyes batch-feladatok futtatásához szükséges adatokat.
* Futtassa az egyes batch-feladatok által igényelt összes műveletet.
* Feladat kimenetének mentése állandó tárolóba.

Bár a feladatokhoz nem szükséges a Batch-feladatok futtatása, a feldolgozók használatának legfőbb előnye, hogy egy burkolót biztosít az összes feladat-végrehajtási művelet egyetlen helyen történő megvalósításához. Ha például több alkalmazást kell futtatnia az egyes feladatok kontextusában, vagy ha az egyes feladatok befejezése után át kell másolnia az adatok állandó tárterületre való másolását.

A feladat processzora által végrehajtott műveletek lehetnek egyszerűek vagy összetettek, és a számítási feladatnak megfelelő számú vagy akár kevesebb is. Emellett az összes feladatra vonatkozó művelet egyetlen feladatba való bevezetésével az alkalmazások vagy a munkaterhelés-követelmények változásai alapján könnyedén frissítheti vagy hozzáadhatja a műveleteket. Bizonyos esetekben azonban előfordulhat, hogy a feladathoz tartozó processzor nem az optimális megoldás a megvalósításhoz, mivel ez szükségtelen bonyolultságot eredményezhet, például olyan feladatok futtatásakor, amelyek gyorsan elindíthatók egy egyszerű parancssorból.

### <a name="create-a-task-processor-using-the-template"></a>Feladat-feldolgozó létrehozása sablon használatával
Ha a korábban létrehozott megoldáshoz szeretne felvenni egy feladathoz tartozó processzort, kövesse az alábbi lépéseket:

1. Nyissa meg meglévő megoldását a Visual Studióban.
2. A Megoldáskezelő kattintson a jobb gombbal a megoldásra, kattintson a **Hozzáadás**, majd az **új projekt**elemre.
3. A **vizualizáció C#** alatt kattintson a **felhő**elemre, majd kattintson **Azure batch feladat-feldolgozó**elemre.
4. Adjon meg egy nevet, amely leírja az alkalmazást, és a feladat processzorként azonosítja ezt a projektet (például "LitwareTaskProcessor").
5. A projekt létrehozásához kattintson **az OK**gombra.
6. Végül hozza létre a projektet a Visual studióhoz, és töltse be az összes hivatkozott NuGet-csomagot, és ellenőrizze, hogy a projekt érvényes-e a módosítás megkezdése előtt.

### <a name="task-processor-template-files-and-their-purpose"></a>A feladathoz tartozó sablonfájlokat és azok célját
Amikor létrehoz egy projektet a feladat-feldolgozó sablonnal, három kódrészletet hoz létre:

* A fő programfájl (Program.cs). Ez tartalmazza a program belépési pontját és a legfelső szintű kivételek kezelését. Általában nem kell módosítania ezt.
* A keretrendszer könyvtára. Ez tartalmazza azokat a fájlokat, amelyekért a Feladatkezelő program végrehajtja a "szabványos" munkát, kicsomagolja a paramétereket, felveszi a tevékenységeket a Batch-feladatba stb. Általában nem kell módosítania ezeket a fájlokat.
* A feladat-feldolgozó fájl (TaskProcessor.cs). Itt helyezheti el az alkalmazás-specifikus logikát a feladatok végrehajtásához (általában egy meglévő végrehajtható fájl meghívásával). Az előzetes és a feldolgozás utáni kód, például további adatfájlok letöltése vagy az eredményhalmaz feltöltése is ide kerül.

Természetesen a feladatok processzor-kódjának támogatásához szükség szerint további fájlokat is hozzáadhat, a feladat felosztási logikájának összetettsége alapján.

A sablon emellett szabványos .NET-projektfájlok (például. csproj-fájl, app. config, packages. config stb.) is létrehozható.

A szakasz további része a különböző fájlokat és a kód szerkezetét írja le, és bemutatja, hogy az egyes osztályok hogyan működik.

![A Visual Studio Megoldáskezelő a feladat processzor-sablon megoldását mutatja][solution_explorer02]

**Keretrendszer fájljai**

* `Configuration.cs`: A feladat konfigurációs adatainak, például a Batch-fiók adatainak, a társított Storage-fiók hitelesítő adatainak, a feladat-és tevékenységadatok és a feladat paramétereinek betöltését foglalja magában. Hozzáférést biztosít a Batch által definiált környezeti változókhoz is (lásd a feladatok környezeti beállításait a Batch dokumentációjában) a Configuration. EnvironmentVariable osztály segítségével.
* `IConfiguration.cs`: Elvonta a konfigurációs osztály megvalósítását, így a feladatok elválasztóját a hamis vagy a Mock konfigurációs objektum használatával is tesztelheti.
* `TaskProcessorException.cs`: Olyan hibát jelöl, amely megköveteli, hogy a Feladatkezelő leálljon. A TaskProcessorException a "várt" hibák becsomagolására szolgál, ha a megszüntetés részeként meghatározott diagnosztikai információk is megadhatók.

**Feladat processzora**

* `TaskProcessor.cs`: Futtatja a feladatot. A keretrendszer a TaskProcessor. Run metódust hívja meg. Ezt az osztályt kell beadnia a feladat alkalmazásspecifikus logikájának beadásához. A futtatási módszer implementálása a következőre:
  * A feladatok paramétereinek elemzése és érvényesítése
  * A parancssor összeállítása bármely elindítani kívánt külső programhoz
  * A hibakeresési célokra esetlegesen szükséges diagnosztikai információk naplózása
  * Folyamat elindítása a parancssor használatával
  * Várakozás a folyamat kilépésére
  * A folyamat kilépési kódjának rögzítése annak megállapításához, hogy sikeres vagy sikertelen volt-e
  * Mentse az állandó tárterületet megőrizni kívánt kimeneti fájlokat

**Standard .NET parancssori projektfájlok**

* `App.config`: Standard .NET-alkalmazás konfigurációs fájlja.
* `Packages.config`: Standard NuGet csomag-függőségi fájl.
* `Program.cs`: A program belépési pontját és a legfelső szintű kivételek kezelését tartalmazza.

## <a name="implementing-the-task-processor"></a>A feladat processzorának implementálása
Amikor megnyitja a feladat-feldolgozó sablonjának projektjét, a projekt alapértelmezés szerint meg lesz nyitva a TaskProcessor.cs fájljában. A számítási feladatok futtatási logikájának megvalósításához használja az alább látható Run () metódust:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> A Run () metódus megjegyzésekkel ellátott szakasza a feladat processzor-sablonjának egyetlen olyan szakasza, amelyet módosítani szeretne a számítási feladatok futtatási logikájának hozzáadásával. Ha módosítani kívánja a sablon egy másik szakaszát, először Ismerkedjen meg a Batch működésével, és tekintse át a Batch dokumentációját.
> 
> 

A Run () metódus felelős a parancssor elindításához, egy vagy több folyamat elindításához, amely az összes folyamat befejezésére vár, menti az eredményeket, és végül kilépési kóddal tér vissza. A Run () metódussal a feladatok feldolgozási logikája valósítható meg. A feladat-feldolgozó keretrendszer elindítja a Run () metódust. nem kell meghívnia.

A Run () implementációja a következőhöz fér hozzá:

* A feladat paraméterei a `_parameters` mezőn keresztül.
* A feladat és a feladat azonosítója a és `_jobId` `_taskId` a mezők használatával.
* A feladat konfigurációja a `_configuration` mezőn keresztül.

**Sikertelen feladat**

Meghibásodás esetén kiléphet a Run () metódusból egy kivétel bedobásával, de ez a feladat kilépési kódjának szabályozása során elhagyja a legfelső szintű kivétel kezelőjét. Ha meg kell határoznia a kilépési kódot, hogy a különböző típusú hibák megkülönböztethetők legyenek, például diagnosztikai célokra, vagy azért, mert bizonyos meghibásodási módoknak le kell fejezniük a feladatot, mások pedig nem, akkor ki kell lépnie a Futtatás () metódusból, ha nullától eltérő értéket ad vissza. kilépési kód. Ez lesz a feladat kilépési kódja.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kilépési kódok és kivételek a feladat-feldolgozó sablonban
A kilépési kódok és kivételek biztosítják a programok futtatásának eredményét, és segíthetnek azonosítani a program végrehajtásával kapcsolatos problémákat. A feladatütemezés sablonja a jelen szakaszban leírt kilépési kódokat és kivételeket implementálja.

A feladat-feldolgozó sablonnal megvalósított feladat-feldolgozó feladat három lehetséges kilépési kódot tud visszaadni:

| Kód | Leírás |
| --- | --- |
| [Process. ExitCode][process_exitcode] |A feladat processzora befejeződött. Vegye figyelembe, hogy ez nem jelenti azt, hogy a meghívott program sikeres volt – csak azt, hogy a feldolgozói feladat sikeresen megkezdődött, és kivételek nélkül hajtotta végre a feldolgozás utáni műveleteket. A kilépési kód jelentése a meghívott programtól függ – általában a 0. kilépési kód azt jelenti, hogy a program sikeres volt, és minden más kilépési kód azt jelenti, hogy a program meghiúsult. |
| 1 |A feladat processzora nem tudott kivételt a program "várt" részében. A kivételt a diagnosztikai információkra fordították le `TaskProcessorException` , és ahol lehetséges, a hiba megoldására vonatkozó javaslatokat. |
| 2 |A feladat processzora váratlan kivétel miatt meghiúsult. A kivétel a standard kimenetre lett naplózva, de a feladat-feldolgozó nem tudta felvenni a további diagnosztikai vagy szervizelési információkat. |

> [!NOTE]
> Ha a meghívó program az 1. és a 2. kód kilépésével jelzi bizonyos meghibásodási módokat, az 1. és 2. kilépési kódok használata nem egyértelmű. Ezeket a feladatsor-hibakódokat a megkülönböztető kilépési kódokra módosíthatja a Program.cs fájlban lévő kivételi esetek szerkesztésével.
> 
> 

A kivételek által visszaadott összes információ az StdOut. txt és a stderr. txt fájlba íródik. További információ: hibakezelés, a Batch dokumentációjában.

### <a name="client-considerations"></a>Ügyfelekkel kapcsolatos megfontolások
**Tárolási hitelesítő adatok**

Ha a feladattípus az Azure Blob Storage-t használja a kimenetek megőrzésére, például a file Conventions Helper Library használatával, akkor  hozzá kell férnie a Felhőbeli Storage-fiók hitelesítő adataihoz *vagy* egy blob-tároló URL-címéhez, amely megosztott hozzáférést tartalmaz aláírás (SAS). A sablon a hitelesítő adatok általános környezeti változókon keresztül történő biztosításának támogatását tartalmazza. Az ügyfél a következő módon adhatja át a tárolási hitelesítő adatokat:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A Storage-fiók ezután a `_configuration.StorageAccount` tulajdonságon keresztül érhető el a TaskProcessor osztályban.

Ha olyan tároló-URL-címet szeretne használni, amely SAS-t használ, akkor a feladat általános környezet beállításával is átadhatja ezt a beállítást, de a feladat-feldolgozó sablon jelenleg nem tartalmaz beépített támogatást ehhez.

**Tároló beállítása**

Azt javasoljuk, hogy az ügyfél vagy a Feladatkezelő feladat hozzon létre a tevékenységek által megkövetelt tárolókat, mielőtt hozzáadja a feladatokat a feladathoz. Ez akkor kötelező, ha a tároló URL-címét SAS-vel használja, mivel az URL-cím nem tartalmazza a tároló létrehozásához szükséges engedélyt. Akkor is ajánlott, ha a Storage-fiók hitelesítő adatait adja meg, mert minden feladatot ment a tárolón a CloudBlobContainer. CreateIfNotExistsAsync hívására.

## <a name="pass-parameters-and-environment-variables"></a>Paraméterek és környezeti változók továbbítása
### <a name="pass-environment-settings"></a>Környezeti beállítások továbbítása
Az ügyfél környezeti beállítások formájában adhatja át az adatokat a Feladatkezelő feladatnak. Ezt az információt a Feladatkezelő feladata használhatja a számítási feladat részeként futtatandó feladat-feldolgozó feladatok létrehozásakor. A környezeti beállításoknak átadható információk például a következők:

* A Storage-fiók neve és a fiók kulcsa
* Batch-fiók URL-címe
* Batch-fiók kulcsa

A Batch szolgáltatás egyszerű mechanizmussal rendelkezik a környezeti beállítások egy Feladatkezelő feladathoz való átadásához `EnvironmentSettings` a [Microsoft. Azure. Batch. JobManagerTask][net_jobmanagertask]tulajdonságának használatával.

A Batch-fiókhoz tartozó `BatchClient` példány beszerzéséhez például a Batch-fiók URL-címét és a megosztott kulcs hitelesítő adatait adhatja át az ügyfél kódjában. Hasonlóképpen, a Batch-fiókhoz csatolt Storage-fiók eléréséhez adja át a Storage-fiók nevét és a Storage-fiók kulcsát környezeti változókként.

### <a name="pass-parameters-to-the-job-manager-template"></a>Paraméterek továbbítása a Feladatkezelő sablonba
Sok esetben hasznos lehet a feladat-felügyeleti feladathoz tartozó feladatok átadása a Feladatkezelő tevékenység számára, vagy a feladat felosztási folyamatának szabályozása vagy a feladat feladatainak konfigurálása. Ezt úgy teheti meg, hogy feltölt egy Parameters. JSON nevű JSON-fájlt a Feladatkezelő feladathoz tartozó erőforrás-fájlként. A paraméterek ezután elérhetővé `JobSplitter._parameters` válnak a Feladatkezelő sablon mezőjében.

> [!NOTE]
> A beépített paraméter-kezelő csak a karakterláncok közötti szótárakat támogatja. Ha az összetett JSON-értékeket paraméter-értékként kívánja átadni, ezeket karakterláncként kell átadnia, és elemezni kell őket a feladatok elválasztójában, `Configuration.GetJobParameters` vagy módosítania kell a keretrendszer metódusát.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Paraméterek átadása a feladathoz tartozó processzor sablonjának
Paramétereket is átadhat a feladat-feldolgozó sablon használatával megvalósított egyes feladatokhoz. Csakúgy, mint a Feladatkezelő sablonnal, a feladat processzor-sablonja a következő nevű erőforrásfájl-fájlt keresi:

Parameters. JSON, és ha megtalálta, betölti a paramétereket tartalmazó szótárt. A következő lehetőségek közül választhat, hogy miként lehet paramétereket átadni a feladat-feldolgozó feladatainak:

* Használja újra a JSON-feladatok paramétereit. Ez jól működik, ha az egyetlen paraméter a feladatok széles skálája (például a renderelés magassága és szélessége). Ennek elvégzéséhez a CloudTask létrehozásakor vegyen fel egy hivatkozást a Task Manager feladat ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) és a CloudTask ResourceFiles-gyűjteményéhez tartozó Parameters. JSON erőforrásfájl-objektumra.
* Feladat-specifikus paraméterek. JSON-dokumentum létrehozása és feltöltése a feladat-elválasztó végrehajtásának részeként, valamint a feladat erőforrásfájl-gyűjteményében lévő blob hivatkozása. Erre akkor van szükség, ha a különböző tevékenységek különböző paraméterekkel rendelkeznek. Ilyen lehet például egy 3D megjelenítési forgatókönyv, amelyben a rendszer paraméterként továbbítja a frame indexet a feladatnak.

> [!NOTE]
> A beépített paraméter-kezelő csak a karakterláncok közötti szótárakat támogatja. Ha az összetett JSON-értékeket paraméter-értékként kívánja átadni, ezeket karakterláncként kell átadnia, és elemezni kell őket a feldolgozói feladatban, vagy módosítania kell a keretrendszer `Configuration.GetTaskParameters` metódusát.
> 
> 

## <a name="next-steps"></a>További lépések
### <a name="persist-job-and-task-output-to-azure-storage"></a>Feladat és feladat kimenetének megőrzése az Azure Storage-ban
A Batch-megoldások fejlesztésének egy másik hasznos eszköze [Azure batch fájl konvenciói][nuget_package]. Használja ezt a .NET-osztályt (jelenleg előzetes verzióban) a Batch .NET-alkalmazásaiban, így egyszerűen tárolhatja és lekérheti a tevékenységek kimeneteit az Azure Storage-ba és az-ból. A [Azure batch feladat és a feladat kimenetének](batch-task-output.md) megőrzése a könyvtár és a használat teljes körű megvitatását tartalmazza.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
