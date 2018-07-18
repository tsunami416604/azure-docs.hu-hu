---
title: A Visual Studio-sablonok – Azure Batch-megoldások létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan Visual Studio-projektsablonok segíthet megvalósítása és nagy számítási igényű számítási feladatok futtatása Azure Batch.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a44c249a957050afb500decd094183c71d6ca5e
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114096"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>A Visual Studio-projektsablonok segítségével gyorsan elindíthatja a Batch-megoldások

A **Feladatkezelő** és **feladat processzor Visual Studio-sablonok** Batch adja meg annak érdekében, hogy megvalósítása és nagy számítási igényű számítási feladatok futtatása a Batch a lehető legkevesebb erőfeszítés-kódot. Ez a dokumentum ismerteti ezeket a sablonokat, és útmutatást kínál a használatukat.

> [!IMPORTANT]
> Ez a cikk ismerteti, csak ez a két sablon vonatkozó információkat, és feltételezi, hogy Ön ismeri a Batch szolgáltatás és a hozzá kapcsolódó alapfogalmakat: készletek, számítási csomópontok, feladatok és a feladatok, Feladatkezelő tevékenységekről, a környezeti változók és más releváns információk. További információkat a [az Azure Batch alapjai](batch-technical-overview.md) és [Batch szolgáltatás áttekintése fejlesztők számára](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Áttekintés
A Feladatkezelő és a feladat processzor-sablonok segítségével két hasznos összetevőket hozhat létre:

* A feladatkezelői tevékenység, amely egy feladat felosztó tönkretehetik több feladatokra, amelyek egymástól függetlenül, párhuzamosan futtathatók egy feladatot, amely megvalósítja.
* Egy feladat processzor előtti és utáni feldolgozási egy alkalmazás parancs sora környékén végrehajtásához használható.

Például a film renderelési forgatókönyvekben a feladat felosztó lenne alakítsa egyetlen movie feladat több száz vagy ezer arról, hogy külön-külön kellene folyamat egyes keretek különböző feladatokhoz. Ennek megfelelően a feladat processzor lenne indítja el a renderelési alkalmazások, és minden függő folyamatokat, melyek szükségesek ahhoz, hogy mindegyik leképezési kerete, valamint végezze el a további műveleteket (például a renderelt kép a tárolóhely másolása).

> [!NOTE]
> A Feladatkezelő és a feladat processzor-sablonok függetlenek egymástól, így lehet váltani, mindkettő, vagy csak az egyiket, a számítási feladat és a saját beállítások követelményeitől függően.
> 
> 

Az alábbi ábrán látható, a számítási feladat, ezeket a sablonokat használó három lépésben hajtaná fog áthaladni:

1. Az Ügyfélkód (pl. alkalmazások, webszolgáltatás, stb.) elküld egy feladatot, amely a Batch szolgáltatás az Azure-ban, adja meg, a Feladatkezelő tevékenység a feladat manager programot.
2. A Batch szolgáltatás a Feladatkezelő tevékenység fut egy számítási csomóponton, és a feladat felosztó a betölti a megadott számú feladat processzor feladatokat, az a számos számítási csomópontokkal szükség szerint a paraméterek és a feladat felosztó kód specifikációk alapján.
3. A feladat processzor feladatok futtatása függetlenül, párhuzamosan dolgozza fel a bemeneti adatokat, és a kimeneti adatokat hozzon létre.

![Hogyan kommunikál az ügyfélkód a Batch szolgáltatás bemutató ábra.][diagram01]

## <a name="prerequisites"></a>Előfeltételek
A Batch-sablonjainak használatához a következőkre lesz szüksége:

* Egy számítógép, amelyen telepítve van a Visual Studio 2015. Batch-sablonok jelenleg csak a Visual Studio 2015 esetében támogatott.
* A Batch-sablonokat, amelyek érhetők el a [Visual Studio-galéria] [ vs_gallery] Visual Studio-bővítményként. A sablon beszerzését két módja van:
  
  * A sablonok használatával telepítse a **bővítmények és frissítések** párbeszédpanel a Visual Studióban (további információkért lásd: [keresés és a Visual Studio-bővítmények használatával][vs_find_use_ext]). Az a **bővítmények és frissítések** párbeszédpanel mezőbe, keresése, és töltse le a következő két bővítményeket:
    
    * Az Azure Batch Feladatkezelő a feladat felosztó
    * Az Azure Batch-feladat processzor
  * A sablonok letöltésére az online katalógusból a Visual Studióhoz: [a Microsoft Azure Batch projektsablonok][vs_gallery_templates]
* Ha azt tervezi, használja a [alkalmazáscsomagok](batch-application-packages.md) üzembe helyezéséhez a Feladatkezelő funkció és a processzor-feladat a Batch számítási csomópontokon kell, hogy egy storage-fiókot kapcsol a Batch-fiókhoz.

## <a name="preparation"></a>Előkészítés
Javasolt létrehozni egy megoldás, amely a Feladatkezelő, valamint a feladat processzor tartalmazhat, mivel ez még könnyebbé teheti a Feladatkezelő és a feladat processzor programok között kódmegosztáshoz. Ez a megoldás létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studiót, és válassza ki **fájl** > **új** > **projekt**.
2. A **sablonok**, bontsa ki a **egyéb projekttípusok**, kattintson a **Visual Studio-megoldások**, majd válassza ki **üres megoldás**.
3. Írja be az alkalmazás és a megoldás (például "LitwareBatchTaskPrograms") célját leíró nevet.
4. Az új megoldás létrehozásához kattintson a **OK**.

## <a name="job-manager-template"></a>Feladatkezelő-sablon
A feladat Manager-sablon segítségével megvalósítani a feladatkezelői tevékenység, amely a következő műveleteket hajthatja végre:

* Feladat felosztása több feladatot.
* Küldje el ezeket a feladatokat a Batch futtathatók.

> [!NOTE]
> További információ a Feladatkezelő tevékenységekről: [Batch szolgáltatás áttekintése fejlesztők számára](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Hozzon létre egy feladatot Manager-sablonnal
-Feladat hozzáadása a megoldáshoz, amelyet korábban hozott létre, kövesse az alábbi lépéseket:

1. Nyissa meg a meglévő megoldást a Visual Studióban.
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, kattintson a **Hozzáadás** > **új projekt**.
3. A **Visual C#**, kattintson a **felhőalapú**, és kattintson a **Azure Batch-feladat felosztó Feladatkezelő**.
4. Adjon meg egy nevet, amely ismerteti az alkalmazást, és a projekt azonosítja, a Feladatkezelő (például) "LitwareJobManager").
5. A projekt létrehozásához kattintson a **OK**.
6. Végezetül állítsa össze a projektet, azzal kényszerítheti a Visual Studio betölteni a hivatkozott NuGet-csomagok, és győződjön meg arról, hogy a projekt érvényes módosítás előtt.

### <a name="job-manager-template-files-and-their-purpose"></a>Feladatkezelő-sablonfájlokat és azok célja
A Feladatkezelő sablonnal projektet hoz létre, amikor kódfájlok három csoportjait hoz létre:

* A fő program fájl (Program.cs). A program a belépési pont és a legfelső szintű kivételkezelés tartalmazza. A beállítás módosítása általában nem kell.
* A keretrendszer könyvtárába. Ez a feladat manager program – a kicsomagolás paraméterek, tevékenységeket ad a Batch-feladat stb "bolierplate" elvégzett felelős fájlokat tartalmazza. Nem megfelelően kell módosítani ezeket a fájlokat.
* A feladat felosztó fájlt (JobSplitter.cs). Ez a meg, ahová az alkalmazás-specifikus logika egy feladat felosztása feladatokra lesz.

Természetesen a feladat felosztó kód a logika felosztása feladat összetettségétől függően támogatásához szükséges további fájlokat is hozzáadhat.

A sablon is létrehoz a standard .NET projektfájlokat, például a .csproj fájlban, app.config, packages.config stb.

A többi Ez a szakasz ismerteti a különböző fájlok és a kód szerkezete, és bemutatja, mire minden egyes osztály használható.

![A Visual Studio Megoldáskezelőben a Feladatkezelő sablon megoldás][solution_explorer01]

**Keretrendszer fájlok**

* `Configuration.cs`: A feladat konfigurációs adatok, például a Batch-fiók adatait, társított storage-fiók hitelesítő adatait, feladat és a feladatok adataihoz és feladatparaméter betöltését magában foglalja. A Batch által definiált környezeti változót (lásd a környezeti beállítások tevékenységekhez, a Batch dokumentációjában) a Configuration.EnvironmentVariable osztállyal hozzáférést is biztosít.
* `IConfiguration.cs`: A konfigurációs osztály végrehajtásának kivonatolja a feladat felosztó használatával egy hamis vagy utánzatként funkcionáló konfigurációs objektum segítségével test jednotky.
* `JobManager.cs`: A feladat manager program összetevői hangolja össze. Ez felelős az feladat elválasztó, a feladat felosztó meghívása és a feladatokat a tárfeladat küldője a feladat felosztó által visszaadott zahájeno inicializálása.
* `JobManagerException.cs`: Leállítja a Feladatkezelő igénylő hiba jelöli. JobManagerException segítségével burkolása "várt" hibák, ahol adott diagnosztikai adatokat lezárást részeként adható meg.
* `TaskSubmitter.cs`: Ez az osztály a Batch-feladat a feladat felosztó által visszaadott feladatok hozzáadása a feladata. A JobManager osztály összesítések feladatok sorozatát kötegekbe hatékony, de a megfelelő időben is a feladat ekkor meghívja a TaskSubmitter.SubmitTasks a háttérbeli szálon az egyes kötegek.

**Feladat-elválasztó**

`JobSplitter.cs`: Ez az osztály a vágását meghatározó a feldolgozás feladatokat az alkalmazás-specifikus logikáját tartalmazza. A keretrendszer beszerzése sorozata, feladatok, amely hozzáadja a feladathoz, a metódus visszaadja azokat a JobSplitter.Split módszert hívja meg. Ez az osztály hol fogja tölteni a feladat logikáját. A felosztás metódus CloudTask példányok, amelybe át szeretné particionálni a feladat a feladatokat képviselő sorozatát visszaadandó megvalósításához.

**Standard szintű .NET parancssori soubory projektu**

* `App.config`: Standard .NET alkalmazás-konfigurációs fájl.
* `Packages.config`: Standard NuGet csomag függőségi fájlt.
* `Program.cs`: A program belépési pont és a legfelső szintű kivételkezelés tartalmazza.

### <a name="implementing-the-job-splitter"></a>A feladat felosztó megvalósítása
Amikor megnyitja a Feladatkezelő sablonprojektjét, a projekt lesz alapértelmezés szerint a JobSplitter.cs fájlt. Meg lehet valósítani a felosztási logika, a feladatok a számítási feladatok a Split() metódus megjelenítése az alábbi használatával:

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
> A jegyzettel ellátott szakaszában a `Split()` módszer a következő: a Feladatkezelő sablonban lévő kód, amely a logikai hozzáadásával módosíthatja célja, hogy a feladatok felosztása a különböző feladatok csak szakaszában. Ha azt szeretné, módosíthatja a sablon egy másik szakaszában, győződjön meg arról, akkor is meg Batch működését, és próbálja ki néhány a [Kódminták Batch][github_samples].
> 
> 

A Split() megvalósítás férhet hozzá:

* A feladat paramétereit keresztül a `_parameters` mező.
* A feladat képviselő keresztül CloudJob objektum a `_job` mező.
* A feladatkezelői tevékenység, képviselő keresztül CloudTask objektum a `_jobManagerTask` mező.

A `Split()` megvalósítása nem kell közvetlenül tevékenységek hozzáadása a feladathoz. Ehelyett a kód CloudTask objektumok sorozatát adja vissza, és ezek hozzáadódik a feldolgozás automatikusan a keretrendszer osztályokat, amelyek aktiválják az feladat elválasztó alapján. Használja a C# a iterátor szokás (`yield return`) funkció megvalósítása a feladat elválasztókat, mert lehetővé teszi a feladatokat, hogy minél hamarabb futó összes tevékenység ki kell tartó várakozás helyett.

**A feladat felosztó sikertelen**

Ha a feladat felosztó hibát észlel, azt kell vagy:

* Leállítja a feladatütemezés, a C# használatával `yield break` utasítással, amely esetben a Feladatkezelő lesz kezelve sikeres; vagy
* Kivételt, amely esetben a Feladatkezelő throw sikertelenként lesz kezelve, és attól függően, hogy az ügyfél konfigurálta, előfordulhat, hogy újra megpróbálja).

Mindkét esetben bármilyen már a feladat felosztó által visszaadott, és hozzáadja a kötegelt feladatok futtatására alkalmas lesz. Ha nem szeretné, hogy ez, majd próbálja meg:

* A feladat felosztó visszatérése előtt a feladat leállítása
* Állítson össze a teljes feladat gyűjtemény visszaküldés előtt. (azaz adja vissza egy `ICollection<CloudTask>` vagy `IList<CloudTask>` a feladat felosztó használatával egy C# iterátor megvalósítása helyett)
* A feladat-függőségek használatára, hogy minden feladat függ, a Feladatkezelő sikeres befejezése

**Feladat manager újrapróbálkozások**

A Feladatkezelő nem sikerül, ha azt a Batch szolgáltatás az ügyfél újrapróbálkozási beállításoktól függően előfordulhat, hogy próbálkozik. Ez általában biztonságos, mivel ha a keretrendszer tevékenységeket ad a feladathoz, figyelmen kívül hagyja minden feladatot, amely már létezik. Azonban ha feladatok kiszámítása költséges, nem célszerű történő újraszámítása feladatot, amely már hozzá lett adva a feladatot; költsége Ezzel szemben ha ismételt futtatása nem garantált, hogy ugyanazt a feladatot azonosítókat létrehozni az "ismétlődések figyelmen kívül" viselkedésének fog nem Dőljön. Ezekben az esetekben a munkát, már megtörtént, és nem a műveletet, például egy CloudJob.ListTasks elvégzésével az eddig is számtalan előnyét feladatok megkezdése előtt észleli a feladat felosztó tervezzen.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kilépési kódot és a kivételeket a Feladatkezelő sablonban
Kilépési kódot és a kivételeket határozza meg a program futtatásának eredménye egy olyan mechanizmust biztosítanak, és segíthetnek az esetleges problémák felismeréséhez, a program végrehajtásával. A Feladatkezelő sablon valósítja meg a kilépési kódot és a jelen szakaszban bemutatott kivételek.

A feladatkezelői tevékenység, amely a Feladatkezelő sablon implementálása adhat vissza három lehetséges kilépési kód:

| Kód | Leírás |
| --- | --- |
| 0 |A feladat-kezelő sikeresen befejeződött. A feladat felosztó kódot futtatta befejezését, és minden feladat lettek hozzáadva a feladathoz. |
| 1 |A Feladatkezelő tevékenység egy "várt" részében a program kivételt okozott. A kivétel lett lefordítva meghaladja a diagnosztikai adatokban JobManagerException, és ahol lehetséges, javaslatokat, a hiba megoldásához. |
| 2 |A Feladatkezelő tevékenység egy "nem várt" kivétel miatt meghiúsult. A kivétel a normál a kimenetbe naplózott, de a Feladatkezelő nem tudta hozzáadni a további diagnosztikai vagy szervizelési adatokat. |

Feladat kezelő feladat sikertelensége esetén néhány feladatot előfordulhat, hogy továbbra is lettek hozzáadva a szolgáltatásnak a hiba bekövetkezése előtt. Ezeket a feladatokat a szokásos módon fog futni. "A feladat felosztó sikertelen" fent találja a kódelérési út veszik górcső alá.

Kivételek által visszaadott összes információ íródik a stdout.txt és stderr.txt fájlokba. További információkért lásd: [hibakezelés](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Az ügyfelek szempontjai
Ez a szakasz ismerteti az egyes ügyfélkövetelmények végrehajtása a sablon alapján Feladatkezelő meghívásakor. Lásd: [hogyan átadása paramétereket és a környezeti változókat az Ügyfélkód](#pass-environment-settings) átadása paramétereket és a környezeti beállítások részleteiért.

**Kötelező hitelesítő adatok**

Annak érdekében, hogy a tevékenységek hozzáadása az Azure Batch-feladatot, a Feladatkezelő tevékenység az Azure Batch-fiók URL-címe és a kulcs van szükség. Át kell adnia ezeket a környezeti változók YOUR_BATCH_URL és YOUR_BATCH_KEY nevű. Ezek a Feladatkezelő tevékenység környezet beállításait beállíthatja. Ha például az egy C#-ügyfél:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Storage hitelesítő adatai**

Általában az ügyfél nem kell megadnia a társított storage-fiók hitelesítő adatait a Feladatkezelő tevékenység (a) a legtöbb feladat kezelők nem kell explicit módon a társított storage-fiókja eléréséhez, és (b) a társított storage-fiók gyakran megadott összes feladatot, mert egy a feladat közös környezet beállítása. Ha nem tartalmazza a közös környezet beállításainál a társított storage-fiókban, és a Feladatkezelő társított storage hozzáférésre van szüksége, majd kell megadni a társított storage hitelesítő adatai a következő:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Feladat manager tevékenység beállításai**

Az ügyfél kell beállítania a Feladatkezelő *killJobOnCompletion* jelzőt **hamis**.

Állítsa be az ügyfél általában biztonságos *runExclusive* való **hamis**.

Az ügyfélnek használnia kell a *resourceFiles* vagy *applicationPackageReferences* gyűjtemény szeretné, hogy a feladat a számítási csomópontra üzembe helyezett manager végrehajtható (és a szükséges DLL-ek).

Alapértelmezés szerint a feladat-kezelő rendszer nem próbálkozik, ha nem sikerül. A feladat manager logikai függően az ügyfél érdemes keresztül újrapróbálkozások engedélyezése *megkötések*/*maxTaskRetryCount*.

**Feladatbeállítások**

A feladat felosztó függőségekkel rendelkező feladatok bocsát ki, ha az ügyfélnek be kell állítania a feladat usesTaskDependencies igaz.

A feladat felosztó modellben szokatlan, az ügyfelek szeretne tevékenységek hozzáadása a feladatok túlmenő a feladat felosztó hoz létre. Az ügyfél ezért kell normális esetben állítsa a feladat *onAllTasksComplete* való **terminatejob**.

## <a name="task-processor-template"></a>A feladat processzor sablon
Egy feladat processzor sablon segítségével megvalósítani egy feladat a processzor, a következő műveleteket hajthatja végre:

* Állítsa be az egyes Batch-feladat futtatásához szükséges adatokat.
* Minden egyes Batch-feladat által igényelt minden művelet fusson.
* Mentse a feladat kimeneti adattárolásra.

Egy feladat a processzor nem kötelező, a Batch a feladatok futtatásához, de a fő feladat processzort használ előnye, hogy az összes feladat-végrehajtási művelet megvalósítása az egyik helyről egy burkolót biztosít. Például ha minden olyan feladatnak a környezetben futtathat több alkalmazás van szüksége, vagy ha át kell másolnia az adatokat állandó tároló befejezése után minden feladat.

A feladat-feldolgozó által végrehajtott műveleteket lehet egyszerű vagy összetett, és több vagy frissítésre, a számítási feladat igényeinek megfelelően. Ezenkívül egy feladatot a processzor, az összes feladat műveletek alkalmazásával azonnal frissíteni vagy is műveletek alapuló alkalmazások és számítási feladatok követelményeit módosítása. Azonban bizonyos esetekben egy feladatot a processzor nem feltétlenül a megvalósítás az optimális megoldást, azt hozzáadhatja a szükségtelen összetettséget, például ha futnak a feladatok, amely gyorsan elindítható egy egyszerű parancssori paranccsal futtathatja.

### <a name="create-a-task-processor-using-the-template"></a>Hozzon létre egy feladatot processzor, a sablon használatával
A megoldás, amely a korábban létrehozott ad hozzá egy feladatot a processzor, kövesse az alábbi lépéseket:

1. Nyissa meg a meglévő megoldást a Visual Studióban.
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldás, kattintson a **Hozzáadás**, és kattintson a **új projekt**.
3. A **Visual C#**, kattintson a **felhőalapú**, és kattintson a **Azure Batch-feladat processzor**.
4. Adjon meg egy nevet, amely ismerteti az alkalmazást, és ez a projekt azonosítja, a feladat processzor (például) "LitwareTaskProcessor").
5. A projekt létrehozásához kattintson a **OK**.
6. Végezetül állítsa össze a projektet, azzal kényszerítheti a Visual Studio betölteni a hivatkozott NuGet-csomagok, és győződjön meg arról, hogy a projekt érvényes módosítás előtt.

### <a name="task-processor-template-files-and-their-purpose"></a>Processzor sablon feladatfájlok és azok célja
Ha a feladat processzor sablonnal projektet hoz létre, kódfájlok három csoportjait hoz létre:

* A fő program fájl (Program.cs). A program a belépési pont és a legfelső szintű kivételkezelés tartalmazza. A beállítás módosítása általában nem kell.
* A keretrendszer könyvtárába. Ez a feladat manager program – a kicsomagolás paraméterek, tevékenységeket ad a Batch-feladat stb "bolierplate" elvégzett felelős fájlokat tartalmazza. Nem megfelelően kell módosítani ezeket a fájlokat.
* A feladat processzor fájlt (TaskProcessor.cs). Ez a meg, ahová az alkalmazás-specifikus logika egy feladat végrehajtása (általában meghívásával a meglévő végrehajtható) lesz. Előzetes és utólagos feldolgozási kódot, mint például a további adatokat tölti le, vagy eredmény-fájlok feltöltésével is ide kerül.

Természetesen a feladat processzor kód a logika felosztása feladat összetettségétől függően támogatásához szükséges további fájlokat is hozzáadhat.

A sablon is létrehoz a standard .NET projektfájlokat, például a .csproj fájlban, app.config, packages.config stb.

A többi Ez a szakasz ismerteti a különböző fájlok és a kód szerkezete, és bemutatja, mire minden egyes osztály használható.

![A Visual Studio Megoldáskezelőben a feladat processzor sablon megoldás][solution_explorer02]

**Keretrendszer fájlok**

* `Configuration.cs`: A feladat konfigurációs adatok, például a Batch-fiók adatait, társított storage-fiók hitelesítő adatait, feladat és a feladatok adataihoz és feladatparaméter betöltését magában foglalja. A Batch által definiált környezeti változót (lásd a környezeti beállítások tevékenységekhez, a Batch dokumentációjában) a Configuration.EnvironmentVariable osztállyal hozzáférést is biztosít.
* `IConfiguration.cs`: A konfigurációs osztály végrehajtásának kivonatolja a feladat felosztó használatával egy hamis vagy utánzatként funkcionáló konfigurációs objektum segítségével test jednotky.
* `TaskProcessorException.cs`: Leállítja a Feladatkezelő igénylő hiba jelöli. TaskProcessorException segítségével burkolása "várt" hibák, ahol adott diagnosztikai adatokat lezárást részeként adható meg.

**A feladat processzor**

* `TaskProcessor.cs`: A feladat fut. A keretrendszer a TaskProcessor.Run metódust hív meg. Ez az osztály hol fogja tölteni az alkalmazás-specifikus logika, a feladat. A Run metódus megvalósításához:
  * Elemezheti és ellenőrzése minden olyan feladat paraméterei
  * Compose bármely szeretne meghívni a külső program parancssora
  * Bármely lehet szükség a hibakereséshez diagnosztikai információk naplózása
  * Elindítani a folyamatot, a parancssor használatával
  * Várjon, amíg a folyamat leállását
  * A folyamat határozza meg, hogy azt a sikeres vagy sikertelen volt a kilépési kód rögzítése
  * Mentse a kimeneti fájlokat szeretné megőrizni az állandó tárolóból

**Standard szintű .NET parancssori soubory projektu**

* `App.config`: Standard .NET alkalmazás-konfigurációs fájl.
* `Packages.config`: Standard NuGet csomag függőségi fájlt.
* `Program.cs`: A program belépési pont és a legfelső szintű kivételkezelés tartalmazza.

## <a name="implementing-the-task-processor"></a>A feladat processzor megvalósítása
A feladat processzor sablonprojekt megnyitásakor a projekt lesz alapértelmezés szerint a TaskProcessor.cs fájlt. Meg lehet valósítani a feladatokat a futtatási logikát a számítási feladatok Run() alább látható módon:

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
> A Run() metódus a jegyzettel ellátott szakasz célja, hogy a számítási feladatok számára a futtatási logika hozzáadásával módosítsa a feladat processzor sablon kódot csak szakaszában. Ha azt szeretné, módosíthatja a sablon egy másik szakasz,. először ismerkedjen meg a Batch – dokumentáció áttekintése, és próbálja ki a Batch-kódminta néhány Batch működése.
> 
> 

A Run() metódus felelős indítása a háttérfolyamatok egy vagy több, az összes folyamat végrehajtásához, Várakozás a parancssor mentése folyamatban van az eredményeket, és végül visszaadó kilépési kóddal. A Run() módszer, ahol a feladatok feldolgozási a logikát alkalmazzák. A feladat processzor keretrendszer meg; a Run() metódust hív meg nem kell saját kezűleg meghívására.

A Run() megvalósítás férhet hozzá:

* A tevékenység-paraméterek használatával a `_parameters` mező.
* A feladatok és tevékenységek azonosítók keresztül a `_jobId` és `_taskId` mezőket.
* A feladat konfigurációja keresztül a `_configuration` mező.

**A feladat sikertelen**

Sikertelenség esetén a kilépéshez a Run() metódus hívása kivétel, de a felső szintű kivételkezelő vezérlése alatt a tevékenység kilépési kód kihasználatlan marad. Szabályozhatja a kilépési kódot úgy, hogy megkülönböztethesse különböző típusú hibák, például diagnosztikai célokra, vagy mert az egyes hibaállapot kell megszüntesse a feladatot, másokat pedig nem kell, ha meg kell zárja be a Run() metódus egy nem nulla értékű felismerésével kilépési kód. Ez lesz a tevékenységek kilépési kódját.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kilépési kódot és a feladat processzor sablonban kivételek
Kilépési kódot és a kivételeket határozza meg a program futtatásának eredménye egy olyan mechanizmust biztosítanak, és segíthetnek a program végrehajtása során problémákat. A feladat processzor sablon valósítja meg a kilépési kódot és a jelen szakaszban bemutatott kivételek.

Egy feladat processzor feladat, amely a feladat processzor sablon implementálása adhat vissza három lehetséges kilépési kód:

| Kód | Leírás |
| --- | --- |
| [Process.ExitCode][process_exitcode] |A feladat processzor futott befejezését. Vegye figyelembe, hogy ez nem jelenti azt, hogy hívta meg a program sikeres volt – csak, hogy a feladat-feldolgozó sikeresen elindítva, és elvégzett bármilyen utófeldolgozás kivételek nélkül. A kilépési kód értelmében a meghívott programtól függ – általában a 0 kilépési kódot azt jelenti, hogy a program sikeres volt, és bármely más kilépési kód azt jelenti, hogy a program futása meghiúsult. |
| 1 |A feladat processzor a program "várt" részében kivétel miatt nem sikerült. A kivétel lett lefordítva egy `TaskProcessorException` diagnosztikai adatokban, és ahol lehetséges, javaslatokat, a hiba megoldásához. |
| 2 |A feladat processzor: Váratlan"kivétel miatt nem sikerült. A kivétel a normál a kimenetbe naplózott, de a feladat-feldolgozó nem tudta hozzáadni a további diagnosztikai vagy szervizelési adatokat. |

> [!NOTE]
> Ha a program hívhat meg jelzi az adott hibaállapot 1. és 2 kilépési kódot használ, majd használja az 1. és 2 kilépési kódot a feladat processzor hibákat nem egyértelmű. A feladat processzor hibakódok a kivétel esetekben a Program.cs fájl szerkesztésével módosíthatja megkülönböztető kilépési kódot.
> 
> 

Kivételek által visszaadott összes információ íródik a stdout.txt és stderr.txt fájlokba. További információkért lásd a hibakezelés képességével is, a Batch dokumentációjában.

### <a name="client-considerations"></a>Az ügyfelek szempontjai
**Storage hitelesítő adatai**

Ha a feladat processzor használja az Azure blob storage-bA megőrizni a kimeneteket, például az file conventions segédkódtárba helyezni, akkor azt hozzá kell férnie *vagy* a felhőalapú tárfiók hitelesítő adatainak *vagy* blob tároló URL-címe, amely tartalmazza a közös hozzáférésű jogosultságkód (SAS). A sablon keresztül közös környezeti változókat a hitelesítő adatokkal támogatását tartalmazza. Az ügyfél módon a tároló hitelesítő adatait adja át:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A tárfiók majd a TaskProcessor osztály keresztül érhető el a `_configuration.StorageAccount` tulajdonság.

Ha inkább egy tároló URL-cím használata SAS, is átadhat a keresztül feladat közös környezeti beállításokat, de a feladat processzor sablon jelenleg nem tartalmaz beépített támogatást nyújt, ez a.

**Tárolás beállítása**

Javasoljuk, hogy az ügyfél vagy a feladat tevékenység létrehozása előtt a feladatok hozzáadása a feladathoz szükséges feladatok tárolókat. Ez a kötelező SAS egy tároló URL-címet használja, ha ilyen egy URL-cím nem tartalmazza a tároló létrehozásához szükséges engedéllyel. Ajánlott még akkor is, ha a tárfiók hitelesítő adatait, felhasználó, minden egyes feladathoz, hogy ehhez fel kellene CloudBlobContainer.CreateIfNotExistsAsync a tárolón menti.

## <a name="pass-parameters-and-environment-variables"></a>Adja át a paramétereket, és a környezeti változók
### <a name="pass-environment-settings"></a>Pass környezeti beállítások
Egy ügyfél is át adatokat a Feladatkezelő tevékenység környezeti beállítások formájában. Ez az információ majd használhatják a Feladatkezelő tevékenység fog futni, a számítási feladat részeként feladat processzor feladatok létrehozásakor. Példák az információ, amelyet továbbíthat, a környezeti beállítások a következők:

* Tárfióknév és tárfiókkulcsok
* Batch-fiók URL-címe
* Batch-fiók kulcsa

A Batch szolgáltatás környezeti beállítások átadása a feladatkezelői tevékenység használatával egyszerű mechanizmussal rendelkezik a `EnvironmentSettings` tulajdonság [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Például lekérése a `BatchClient` példány egy Batch-fiókhoz, a környezeti változókat az ügyfél a kód az URL-cím és a megosztott kulcsos hitelesítő adatok, a Batch-fiók adhat át. Hasonlóképpen a tárfiók eléréséhez, amely a Batch-fiókhoz van csatolva, adhat át a tárfiók nevét és a tárfiók kulcsát környezeti változókként.

### <a name="pass-parameters-to-the-job-manager-template"></a>Adja át a paramétereket a feladat Manager-sablon
Sok esetben hasznos lehet a feladatkezelői tevékenység, szabályozhatja a feladat felosztása folyamat, vagy konfigurálhatja a tevékenységek a feladat feladat paraméterek átadása. Ehhez egy parameters.json nevű erőforrásfájlként a Feladatkezelő tevékenység a JSON-fájl feltöltése. A paraméterek is majd válnak elérhetővé a `JobSplitter._parameters` mezőt a Feladatkezelő sablonban.

> [!NOTE]
> A beépített paraméter-kezelő csak karakterlánc-karakterlánc szótárak támogatja. Ha azt szeretné, a komplex JSON-értékeit adja át a paraméter értékeként, kell adja át ezeket, mint a karakterláncok és a feladat felosztó elemzett őket, vagy módosítsa a keretrendszert `Configuration.GetJobParameters` metódust.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Adja át a paramétereket a tevékenység processzor-sablon
Az egyes tevékenységek a feladat processzor-sablon használatával implementált paramétereket is átadhat. Ugyanúgy, mint a feladat manager-sablonnal, a feladat processzor sablon keres egy erőforrás-fájlt

Parameters.JSON, és ha találhatók, mint a paraméterek szótár betölti azt. Van néhány lehetőség a paramétereket adhat át a feladat processzor feladatokat:

* Újból felhasználhatja a feladat paramétereit JSON. Ez jól működik, ha csak paraméterei feladatokra (például egy renderelési magasság és szélesség) is. Ehhez a feladathoz felosztó egy CloudTask létrehozásakor, adjon hozzá egy hivatkozást a parameters.json fájl objektum, a Feladatkezelő ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) a CloudTask ResourceFiles gyűjtemény.
* Hozzon létre és feladatspecifikus parameters.json dokumentum feltöltése a felosztó végrehajtási feladat részeként, és hivatkozni, hogy a blob, a feladat erőforrás fájlok gyűjteményben. Ez azért szükséges, ha különböző feladatok más paraméterekkel rendelkezik. Például egy 3D renderelési forgatókönyv, ahol a keret index átadott paraméterként. a feladat lehet.

> [!NOTE]
> A beépített paraméter-kezelő csak karakterlánc-karakterlánc szótárak támogatja. Ha azt szeretné, a komplex JSON-értékeit adja át a paraméter értékeként, szüksége lesz adja át ezeket, mint a karakterláncok és értelmezni őket a feladat processzor, vagy módosítsa a keretrendszert `Configuration.GetTaskParameters` metódust.
> 
> 

## <a name="next-steps"></a>További lépések
### <a name="persist-job-and-task-output-to-azure-storage"></a>Az Azure Storage-feladatok és tevékenységek kimenetének megőrzése
Egy másik hasznos eszköz a Batch-megoldások fejlesztését [Azure Batch File Conventions][nuget_package]. A .NET osztálytár (jelenleg előzetes verzióban érhető el) a Batch .NET-alkalmazásokban segítségével könnyen tárolása és beolvasása a feladat kimeneti Azure Storage szolgáltatásba vagy onnan. [Azure Batch-feladat és tevékenységek kimenetének megőrzése](batch-task-output.md) tartalmazza a teljes hatásának a megbeszélését a könyvtárban, és azok használatát.


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
