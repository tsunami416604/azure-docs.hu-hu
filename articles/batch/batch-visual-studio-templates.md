---
title: "Indítsa el a Visual Studio projektsablonjai - Azure Batch megoldások készítésével |} Microsoft Docs"
description: "Ismerje meg, hogyan Visual Studio projektsablonjai segítségével valósítja meg, és futtassa a számítási-igényes munkaterhelések Azure Batch."
services: batch
documentationcenter: .net
author: fayora
manager: timlt
editor: 
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: da77ce827c65deb18d9d84ce5cf768d89788e205
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Visual Studio projektsablonjai segítségével jump-start kötegelt megoldások

A **Feladatkezelő** és **feladat processzor Visual Studio sablonok** köteg adja meg a kódot valósítja meg, és futtassa a számítási-igényes munkaterhelések kötegelt a legkisebb erőfeszítéssel alapján. Ez a dokumentum ismerteti ezeket a sablonokat és útmutatást is használhatja őket.

> [!IMPORTANT]
> Ez a cikk ismerteti, amelyek csak ez a két sablon vonatkozó információkat, és feltételezi, hogy Ön ismeri a Batch szolgáltatás és a hozzá kapcsolódó főbb fogalmait a:-készletek, a számítási csomópontok, feladatok és feladatokat, feladat manager feladatok, környezeti változók és más releváns információ. További információt a [alapjai az Azure Batch](batch-technical-overview.md), [Batch funkcióinak áttekintése a fejlesztők](batch-api-basics.md), és [Ismerkedés az Azure Batch könyvtár a .NET-hez](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Áttekintés
A Feladatkezelő és a feladat processzor sablonok segítségével hozzon létre két hasznos összetevői:

* Egy feladat manager feladat, amely egy, amely képes felosztása a feladat több feladat is önállóan, párhuzamosan futó feladat elválasztó.
* Egy feladat processzor előzetesen feldolgozni, és egy alkalmazás parancs sora környékén utófeldolgozási végrehajtásához használható.

Például movie megjelenítés esetén, a feladat elválasztó volna ikonná egy egyetlen movie feladat több száz vagy ezer a különböző feladatokhoz, amelyek feldolgozzák egyes keretek külön-külön kellene. Ennek megfelelően a feladat processzor volna el a megjelenítési alkalmazás és minden függő folyamatok, amelyek szükségesek ahhoz, hogy mindegyik leképezési keret, valamint minden további műveleteket (például a megjelenített keret tárolóhelyre másolása).

> [!NOTE]
> A Feladatkezelő és a feladat processzor sablonok függetlenek egymástól, dönthet úgy is, vagy csak az egyik, a számítási feladatok és preferenciák követelményeitől függően.
> 
> 

Ahogy az alábbi ábrán is látható, a számítási feladat által használt ezeket a sablonokat végighaladnia három fázisból áll:

1. Az Ügyfélkód (pl. alkalmazások, webes szolgáltatás, stb.) küldi el egy feladatot, amely a Batch szolgáltatás a Azure-ban, a Feladatkezelő tevékenység a feladat manager program megadása.
2. A Batch szolgáltatás a feladat manager feladatot futtatja egy számítási csomóponton, és a feladat elválasztó a elindítja a megadott számú feladat processzor feladatok, a különböző számítási csomópontokkal szükséges, a paraméterek és a feladat elválasztó kód specifikációk alapján.
3. A feladat processzor feladatok egymástól függetlenül, a bemeneti adatok feldolgozása és a kimeneti adatok létrehozása ezzel párhuzamosan futnak.

![Hogyan kommunikál az ügyfélkód a Batch szolgáltatás ábrája][diagram01]

## <a name="prerequisites"></a>Előfeltételek
A kötegelt sablonok használatához a következőkre lesz szüksége:

* Egy számítógép, amelyen telepítve a Visual Studio 2015. Kötegelt sablonok jelenleg csak a Visual Studio 2015 esetében támogatott.
* A kötegelt sablonok, amely elérhető a [Visual Studio galériában] [ vs_gallery] Visual Studio kiterjesztéseket. A sablonok beszerzését két módja van:
  
  * A sablonok használatával telepítse a **bővítmények és frissítések** párbeszédpanel a Visual Studio (további információkért lásd: [keresés és a Visual Studio-bővítmények használatával][vs_find_use_ext]). Az a **bővítmények és frissítések** párbeszédpanel mezőben keressen, és töltse le a következő két kiterjesztések:
    
    * A feladat elválasztó Azure Batch Feladatkezelő
    * Az Azure Batch feladat processzor
  * A sablonok letöltésére az online katalógusból a Visual Studio: [Projektsablonjai a Microsoft Azure Batch][vs_gallery_templates]
* Ha szeretné használni a [alkalmazáscsomagok](batch-application-packages.md) beállítást, a Feladatkezelő telepítéséhez és a kötegelt feladat processzor számítási csomópontok, össze kell kapcsolni egy tárfiókot a Batch-fiókhoz.

## <a name="preparation"></a>Előkészítése
Azt javasoljuk, hogy a Feladatkezelő, valamint a feladat processzor tartalmazó megoldás létrehozásával, mert ez segítségével egyszerűbb megosztani a kód a Feladatkezelőt és a feladat feldolgozó programok között. Ez a megoldás létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio, és válassza ki **fájl** > **új** > **projekt**.
2. A **sablonok**, bontsa ki a **egyéb projekttípusok**, kattintson **Visual Studio megoldás**, majd válassza ki **üres megoldás**.
3. Írja be az alkalmazás és a megoldás (például "LitwareBatchTaskPrograms") célját leíró nevet.
4. Az új megoldás létrehozásához kattintson a **OK**.

## <a name="job-manager-template"></a>Feladatkezelő sablon
A Feladatkezelő sablon segítségével valósítja meg a kezelő feladata, hogy a következő műveleteket hajthatja végre:

* A feladatok felosztása több feladat.
* Küldje el ezeket a feladatokat a kötegelt futtatásához.

> [!NOTE]
> Feladat manager feladatokkal kapcsolatos további információkért lásd: [Batch funkcióinak áttekintése a fejlesztők](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>A feladat-kezelőt a sablon létrehozása
A Feladatkezelő hozzáadása a megoldás, amely a korábban létrehozott, kövesse az alábbi lépéseket:

1. Nyissa meg a meglévő megoldást a Visual Studio.
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, kattintson a **Hozzáadás** > **új projekt**.
3. A **Visual C#**, kattintson a **felhő**, és kattintson a **Azure Batch Job Manager használatát a feladat elválasztó**.
4. Adjon meg egy nevet, amely ismerteti az alkalmazást, és úgy azonosítja a projektet, a Feladatkezelő (pl. "LitwareJobManager").
5. A projekt létrehozásához kattintson a **OK**.
6. Végezetül fordítsa le a projekt azzal kényszerítheti a Visual Studio betölteni az összes hivatkozott NuGet-csomagok, és győződjön meg arról, hogy a projekt érvényes módosításához megkezdése előtt.

### <a name="job-manager-template-files-and-their-purpose"></a>Feladatkezelő sablonfájlokat és célját
A Feladatkezelő sablonnal projekt létrehozásakor három fájlcsoportok kódot állít elő:

* A fő programfájl (Program.cs). A program a belépési pont és a legfelső szintű kivételkezelést tartalmazza. Ezt általában nem szükséges.
* A keretrendszer könyvtára. Ez a feladat manager program – a paraméterek, a a kötegelt feladatok hozzáadása kicsomagolása "bolierplate" dolgozott felelős fájlokat tartalmazza. Módosítani ezeket a fájlokat általában nem szükséges.
* A feladat elválasztó fájlt (JobSplitter.cs). Ez azért, ahol az alkalmazás-specifikus logika vágását meghatározó egy feladatot a feladatok helyezzük el.

Természetesen a feladat elválasztó kódot, attól függően, hogy a feladat a felosztás logika összetettsége támogatásához szükséges további fájlokat is hozzáadhat.

A sablon is létrehoz a szabványos .NET project fájlok például .csproj fájlhoz, app.config, packages.config, stb.

A többi Ez a szakasz ismerteti a különböző fájlok és a kód szerkezete, és ismerteti a osztályra funkciója.

![A Visual Studio Megoldáskezelőben a Feladatkezelő sablon megoldás][solution_explorer01]

**Keretrendszer fájlok**

* `Configuration.cs`: A feladat konfigurációs adatok, például a Batch-fiók adatait, a csatolt tárfiók hitelesítő adatainak, a feladat és a feladat adatainak és a feladat paramétereit betöltésének magában foglalja. Kötegelt által definiált környezeti változók (lásd a feladatokat, a Batch-dokumentáció környezeti beállításai) a Configuration.EnvironmentVariable osztály keresztül hozzáférést is biztosít.
* `IConfiguration.cs`: A Configuration osztály végrehajtásának kivonatolja a egységteszt a feladat elválasztó egy hamis vagy utánzatait konfigurációs objektuma használja.
* `JobManager.cs`: A feladat manager program összetevői koordinálja. Ez felelős inicializálása a feladat elválasztó, a feladat elválasztó meghívása, és a feladatokat, a feladat küldőtől a feladat elválasztó által visszaadott terjesztéséhez.
* `JobManagerException.cs`: A Feladatkezelő leáll igénylő hiba jelöli. JobManagerException burkolása "várt" hibák, ahol a diagnosztikai információk lezárást részeként megadható szolgál.
* `TaskSubmitter.cs`: Ez az osztály felelős való hozzáadásakor a kötegelt a feladat elválasztó által visszaadott feladatok. A JobManager osztály összesítések kötegek hatékony, de időben történő betöltésére ide a feladatot a feladatok sorozatát majd meghívja a TaskSubmitter.SubmitTasks a háttérszálon az egyes kötegekben.

**Feladat elválasztó**

`JobSplitter.cs`: Ez az osztály tartalmazza az alkalmazás-specifikus logika vágását meghatározó a feladatot a feladatok. A keretrendszer meghívja az beszerzése egy feladatot, amely hozzáadja a feladathoz, a metódus visszaadja azokat feladatütemezési JobSplitter.Split metódus. Ez az osztály hol fogja tölteni a feladat a programot. Valósítja meg a vegyes vissza a feladatokat, amelybe a feladat particionálásához képviselő CloudTask példányok sorozata.

**Standard szintű .NET parancssori project fájlok**

* `App.config`: Standard .NET alkalmazás konfigurációs fájljában.
* `Packages.config`: Standard NuGet csomag függőségi fájlt.
* `Program.cs`: A program a belépési pont és a legfelső szintű kivételkezelést tartalmazza.

### <a name="implementing-the-job-splitter"></a>A feladat elválasztó megvalósítása
A Feladatkezelő sablonprojekt megnyitásakor a projekt alapértelmezés szerint a JobSplitter.cs fájlt fog rendelkezni. A Split() módszer megjelenítése az alábbi használatával a feladatok felosztása logikáját a terhelést a valósíthatja meg:

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
> A jegyzettel ellátott részt a `Split()` módszer a következő: a Feladatkezelő sablon kódot, amelyet logika hozzáadásával módosíthatja a feladatok ossza fel a különböző feladatok csak szakasza. Ha a sablon egy másik szakasz módosítani szeretné, győződjön meg arról, amelyek familiarized kötegelt működése, a és néhány kipróbálhatja a [Batch-Kódminták][github_samples].
> 
> 

A Split() megvalósítás hozzáféréssel rendelkezik:

* A feladat paramétereit keresztül a `_parameters` mező.
* A feladat képviselő keresztül CloudJob objektum a `_job` mező.
* A feladat-kezelő feladat képviselő keresztül CloudTask objektum a `_jobManagerTask` mező.

A `Split()` megvalósítása nem kell közvetlenül tevékenységek hozzáadása a feladatot. Ehelyett a kód CloudTask objektumok sorozatát kell visszaadnia, és ezek nem kerülnek be a feladat automatikusan által a feladat elválasztó meghívása keretrendszer osztályait. Gyakori megoldás, hogy a C# tartozó iterátor (`yield return`) szolgáltatás feladat elválasztókat végrehajtásához, mivel ez teszi lehetővé a feladatokat, hogy fut a lehető leghamarabb, nem pedig ki kell számítani az összes tevékenység vár.

**A feladat elválasztó sikertelen**

Ha a feladat elválasztó hibát észlel, azt kell vagy:

* Állítsa le a folyamat a C# `yield break` utasítást, amelyben a Feladatkezelő eset kezeli a program sikeres; vagy
* Kivételt jelez, a Feladatkezelő számít eset nem sikerült és attól függően, hogy az ügyfél konfigurálta, előfordulhat, hogy ismételhető).

Mindkét esetben bármilyen már a feladat elválasztó által visszaadott, és hozzáadja a kötegelt feladatok futtatására alkalmas lesz. Ha nem szeretné, hogy ez, majd meg a következőket teheti:

* A feladat leáll a feldolgozás elválasztó visszatérése előtt
* A feladat teljes gyűjteményhez való visszaküldés előtt állítson össze (Ez azt jelenti, hogy vissza egy `ICollection<CloudTask>` vagy `IList<CloudTask>` helyett a feladat felosztó használatával egy C# iterátor megvalósítása)
* Feladat függőségek segítségével minden feladat függ, a Feladatkezelő sikeres befejezése

**Feladat manager újrapróbálkozások**

A Feladatkezelő nem sikerül, ha ez lehetséges, hogy újra megpróbálja a Batch szolgáltatás attól függően, hogy az ügyfél újrapróbálkozási beállításainak. Ez általában biztonságos, mert a keretrendszer feladatok ad hozzá a feladatot, akkor figyelmen kívül hagyja minden feladatot, amely már létezik. Azonban ha feladatok kiszámítása költséges, előfordulhat, hogy nem kívánja fel Önnek a feladatokat, amelyek a feladat már hozzáadott újraszámítása költsége Ezzel szemben ha ismételt futtatása nem biztos, hogy ugyanazon feladathoz azonosítók készítése majd a "Mellőzés ismétlődő" viselkedését fogja nem indítsa. Ezekben az esetekben akkor tervezzen a feladat elválasztó már megtörtént, és ismétlődik, például hogy feladatok megkezdése előtt egy CloudJob.ListTasks elvégzésével munkáját észleléséhez.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Olyan kilépési kódot és a Feladatkezelő sablonban kivételek
Olyan kilépési kódot, és kivételeket egy olyan mechanizmus biztosítása meghatározására a program futtatása eredményeit, és segíthetnek a program végrehajtásának problémákat azonosítása. A Feladatkezelő sablon valósítja meg a kilépési kódot és a jelen szakaszban ismertetett kivételek.

A Feladatkezelő sablonnal megvalósítása manager feladat visszatérhessen három lehetséges kilépési kód:

| Kód | Leírás |
| --- | --- |
| 0 |A feladat-kezelő sikeresen befejeződött. A feladat elválasztó kódot befejezési futott, és a feladat bekerült az összes feladat. |
| 1 |A kezelő feladata a program "várt" részében kivételhiba miatt sikertelen. A kivétel volt lefordítani a diagnosztikai adatokkal JobManagerException és, ha lehetséges, a hiba elhárításához javaslatokat. |
| 2 |A kezelő feladat egy "nem várt" kivétel miatt sikertelen volt. A kivétel a normál a kimenetbe naplózott, de a Feladatkezelő nem tudta hozzáadni a további diagnosztikai vagy javítási adatokat. |

Feladat manager feladat sikertelensége esetén néhány feladatot előfordulhat, hogy továbbra is érhetőek el a szolgáltatást a hiba bekövetkezése előtt. Ezek a feladatok szokásos módon indul el. "Feladat elválasztó sikertelen" fent látható kód elérési út leírását.

Kivételek által visszaadott összes információt stdout.txt és stderr.txt fájlok íródtak. További információkért lásd: [hiba kezelése](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Az ügyfelek szempontjai
Ez a szakasz ismerteti a végrehajtása néhány ügyfélkövetelmények, a Feladatkezelő, ez a sablon alapján való meghívásakor. Lásd: [hogyan paraméterek és a környezeti változók adhatók át az Ügyfélkód](#pass-environment-settings) paraméterek-környezet beállításait, és leírását.

**Kötelező hitelesítő adatok**

Ahhoz, hogy az Azure kötegelt feladatok hozzáadásához a feladat manager feladathoz az Azure Batch-fiók URL-címe és a kulcsot. Át kell adnia ezeket a környezeti változók neve YOUR_BATCH_URL és YOUR_BATCH_KEY. Meg a Feladatkezelő tevékenység környezeti beállításokat. Például a C# ügyfél:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Tároló hitelesítő adatait**

Általában az ügyfél nem kell megadnia a csatolt tárfiók hitelesítő adatainak a feladat manager feladathoz (a) a legtöbb feladatot kezelők nem kell explicit módon hozzáférni a kapcsolt tárfiókra, és (b) a kapcsolt tárfiókra gyakran megadott összes feladatot, mert egy a feladat általános környezet beállítása. Ha nem tartalmazza a kapcsolt tárfiókra keresztül környezet általános beállításait, és a Feladatkezelő csatolt tároló hozzáférésre van szüksége, majd meg kell adnia a csatolt tároló hitelesítő adatokat az alábbiak szerint:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Feladat manager feladatbeállítások**

Az ügyfél kell beállítania a Feladatkezelő *killJobOnCompletion* jelzőt **hamis**.

Az ügyfél beállítása általában biztonságosan *runExclusive* való **hamis**.

Az ügyfélnek használnia kell a *resourceFiles* vagy *applicationPackageReferences* gyűjtemény szeretné, hogy a feladat végrehajtható kezelő (és a szükséges DLL-ek) központilag telepített a számítási csomópont.

Alapértelmezés szerint a Feladatkezelő nem úja sikertelen. Attól függően, hogy a feladat manager programot, az ügyfél előfordulhat, hogy engedélyezni szeretné az újrapróbálkozások keresztül *megkötések*/*maxTaskRetryCount*.

**Feladatbeállítások**

Ha a feladat elválasztó függőségekkel rendelkező feladatok bocsát ki, az ügyfél a feladat usesTaskDependencies kell értéke igaz.

A feladat elválasztó modellben szokatlan, az ügyfelek számára a feladatok bővítéséhez feladatok felül a feladat elválasztó hoz létre. Az ügyfél ezért kell megfelelően beállítva a feladat *onAllTasksComplete* való **terminatejob**.

## <a name="task-processor-template"></a>Processzor sablon
Egy feladat processzor sablon valósítja meg a következő műveleteket tudják végrehajtani a feladat processzor nyújt segítséget:

* Állítson be minden kötegelt feladat futtatásához szükséges adatokat.
* Minden kötegelt feladat által igényelt összes műveletek futtatása.
* Mentse a feladat kimenetének állandó tároló.

Bár egy feladat processzor nem szükséges a kötegelt feladatok futtatásához, a feladat processzor használatának legfontosabb előnye, biztosítja a burkoló egy helyen az összes feladatütemezés végrehajtási műveletek végrehajtásához. Ha például minden feladat keretében több alkalmazás futtatásához szükséges, vagy ha másolja adatok állandó tároló befejezése után minden feladat.

A feladat-feldolgozó által végrehajtott műveletekről lehet egyszerű vagy összetett, és annyi, vagy kevés a munkaterhelés szerint szükség szerint. Emellett egy feladat processzor az összes feladat műveletek alkalmazásával könnyen frissítése vagy is műveletek alkalmazások és munkaterhelések követelményeinek végbement változások alapján. Azonban bizonyos esetekben egy feladat processzor előfordulhat, hogy nem lehet az ideális megoldás a példányhoz, adhat hozzá a szükségtelen összetettségét, például amikor fut, amely egy egyszerű parancssorból gyorsan indítható feladatok.

### <a name="create-a-task-processor-using-the-template"></a>Hozzon létre egy feladat processzor, a sablon használatával
Egy feladat processzor hozzáadása a megoldás, amely a korábban létrehozott, kövesse az alábbi lépéseket:

1. Nyissa meg a meglévő megoldást a Visual Studio.
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, kattintson a **Hozzáadás**, és kattintson a **új projekt**.
3. A **Visual C#**, kattintson a **felhő**, és kattintson a **Azure kötegelt feladat processzor**.
4. Adjon meg egy nevet, amely ismerteti az alkalmazást, és úgy azonosítja a projektet, a feladat processzor (pl. "LitwareTaskProcessor").
5. A projekt létrehozásához kattintson a **OK**.
6. Végezetül fordítsa le a projekt azzal kényszerítheti a Visual Studio betölteni az összes hivatkozott NuGet-csomagok, és győződjön meg arról, hogy a projekt érvényes módosításához megkezdése előtt.

### <a name="task-processor-template-files-and-their-purpose"></a>Feladat processzor sablonfájlokat és célját
A feladat processzor sablonnal projekt létrehozásakor három fájlcsoportok kódot állít elő:

* A fő programfájl (Program.cs). A program a belépési pont és a legfelső szintű kivételkezelést tartalmazza. Ezt általában nem szükséges.
* A keretrendszer könyvtára. Ez a feladat manager program – a paraméterek, a a kötegelt feladatok hozzáadása kicsomagolása "bolierplate" dolgozott felelős fájlokat tartalmazza. Módosítani ezeket a fájlokat általában nem szükséges.
* A feladat processzor fájlt (TaskProcessor.cs). Ez az meg, ahová a az alkalmazás-specifikus logika egy feladat végrehajtása (általában által létesítő egy létező végrehajtható fájl) a rendszer. Előtti és utáni feldolgozási kódok, például a további adatok letöltése vagy eredmény fájlok feltöltése is ide kerül.

Természetesen a processzor feladatkód, attól függően, hogy a feladat a felosztás logika összetettsége támogatásához szükséges további fájlokat is hozzáadhat.

A sablon is létrehoz a szabványos .NET project fájlok például .csproj fájlhoz, app.config, packages.config, stb.

A többi Ez a szakasz ismerteti a különböző fájlok és a kód szerkezete, és ismerteti a osztályra funkciója.

![A Visual Studio Megoldáskezelőben a feladat processzor sablon megoldás][solution_explorer02]

**Keretrendszer fájlok**

* `Configuration.cs`: A feladat konfigurációs adatok, például a Batch-fiók adatait, a csatolt tárfiók hitelesítő adatainak, a feladat és a feladat adatainak és a feladat paramétereit betöltésének magában foglalja. Kötegelt által definiált környezeti változók (lásd a feladatokat, a Batch-dokumentáció környezeti beállításai) a Configuration.EnvironmentVariable osztály keresztül hozzáférést is biztosít.
* `IConfiguration.cs`: A Configuration osztály végrehajtásának kivonatolja a egységteszt a feladat elválasztó egy hamis vagy utánzatait konfigurációs objektuma használja.
* `TaskProcessorException.cs`: A Feladatkezelő leáll igénylő hiba jelöli. TaskProcessorException burkolása "várt" hibák, ahol a diagnosztikai információk lezárást részeként megadható szolgál.

**A feladat processzor**

* `TaskProcessor.cs`: A feladat fut. A keretrendszer meghívja a TaskProcessor.Run metódust. Ez az adott fogja tölteni a feladat az alkalmazás-specifikus logika. Valósítsa meg a Futtatás metódust:
  * Elemzése és a tevékenység paramétereket ellenőrzése
  * A meghívni kívánt külső program parancssorának összeállítása
  * A diagnosztikai adatok lehet szükség a hibakereséshez
  * A folyamat a parancssor használatával
  * Várjon, amíg a folyamat leállását
  * A kilépési kódot annak meghatározásához, hogy sikeres volt, vagy nem sikerült a folyamat rögzítése
  * Mentse a kimeneti fájlokat szeretné megőrizni az állandó tároló

**Standard szintű .NET parancssori project fájlok**

* `App.config`: Standard .NET alkalmazás konfigurációs fájljában.
* `Packages.config`: Standard NuGet csomag függőségi fájlt.
* `Program.cs`: A program a belépési pont és a legfelső szintű kivételkezelést tartalmazza.

## <a name="implementing-the-task-processor"></a>A feladat processzor megvalósítása
A feladat processzor sablonprojekt megnyitásakor a projekt alapértelmezés szerint a TaskProcessor.cs fájlt fog rendelkezni. A munkaterhelésnek a feladatok futtatása logikáját valósíthatja meg a Run() metódussal a lent látható módon:

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
> A jegyzettel ellátott szakasz Run() metódus módosíthatja a feladatok futtatása logikáját hozzáadásával a munkaterhelés számára készült feladat processzor sablon kódot csak része. A sablon egy másik szakasz módosítani szeretné, ha adjon először megismerje a kötegelt működése a kötegelt dokumentációja, illetve próbálhatja ki a kötegelt mintakódok néhány által.
> 
> 

A Run() metódus felelős kezdődően egy vagy több folyamatok, az összes folyamat befejezésére történő várakozáskor befejeződik, a parancssor megnyitása az eredmények mentése, és végül a kilépési kóddal vissza. A Run() módszer, ahol a feldolgozó logika megvalósítása a feladatokhoz. A feladat processzor keretrendszer Run() metódus hív meg; nem kell neki magának.

A Run() megvalósítás hozzáféréssel rendelkezik:

* A tevékenység-paraméterek segítségével a `_parameters` mező.
* A feladat- és azonosító keresztül a `_jobId` és `_taskId` mezőket.
* A feladat konfigurációja keresztül a `_configuration` mező.

**A feladat sikertelen**

Hiba, esetén kiléphet a Run() metódus által egy kivétel kiváltása, de ez hagyja a legfelső szintű kivételkezelő ellenőrzése a feladat kilépési kódot. Ha a kilépési kód szabályozhatja, hogy a különböző típusú hiba, például képes megkülönböztetni, diagnosztikai célokra, vagy mert függően néhány kell leállítani a feladatot, és mások számára nem kell módosítania, majd kell kilép a Run() metódus vissza egy nem nulla a kilépési kód. Ez lesz a feladat kilépési kódot.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Olyan kilépési kódot és a feladat processzor sablonban kivételek
Olyan kilépési kódot, és kivételeket egy olyan mechanizmus biztosítása meghatározására a program futtatása eredményeit, és segíthetnek, hogy a program végrehajtásának problémákat azonosítása. A feladat processzor sablon valósítja meg a kilépési kódot és a jelen szakaszban ismertetett kivételek.

Egy feladat processzor feladat feladat processzor sablonnal megvalósított visszatérhessen három lehetséges kilépési kód:

| Kód | Leírás |
| --- | --- |
| [Process.ExitCode][process_exitcode] |A feladat processzor befejezési futott. Vegye figyelembe, hogy ez nem feltétlenül jelenti azt, hogy a program indításakor sikeres volt – csak, hogy a feladat-feldolgozó sikeresen elindítva, és végre semmilyen utófeldolgozás kivételek nélkül. A kilépési kód pontos jelentése attól függ a meghívott programmal – általában kilépési kód 0 azt jelenti, hogy a program sikeres volt, és bármely más kilépési kód azt jelenti, hogy a program futása meghiúsult. |
| 1 |A feladat processzor a program "várt" részében kivételhiba miatt sikertelen. A kivétel lefordítani a `TaskProcessorException` diagnosztikai információkat, és ha lehetséges, javaslatokat a hiba elhárításához. |
| 2 |A feladat processzor "Váratlan" kivételhiba miatt sikertelen. A kivétel a normál a kimenetbe naplózott, de a feladat-feldolgozó nem tudta hozzáadni a további diagnosztikai vagy javítási adatokat. |

> [!NOTE]
> Ha a program indításakor 1 és 2 kilépési kód jelzi az adott meghibásodás használ, a feladat processzor hibákat 1 és 2 kilépési kód használata nem egyértelmű. A feladat processzor hibakódok a kivétel esetekben a Program.cs fájl szerkesztésével módosíthatja megkülönböztető kilépési kódot.
> 
> 

Kivételek által visszaadott összes információt stdout.txt és stderr.txt fájlok íródtak. További információ: hiba kezelése, a Batch-dokumentáció.

### <a name="client-considerations"></a>Az ügyfelek szempontjai
**Tároló hitelesítő adatait**

Ha a feladat processzor használja az Azure blob storage megőrizni a kimenet, például a fájl egyezmények segítő szalagtár használata esetén, akkor azt hozzá kell férnie *vagy* a felhő tárfiók hitelesítő adatainak *vagy* blob tároló URL-cím tartalmaz egy közös hozzáférésű jogosultságkód (SAS). A sablon közös környezeti változók hitelesítő adatokat biztosító támogatását is magában foglalja. Az ügyfél az alábbiak szerint a tároló hitelesítő adatait adja át:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A tárfiók majd érhető el a TaskProcessor osztály keresztül a `_configuration.StorageAccount` tulajdonság.

Ha inkább a SAS használatával tároló URL-címet használja, is átadhatja a feladat közös környezeti beállításokat keresztül, de a feladat processzor sablon jelenleg nem tartalmaz ez beépített támogatása.

**Tárolás beállítása**

Javasoljuk, hogy az ügyfél vagy a feladatot manager feladat létrehozása a feladatok a feladatok hozzáadása a projekthez szükséges tárolókkal. Ez a kötelező SAS tároló URL-címet használja, ha ilyen URL-címet az engedély nem vonatkozik a tároló létrehozása. Minden tevékenység CloudBlobContainer.CreateIfNotExistsAsync felhívását tárolón menti, ajánlott akkor is, ha a tárfiók hitelesítő adatait, adja meg.

## <a name="pass-parameters-and-environment-variables"></a>Paraméterek és a környezeti változók
### <a name="pass-environment-settings"></a>Pass-környezet beállításaiban
Egy ügyfél is át adatokat a kezelő feladat környezeti beállítások formájában. Ez az információ majd használhatják a kezelő feladat a számítási feladat részeként futó feladat processzor feladatok létrehozásakor. A létrejövő átadhatók környezeti beállításokat, például a következők:

* Név és fiókkulcs tárfiókkulcsok
* Batch-fiók URL-címe
* Kötegelt fiókkulcs

A Batch szolgáltatás környezeti beállítások átadása egy kezelő feladat használatával egyszerű mechanizmussal rendelkezik a `EnvironmentSettings` tulajdonság [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Ahhoz például, hogy az beszerzése a `BatchClient` példány, a környezeti változókat az ügyféltől a kód az URL-cím és a megosztott kulcs hitelesítő adatai, a Batch-fiókhoz is át a Batch-fiókhoz. Hasonlóképpen a Batch-fiókhoz kapcsolódó tárfiók eléréséhez átadhatók a tárfiók nevét és a tárfiók hívóbetűjét környezeti változóként.

### <a name="pass-parameters-to-the-job-manager-template"></a>A Feladatkezelő sablont paraméterekkel
A legtöbb esetben célszerű Feladatonkénti paramétereket át a feladat manager feladat, a folyamat a felosztás feladat ellenőrzése vagy a feladatot a feladatok konfigurálása. Ehhez a parameters.json nevű kezelő feladata az erőforrás-fájlként JSON-fájl feltöltésével. A paraméterek tudja majd válnak elérhetővé a `JobSplitter._parameters` mezőt a Feladatkezelő sablonban.

> [!NOTE]
> A beépített paraméter kezelő csak karakterlánc-karakterlánc szótárak támogatja. Ha a paraméter értékeként összetett JSON-értéket átadni kívánt, szüksége lesz, ezek karakterláncként és a feladat elválasztó elemzett őket, vagy módosíthatja a keretrendszer `Configuration.GetJobParameters` metódust.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>A feladat processzor sablont paraméterekkel
A paramétereket is át az egyes feladatok megvalósítva, a feladat processzor sablonnal. Ahogy a feladat manager sablon, a processzor sablon keresi nevű erőforrásfájl

Parameters.JSON, és ha úgy találta, a paraméterek szótár betölti azt. Többféle arról, hogyan adhatók át a feladat processzor feladatok paraméterek közül:

* A feladat paramétereit JSON felhasználhatja. Ez a módszer jól, ha csak paraméterei feladat kiterjedő (például a leképezési magassága és szélessége) is. Ehhez a feladathoz elválasztó egy CloudTask létrehozásakor, a kezelő feladata ResourceFiles a parameters.json fájl objektum mutató hivatkozás hozzáadása (`JobSplitter._jobManagerTask.ResourceFiles`) a CloudTask ResourceFiles gyűjteményhez.
* Készítése és feladatspecifikus parameters.json dokumentum feltöltése feladatok elválasztó végrehajtásának részeként, és hivatkozni, hogy a blob, a feladat erőforrás fájlok gyűjteményben. Ez azért szükséges, ha a különböző feladatok más paraméterekkel rendelkeznek. Például lehet egy 3D megjelenítés forgatókönyv, ahol a keret index átadott paraméterként a feladatot.

> [!NOTE]
> A beépített paraméter kezelő csak karakterlánc-karakterlánc szótárak támogatja. Ha a paraméter értékeként összetett JSON-értéket átadni kívánt, szüksége lesz, ezek karakterláncként és azokat a feladat processzor elemezni, vagy módosíthatja a keretrendszer `Configuration.GetTaskParameters` metódust.
> 
> 

## <a name="next-steps"></a>Következő lépések
### <a name="persist-job-and-task-output-to-azure-storage"></a>Feladat- és kimeneti Azure Storage megőrzése
Egy másik hasznos eszköz a kötegelt megoldás fejlesztési [Azure Batch fájl egyezmények][nuget_package]. A .NET osztálykönyvtár (jelenleg előzetes verzió) az Ön Batch .NET-alkalmazások segítségével könnyen tárolja, és Azure Storage érkező vagy oda irányuló feladat kimenetének beolvasása. [Azure Batch feladat- és kimeneti megőrzéséhez](batch-task-output.md) a szalagtár és a használati ismertetését tartalmazza.

### <a name="batch-forum"></a>Batch fórum
A [Azure Batch fórum] [ forum] az MSDN webhelyen van remek kötegelt tárgyalja, és kérdése van a szolgáltatás. Központi a keresztül a hasznos "kapcsolódó" bejegyzések, és a kötegelt megoldások létrehozása során felmerülő kérdéseit.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
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
