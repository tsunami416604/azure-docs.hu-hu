---
title: Megoldások készítése Visual Studio-sablonokkal - Azure Batch | Microsoft dokumentumok
description: Megtudhatja, hogy a Visual Studio projektsablonjai hogyan segíthetik a nagy számítási feladatok megvalósítását és futtatását az Azure Batch-en.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: a71dbd1b38ff58ccf1eb7a4d50daad5b24922e2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022749"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Batch-megoldások ugrással történő indítása visual studio-projektsablonokkal

A **Feladatkezelő** és **a Feladatfeldolgozó Visual Studio batch-sablonjai** olyan kódot biztosítanak, amely segít a nagy számítási feladatok megvalósításában és futtatásában a Batch-en a legkevesebb erőfeszítéssel. Ez a dokumentum ismerteti ezeket a sablonokat, és útmutatást ad azok használatához.

> [!IMPORTANT]
> Ez a cikk csak az e két sablonra vonatkozó információkat ismerteti, és feltételezi, hogy ismeri a Batch szolgáltatást és a hozzá kapcsolódó legfontosabb fogalmakat: készletek, számítási csomópontok, feladatok és feladatok, feladatkezelői feladatok, környezeti változók és egyéb releváns Információ. További információt az Azure Batch és a [Batch szolgáltatás áttekintése](batch-api-basics.md)a fejlesztők számára című [témakörben](batch-technical-overview.md) talál.
> 
> 

## <a name="high-level-overview"></a>Magas szintű áttekintés
A Feladatkezelő és a Feladatfeldolgozó sablon két hasznos összetevő létrehozásához használható:

* Feladatkezelő feladat, amely olyan feladatfelosztást valósít meg, amely egy feladatot több feladatra bonthat, amelyek egymástól függetlenül, párhuzamosan futtathatók.
* Olyan feladatfeldolgozó, amely az alkalmazás parancssora körüli előfeldolgozás és utófeldolgozás végrehajtására használható.

Például egy filmrenderelési forgatókönyvben a feladatosztó egyetlen filmfeladatot több száz vagy több ezer különálló feladattá alakítana át, amelyek az egyes képkockákat külön-külön dolgoznák fel. Ennek megfelelően a feladatfeldolgozó meghívja a renderelési alkalmazást és az egyes képkockák megjelenítéséhez szükséges összes függő folyamatot, valamint további műveleteket hajt végre (például a renderelt keretet egy tárolóhelyre másolja).

> [!NOTE]
> A Feladatkezelő és a Feladatfeldolgozó sablonok egymástól függetlenek, így a számítási feladat követelményeitől és a beállításoktól függően mindkettőt vagy csak az egyiket használhatja.
> 
> 

Az alábbi ábrán látható módon az ezeket a sablonokat használó számítási feladat három szakaszon megy keresztül:

1. Az ügyfélkód (pl. alkalmazás, webszolgáltatás stb.) egy feladatot küld az Azure Batch szolgáltatásának, amely feladatkezelői feladatként megadja a feladatkezelő imázsát a feladatkezelő programban.
2. A Batch szolgáltatás egy számítási csomóponton futtatja a feladatkezelő feladatot, és a feladatosztó elindítja a feladatfeldolgozó feladatok megadott számát, annyi számítási csomóponton, amennyi szükséges, a feladatfelosztási kód paraméterei és specifikációi alapján.
3. A feladatfeldolgozó feladatok egymástól függetlenül, párhuzamosan futnak a bemeneti adatok feldolgozásához és a kimeneti adatok létrehozásához.

![Az ügyfélkód és a Batch szolgáltatás együttműködését bemutató diagram][diagram01]

## <a name="prerequisites"></a>Előfeltételek
A Batch sablonok használatához a következőkre lesz szükség:

* Telepített számítógép, amelyen telepítve van a Visual Studio 2015. A kötegsablonok jelenleg csak a Visual Studio 2015-ben támogatottak.
* A Batch sablonok, amelyek a [Visual Studio-galériában][vs_gallery] visual studio-bővítményekként érhetők el. A sablonok kétféleképpen szerezhetők be:
  
  * Telepítse a sablonokat a Visual Studio **Bővítmények és frissítések** párbeszédpanelén (további információt a Visual [Studio-bővítmények keresése és használata][vs_find_use_ext]című témakörben talál). A **Bővítmények és frissítések** párbeszédpanelen keresse meg és töltse le a következő két bővítményt:
    
    * Azure kötegelt feladatkezelő feladatfelosztással
    * Azure batch feladatprocesszor
  * A sablonok letöltése a Visual Studio online gyűjteményéből: [Microsoft Azure Batch project sablonok][vs_gallery_templates]
* Ha azt tervezi, hogy az [Alkalmazáscsomagok](batch-application-packages.md) szolgáltatás használatával telepíti a feladatkezelőt és a feladatfeldolgozót a Batch számítási csomópontjaira, akkor egy tárfiókot kell csatolnia a Batch-fiókhoz.

## <a name="preparation"></a>Előkészítés
Javasoljuk, hogy hozzon létre egy olyan megoldást, amely tartalmazza a feladatkezelőt és a feladatfeldolgozót is, mivel ez megkönnyíti a kód megosztását a feladatkezelő és a feladatfeldolgozó programok között. A megoldás létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio alkalmazást, és válassza az**Új** > **projekt** **fájlja** > lehetőséget.
2. A **Sablonok**csoportban bontsa ki **az Egyéb projekttípusok ,** majd a Visual **Studio-megoldások**lehetőséget, majd válassza az **Üres megoldás**lehetőséget.
3. Írjon be egy nevet, amely leírja az alkalmazást és a megoldás célját (pl. "LitwareBatchTaskPrograms").
4. Az új megoldás létrehozásához kattintson az **OK gombra.**

## <a name="job-manager-template"></a>Feladatkezelő sablon
A Feladatkezelő sablon segítségével olyan feladatkezelői feladatot valósíthat meg, amely a következő műveleteket hajthatja végre:

* Feladat felosztása több feladatra.
* Küldje el ezeket a feladatokat a Kötegben való futtatáshoz.

> [!NOTE]
> A feladatkezelői feladatokról a [Kötegelt szolgáltatások áttekintése fejlesztőknek](batch-api-basics.md#job-manager-task)című témakörben olvashat bővebben.
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Feladatkezelő létrehozása a sablon használatával
Ha egy korábban létrehozott megoldáshoz szeretne feladatkezelőt hozzáadni, kövesse az alábbi lépéseket:

1. Nyissa meg meglévő megoldását a Visual Studióban.
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és kattintson az**Új projekt** **hozzáadása** > parancsra.
3. A **Visual C#** csoportban kattintson a **Felhő**elemre, majd az Azure Batch Job Manager with **Job Splitter**elemre.
4. Írjon be egy nevet, amely leírja az alkalmazást, és azonosítja a projektet feladatkezelőként (pl. "LitwareJobManager").
5. A projekt létrehozásához kattintson az **OK**gombra.
6. Végül hozza létre a projektet úgy, hogy a Visual Studio kényszerítse az összes hivatkozott NuGet-csomag betöltésére, és ellenőrizze, hogy a projekt érvényes-e, mielőtt elkezdené módosítani.

### <a name="job-manager-template-files-and-their-purpose"></a>Job Manager sablonfájlok és céljuk
Amikor projektet hoz létre a Feladatkezelő sablonnal, az három kódfájlcsoportot hoz létre:

* A fő programfájl (Program.cs). Ez tartalmazza a program belépési pontját és a legfelső szintű kivételkezelést. Ezt általában nem kell módosítania.
* A keretrendszer könyvtára. Ez tartalmazza a fájlokat felelős a "boilerplate" munkát végzett a job manager program - kicsomagolás paraméterek, hozzátéve, feladatok a batch feladat, stb Általában nem kell módosítania ezeket a fájlokat.
* A feladatosztó fájl (JobSplitter.cs). Ez az a hely, ahol a feladat feladatokra való felosztásának alkalmazásspecifikus logikáját helyezheti el.

Természetesen szükség szerint további fájlokat is hozzáadhat a feladatfelosztási kód támogatásához, a feladatfelosztási logika összetettségétől függően.

A sablon szabványos .NET projektfájlokat is létrehoz, például .csproj fájlt, app.config, packages.config stb.

A szakasz többi része ismerteti a különböző fájlokat és azok kódszerkezetét, és ismerteti, hogy az egyes részlegek mit csinálnak.

![Visual Studio Solution Explorer a Job Manager sablonmegoldással][solution_explorer01]

**Keretrendszerfájljai**

* `Configuration.cs`: A feladat konfigurációs adatainak betöltését foglalja magában, például a Batch-fiók adatait, a kapcsolódó tárfiók hitelesítő adatait, a feladat- és feladatadatokat, valamint a feladatparamétereket. Emellett hozzáférést biztosít a Batch által definiált környezeti változókhoz (lásd: A feladatok környezeti beállításai a Batch dokumentációban) a Configuration.EnvironmentVariable osztályon keresztül.
* `IConfiguration.cs`: Absztrakt a konfigurációs osztály megvalósításában, így egységben tesztelheti a feladatelválasztót egy hamis vagy ál konfigurációs objektum használatával.
* `JobManager.cs`: A feladatkezelő program összetevőit vezényli. Felelős a feladatosztó inicializálásáért, a feladatelválasztó meghívásáért és a feladatosztó által visszaküldött feladatok nak a feladatbeküldőnek történő elküldésével.
* `JobManagerException.cs`: Olyan hibát jelöl, amelynek befejezéséhez a feladatkezelőnek kell megszüntetnie. A JobManagerException a "várt" hibák burkolására szolgál, ahol a megszüntetés részeként konkrét diagnosztikai információk adhatók meg.
* `TaskSubmitter.cs`: Ez az osztály felelős a feladatosztó által visszaadott feladatok hozzáadásáért a kötegelt feladathoz. A JobManager osztály a feladatok sorrendjét kötegekbe összesíti a feladat hatékony, de időben történő hozzáadása érdekében, majd meghívja a TaskSubmitter.SubmitTasks fájlt az egyes kötegek háttérszálán.

**Feladatosztó**

`JobSplitter.cs`: Ez az osztály alkalmazásspecifikus logikát tartalmaz a feladat feladatokra való felosztásához. A keretrendszer meghívja a JobSplitter.Split metódust egy feladatsorozat beszerzéséhez, amelyet hozzáad a feladathoz, ahogy a metódus visszaadja őket. Ez az az osztály, ahol beadja a munkája logikáját. Valósítsa meg a Split metódust a CloudTask-példányok sorozatának visszaadásához, amelyek azokat a feladatokat jelölik, amelyekbe particionálni szeretné a feladatot.

**Szabványos .NET parancssori projektfájlok**

* `App.config`: Szabványos .NET alkalmazáskonfigurációs fájl.
* `Packages.config`: Standard NuGet csomag függőségi fájl.
* `Program.cs`: A program belépési pontját és a legfelső szintű kivételkezelést tartalmazza.

### <a name="implementing-the-job-splitter"></a>A feladatosztó megvalósítása
A Feladatkezelő sablonprojekt megnyitásakor a projekt ben alapértelmezés szerint meg nyílik a JobSplitter.cs fájl. A számítási feladatok felosztási logikáját az alábbi Split() metódus segítségével valósíthatja meg:

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
> A `Split()` módszer jegyzetekkel ellátva szakasza a Feladatkezelő sablonkódjának egyetlen olyan szakasza, amelyet úgy módosíthat, hogy hozzáadja a logikát a feladatok különböző feladatokra való felosztásához. Ha módosítani szeretné a sablon egy másik szakaszát, győződjön meg arról, hogy ismeri a Batch működését, és próbáljon ki néhányat a [Batch kódminták közül.][github_samples]
> 
> 

A Split() implementációja hozzáfér a következőkhöz:

* A feladat paraméterei `_parameters` a mezőn keresztül.
* A feladatot képviselő CloudJob objektum `_job` a mezőn keresztül.
* A feladatkezelő feladatot képviselő CloudTask `_jobManagerTask` objektum a mezőn keresztül.

A `Split()` megvalósításnak nem kell közvetlenül hozzáadnia a feladatokat a feladathoz. Ehelyett a kódot kell visszaadnia a CloudTask objektumok sorozatát, és ezeket automatikusan hozzá adja a feladathoz a keretrendszer osztályok, amelyek meghívja a feladat elosztott. Gyakori, hogy a C#iterátor (`yield return`) funkciót használja a feladatosztók megvalósításához, mivel ez lehetővé teszi, hogy a feladatok a lehető leghamarabb elinduljanak, ahelyett, hogy megvárnák az összes feladat kiszámítását.

**Feladatosztó hibája**

Ha a feladatosztó hibát észlel, akkor a következőknek kell lennie:

* A C# `yield break` utasítás sal megszakítja a sorozatot, amely esetben a feladatkezelő sikeresnek lesz megfelelően kezelve; Vagy
* Kivételt, amely esetben a feladatkezelő sikertelenként lesz kezelve, és az ügyfél konfigurálásától függően újra próbálkozhat).

Mindkét esetben a feladatosztó által már visszaadott és a kötegelt feladathoz hozzáadott feladatok futtathatók. Ha nem akarod, hogy ez történjen, akkor:

* A feladat leállítása a feladatelosztóból való visszatérés előtt
* Fogalmazza meg a teljes feladatgyűjteményt, `ICollection<CloudTask>` mielőtt `IList<CloudTask>` visszaadna (azaz visszaad egy vagy ahelyett, hogy a feladatosztót egy C# iterátorral valósítana meg)
* Tevékenységfüggőségek használata, hogy az összes tevékenység a feladatkezelő sikeres befejezésétől függjön

**Feladatkezelő újrapróbálkozásai**

Ha a feladatkezelő meghibásodik, a Batch szolgáltatás újrapróbálkozhat vele az ügyfél újrapróbálkozási beállításaitól függően. Ez általában biztonságos, mert amikor a keretrendszer feladatokat ad hozzá a feladathoz, figyelmen kívül hagyja a már létező feladatokat. Ha azonban a feladatok kiszámítása költséges, előfordulhat, hogy nem kívánja a feladathoz már hozzáadott feladatok újraszámítási költségét felszámítani; ezzel szemben, ha az újrafuttatás nem garantált, hogy ugyanazt a feladatazonosítókat hozza létre, akkor a "duplikált példányok figyelmen kívül hagyása" viselkedés nem lép be. Ezekben az esetekben meg kell terveznie a feladat elosztott észlelésére a már elvégzett munka, és nem ismételjük meg, például egy CloudJob.ListTasks végrehajtása előtt a feladatok hozamát.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kilépési kódok és kivételek a Feladatkezelő sablonban
A kilépési kódok és kivételek lehetővé teszik a program futtatásának kimenetelét, és segíthetnek a program végrehajtásával kapcsolatos problémák azonosításában. A Feladatkezelő sablon megvalósítja az ebben a szakaszban ismertetett kilépési kódokat és kivételeket.

A Feladatkezelő sablonnal megvalósított feladatkezelői feladat három lehetséges kilépési kódot adhat vissza:

| Kód | Leírás |
| --- | --- |
| 0 |A feladatkezelő sikeresen befejeződött. A feladatosztó kódja a befejezésig futott, és az összes feladat hozzá adódott a feladathoz. |
| 1 |A feladatkezelő feladat kivétellel meghiúsult a program egy "várt" részén. A kivétel t egy JobManagerException-re fordította le diagnosztikai információkkal, és ahol lehetséges, a hiba feloldására vonatkozó javaslatokkal. |
| 2 |A feladatkezelő feladat "váratlan" kivétellel meghiúsult. A kivétel a szabványos kimenetre lett naplózva, de a feladatkezelő nem tudott további diagnosztikai vagy javítási adatokat hozzáadni. |

A feladatkezelő feladatának meghibásodása esetén előfordulhat, hogy a hiba bekövetkezése előtt bizonyos feladatok még hozzá lettek adva a szolgáltatáshoz. Ezek a feladatok a szokásos módon fognak futni. A kódelérési útról a fenti "Feladatfelosztó hiba" című témakörben található.

A kivételek által visszaadott összes információ az stdout.txt és az stderr.txt fájlokba van beírva. További információt a [Hibakezelés című](batch-api-basics.md#error-handling)témakörben talál.

### <a name="client-considerations"></a>Ügyfélszempontok
Ez a szakasz néhány ügyfél-megvalósítási követelményt ismertet, amikor a sablon alapján egy feladatkezelőt kér. [Lásd: Paraméterek és környezeti változók az ügyfélkódból a](#pass-environment-settings) paraméterek és a környezeti beállítások átadásának részleteiért.

**Kötelező hitelesítő adatok**

Annak érdekében, hogy feladatokat adjon hozzá az Azure Batch-feladathoz, a feladatkezelő feladathoz az Azure Batch-fiók URL-címe és kulcsa szükséges. Ezeket a YOUR_BATCH_URL és YOUR_BATCH_KEY nevű környezeti változókban kell átadnia. Ezeket a Feladatkezelő feladatkörnyezet beállításaiban állíthatja be. Például egy C# ügyfél:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Tárolási hitelesítő adatok**

Az ügyfélnek általában nem kell megadnia a kapcsolódó tárfiók hitelesítő adatait a feladatkezelő feladathoz, mert (a) a legtöbb feladatkezelőnek nem kell kifejezetten hozzáférnie a csatolt tárfiókhoz, és (b) a csatolt tárfiókot gyakran minden feladathoz biztosítják a feladat hoz létre közös környezetet. Ha nem adja meg a csatolt tárfiókot a közös környezeti beállításokon keresztül, és a feladatkezelőnek hozzáférésre van szüksége a csatolt tárolóhoz, akkor a csatolt tárolási hitelesítő adatokat az alábbiak szerint kell megadnia:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Feladatkezelő feladatbeállításai**

Az ügyfélnek hamisra kell állítania a feladatkezelő *killJobOnCompletion* **jelzőjét.**

Általában biztonságos, hogy az ügyfél a *runExclusive-t* **hamisra**állítsa.

Az ügyfélnek a *resourceFiles* vagy *az applicationPackageReferences* gyűjteményt kell használnia ahhoz, hogy a feladatkezelő végrehajtható fájlja (és a szükséges DL-ek) telepítve legyen a számítási csomóponton.

Alapértelmezés szerint a feladatkezelő nem lesz újratárgyalva, ha nem sikerül. A feladatkezelő logikájától függően előfordulhat, hogy az ügyfél engedélyezni szeretné az újrapróbálkozásokat/*a maxTaskRetryCount* *megkötéseken*keresztül.

**Feladatbeállítások**

Ha a feladatosztó függőségekkel rendelkező feladatokat bocsát ki, az ügyfélnek true értékre kell állítania a feladat felhasználási céljait.

A feladatosztó modellben szokatlan, hogy az ügyfelek a feladatosztó által létrehozott lehetőségeken felül feladatokat szeretnének hozzáadni a feladatokhoz. Az ügyfélnek ezért általában be kell állítania a feladat *onAllTasksComplete* feladatát **a feladat megszakításához.**

## <a name="task-processor-template"></a>Feladatfeldolgozó sablon
A Feladatfeldolgozó sablon segítségével olyan feladatfeldolgozót valósíthat meg, amely a következő műveleteket hajtja végre:

* Állítsa be az egyes Kötegfeladatok futtatásához szükséges adatokat.
* Futtassa az egyes Kötegfeladatokhoz szükséges összes műveletet.
* Mentse a feladatkimeneteket az állandó tárolóba.

Bár a feladatfeldolgozóknak nem kell feladatokat futtatniuk a Batch-en, a feladatfeldolgozó használatának fő előnye, hogy egy burkológépet biztosít az összes feladatvégrehajtási művelet egy helyen történő végrehajtásához. Ha például több alkalmazást kell futtatnia az egyes feladatok környezetében, vagy ha az egyes feladatok befejezése után adatokat kell átmásolnia az állandó tárolóba.

A feladatprocesszor által végrehajtott műveletek lehetnek olyan egyszerűek vagy összetettek, és a munkaterhelés által megkövetelt, akár csak annyi vagy kevesebb is lehet. Ezenkívül az összes feladatművelet egyetlen feladatfeldolgozóba való implementálásával könnyen frissítheti vagy hozzáadhatműveleteket az alkalmazások vagy a munkaterhelési követelmények módosításai alapján. Bizonyos esetekben azonban előfordulhat, hogy egy feladatprocesszor nem az optimális megoldás a megvalósításhoz, mivel szükségtelen bonyolultságot okozhat, például olyan feladatok futtatásakor, amelyek gyorsan elindíthatók egy egyszerű parancssorból.

### <a name="create-a-task-processor-using-the-template"></a>Feladatfeldolgozó létrehozása a sablon használatával
Ha feladatfeldolgozót szeretne hozzáadni a korábban létrehozott megoldáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg meglévő megoldását a Visual Studióban.
2. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, kattintson a **Hozzáadás**parancsra, majd az **Új projekt**parancsra.
3. A **Visual C#** csoportban kattintson a **Felhő**elemre, majd az **Azure Batch-feladatfeldolgozó parancsra.**
4. Írjon be egy nevet, amely leírja az alkalmazást, és a projektet feladatfeldolgozóként azonosítja (pl. "LitwareTaskProcessor").
5. A projekt létrehozásához kattintson az **OK**gombra.
6. Végül hozza létre a projektet úgy, hogy a Visual Studio kényszerítse az összes hivatkozott NuGet-csomag betöltésére, és ellenőrizze, hogy a projekt érvényes-e, mielőtt elkezdené módosítani.

### <a name="task-processor-template-files-and-their-purpose"></a>Feladatprocesszor-sablonfájlok és céljuk
Amikor a feladatfeldolgozó sablonnal hoz létre projektet, az három kódfájlcsoportot hoz létre:

* A fő programfájl (Program.cs). Ez tartalmazza a program belépési pontját és a legfelső szintű kivételkezelést. Ezt általában nem kell módosítania.
* A keretrendszer könyvtára. Ez tartalmazza a fájlokat felelős a "boilerplate" munkát végzett a job manager program - kicsomagolás paraméterek, hozzátéve, feladatok a batch feladat, stb Általában nem kell módosítania ezeket a fájlokat.
* A feladatfeldolgozó fájl (TaskProcessor.cs). Ez az, ahol az alkalmazás-specifikus logika egy feladat végrehajtása (általában egy meglévő végrehajtható fájl hívásával). Az elő- és utófeldolgozási kód, például további adatok letöltése vagy eredményfájlok feltöltése szintén ide kerül.

Természetesen szükség szerint további fájlokat is hozzáadhat a feladatprocesszor-kód támogatásához, a feladatfelosztási logika összetettségétől függően.

A sablon szabványos .NET projektfájlokat is létrehoz, például .csproj fájlt, app.config, packages.config stb.

A szakasz többi része ismerteti a különböző fájlokat és azok kódszerkezetét, és ismerteti, hogy az egyes részlegek mit csinálnak.

![Visual Studio Solution Explorer a Feladatprocesszor sablonmegoldással][solution_explorer02]

**Keretrendszerfájljai**

* `Configuration.cs`: A feladat konfigurációs adatainak betöltését foglalja magában, például a Batch-fiók adatait, a kapcsolódó tárfiók hitelesítő adatait, a feladat- és feladatadatokat, valamint a feladatparamétereket. Emellett hozzáférést biztosít a Batch által definiált környezeti változókhoz (lásd: A feladatok környezeti beállításai a Batch dokumentációban) a Configuration.EnvironmentVariable osztályon keresztül.
* `IConfiguration.cs`: Absztrakt a konfigurációs osztály megvalósításában, így egységben tesztelheti a feladatelválasztót egy hamis vagy ál konfigurációs objektum használatával.
* `TaskProcessorException.cs`: Olyan hibát jelöl, amelynek befejezéséhez a feladatkezelőnek kell megszüntetnie. A TaskProcessorException a "várt" hibák tördelésére szolgál, ahol a megszüntetés részeként konkrét diagnosztikai információk adhatók meg.

**Feladatprocesszor**

* `TaskProcessor.cs`: Futtatja a feladatot. A keretrendszer meghívja a TaskProcessor.Run metódust. Ez az az osztály, ahol a feladat alkalmazásspecifikus logikáját adja be. A Futtatás metódus megvalósítása:
  * A feladatparaméterek elemzése és ellenőrzése
  * A meghívni kívánt külső programok parancssorának megírása
  * A hibakereséshez szükséges diagnosztikai adatok naplózása
  * Folyamat indítása az adott parancssorból
  * Várja meg, amíg a folyamat kilép
  * A folyamat kilépési kódjának rögzítése annak megállapításához, hogy sikeres vagy sikertelen volt-e
  * Az állandó tárolóba tárolni kívánt kimeneti fájlok mentése

**Szabványos .NET parancssori projektfájlok**

* `App.config`: Szabványos .NET alkalmazáskonfigurációs fájl.
* `Packages.config`: Standard NuGet csomag függőségi fájl.
* `Program.cs`: A program belépési pontját és a legfelső szintű kivételkezelést tartalmazza.

## <a name="implementing-the-task-processor"></a>A feladatfeldolgozó implementálása
A Feladatfeldolgozó sablonprojekt megnyitásakor a projekt alapértelmezés szerint meg nyitja a TaskProcessor.cs fájlt. A számítási feladatok futtatási logikáját az alábbi Run() metódus segítségével valósíthatja meg:

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
> A Run() metódus jegyzetekkel ellátva szakasza a Feladatfeldolgozó sablonkód egyetlen olyan szakasza, amelyet a számítási feladatok futtatási logikájának hozzáadásával módosíthat. Ha módosítani szeretné a sablon egy másik szakaszát, először ismerkedjen meg a Batch működésével a Batch dokumentáció jának áttekintésével és a Batch-kód minták kipróbálásával.
> 
> 

A Run() metódus felelős a parancssor elindításáért, egy vagy több folyamat elindításáért, az összes folyamat befejezésére való várakozásért, az eredmények mentéséért és végül a kilépési kóddal való visszatérésért. A Run() metódus, ahol a feladatok feldolgozási logikáját valósítja meg. A feladatfeldolgozó keretrendszer meghívja a Run() metódust; nem kell, hogy hívja meg magad.

A Run() implementációja hozzáfér a következőkhöz:

* A feladat paraméterei `_parameters` a mezőn keresztül.
* A feladat és a feladat `_jobId` azonosítók, a és `_taskId` a mezőkön keresztül.
* A tevékenység konfigurációja `_configuration` a mezőn keresztül.

**A feladat hibája**

Hiba esetén kivétel esetén kiléphet a Run() metódusból, de ez a feladat kilépési kódjának vezérlésében hagyja a legfelső szintű kivételkezelőt. Ha a kilépési kódot úgy kell szabályoznia, hogy meg tudja különböztetni a különböző típusú hibákat, például diagnosztikai célokra, vagy mert egyes hibamódoknak meg kell szakítaniuk a feladatot, míg másoknak nem, akkor egy nem nulla kilépési kódot. Ez lesz a feladat kilépési kódja.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kilépési kódok és kivételek a Feladatfeldolgozó sablonban
A kilépési kódok és kivételek lehetővé teszik a program futtatásának kimenetelét, és segítenek azonosítani a program végrehajtásával kapcsolatos problémákat. A Feladatfeldolgozó sablon megvalósítja az ebben a szakaszban ismertetett kilépési kódokat és kivételeket.

A Feladatfeldolgozó sablonnal megvalósított feladatfeldolgozó feladat három lehetséges kilépési kódot adhat vissza:

| Kód | Leírás |
| --- | --- |
| [Process.ExitCode][process_exitcode] |A feladatprocesszor a befejezésig futott. Ne feledje, hogy ez nem jelenti azt, hogy a meghívott program sikeres volt – csak azt, hogy a feladatfeldolgozó sikeresen meghívta, és kivétel nélkül végrehajtotta az utófeldolgozást. A kilépési kód jelentése a meghívott programtól függ – általában a 0 kilépési kód azt jelenti, hogy a program sikeres volt, és bármely más kilépési kód azt jelenti, hogy a program nem sikerült. |
| 1 |A feladatfeldolgozó kivétellel meghibásodott a program egy "várt" részén. A kivétel ta-ra fordította `TaskProcessorException` a diagnosztikai információkat, és ahol lehetséges, javaslatokat a hiba feloldására. |
| 2 |A feladatfeldolgozó "váratlan" kivétellel meghibásodott. A kivétel a szabványos kimenetre lett naplózva, de a feladatfeldolgozó nem tudott további diagnosztikai vagy szervizelési adatokat hozzáadni. |

> [!NOTE]
> Ha a meghívott program az 1-es és 2-es kilépési kódokat használja adott hibamódok jelzésére, akkor az 1-es és 2-es kilépési kód használata a feladatprocesszor-hibákhoz nem egyértelmű. Ezeket a feladatfeldolgozóhibakódokat a Program.cs fájlban lévő kivételesetek szerkesztésével megkülönböztető kilépési kódokra módosíthatja.
> 
> 

A kivételek által visszaadott összes információ az stdout.txt és az stderr.txt fájlokba van beírva. További információt a Hibakezelés című témakörben talál a Batch dokumentációjában.

### <a name="client-considerations"></a>Ügyfélszempontok
**Tárolási hitelesítő adatok**

Ha a feladatfeldolgozó az Azure blob storage-t használja a kimenetek megőrzéséhez, például a fájlkonvenciók segítő könyvtárának használatával, akkor *hozzáférésre* van szüksége a felhőbeli tárfiók hitelesítő adataihoz *vagy* egy blobtároló URL-címéhez, amely megosztott hozzáférésű aláírást (SAS) tartalmaz). A sablon támogatja a hitelesítő adatok biztosítását a közös környezeti változókon keresztül. Az ügyfél a következőképpen adhatja át a tárolási hitelesítő adatokat:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A tárfiók ezután elérhető a TaskProcessor osztályban a `_configuration.StorageAccount` tulajdonságon keresztül.

Ha inkább egy tároló URL-címét használja a SAS-szal, ezt is átadhatja egy közös feladat környezetbeállításon keresztül, de a feladatfeldolgozó sablon jelenleg nem tartalmaz beépített támogatást ehhez.

**Tároló beállítása**

Javasoljuk, hogy az ügyfél vagy a feladatkezelő feladat hozza létre a feladatok által igényelt tárolókat, mielőtt hozzáadná a feladatokat a feladathoz. Ez kötelező, ha egy tároló URL-címét használja a SAS-szal, mert az ilyen URL-cím nem tartalmaz engedélyt a tároló létrehozásához. Akkor is ajánlott, ha átadja a tárfiók hitelesítő adatait, mivel minden olyan feladatot ment, amelynek meg kell hívnia a CloudBlobContainer.CreateIfNotExistsAsync metódust a tárolón.

## <a name="pass-parameters-and-environment-variables"></a>Paraméterek és környezeti változók áthaladása
### <a name="pass-environment-settings"></a>Környezeti beállítások megadása
Az ügyfél környezeti beállítások formájában továbbíthatja az adatokat a feladatkezelő feladatnak. Ezt az információt a feladatkezelő feladat használhatja a számítási feladat részeként futó feladatfeldolgozó feladatok létrehozásakor. Példák a környezeti beállításokként átadható információkra:

* Tárfiók neve és fiókkulcsai
* Kötegfiók URL-címe
* Kötegszámla kulcsa

A Batch szolgáltatás egy egyszerű mechanizmussal rendelkezik a környezeti `EnvironmentSettings` beállítások feladatkezelői feladatnak való átadására a [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask]tulajdonság használatával.

Például egy Batch-fiók `BatchClient` példányának lekérése esetén környezeti változókként továbbíthatja az ügyfélkódból a Batch-fiók URL-címét és megosztott kulcshitelesítő adatait. Hasonlóképpen a Batch-fiókhoz kapcsolódó tárfiók eléréséhez környezeti változóként adhatja meg a tárfiók nevét és a tárfiók kulcsát.

### <a name="pass-parameters-to-the-job-manager-template"></a>Paraméterek átadása a Feladatkezelő sablonba
Sok esetben hasznos feladatonkénti paramétereket átadni a feladatkezelő feladatnak, vagy a feladatfelosztási folyamat szabályozásához vagy a feladat feladathoz való konfigurálásához. Ezt úgy teheti meg, hogy feltölt egy parameters.json nevű JSON-fájlt erőforrásfájlként a feladatkezelő feladathoz. A paraméterek ezután elérhetővé `JobSplitter._parameters` válhatnak a Feladatkezelő sablon mezőjében.

> [!NOTE]
> A beépített paraméterkezelő csak karakterlánc-karakterlánc szótárakat támogat. Ha összetett JSON-értékeket szeretne paraméterértékként átadni, ezeket karakterláncként kell átadnia, és elemeznie kell őket `Configuration.GetJobParameters` a feladatosztóban, vagy módosítania kell a keretrendszer metódusát.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Paraméterek átadása a Feladatfeldolgozó sablonba
Paramétereket is átadhat a Feladatfeldolgozó sablon nal megvalósított egyes feladatoknak. A feladatkezelő sablonhoz ugyanúgy, a feladatfeldolgozó sablon is egy nevű erőforrásfájlt keres

parameters.json, és ha úgy találta, hogy betölti azt a paraméterekszótár. A paraméterek feladatfeldolgozó feladatokhoz való átvitelének néhány lehetősége van:

* Használja fel újra a JSON feladatparamétereket. Ez akkor működik jól, ha az egyetlen paraméter a feladat szintű (például a renderelési magasság és szélesség). Ehhez amikor egy CloudTask-t hoz létre a feladatosztóban, adjon hozzá egy hivatkozást a parameters.json`JobSplitter._jobManagerTask.ResourceFiles`erőforrásfájl objektumra a feladatkezelő feladat ResourceFiles ( ) gyűjteményéből a CloudTask ResourceFiles gyűjteményéhez.
* Hozzon létre és töltsön fel egy feladatspecifikus paramétert.json dokumentumot a feladatfelosztás végrehajtásának részeként, és hivatkozzon erre a blobra a feladat erőforrásfájl-gyűjteményében. Erre akkor van szükség, ha a különböző feladatok különböző paraméterekkel rendelkeznek. Példa lehet egy 3D renderelési forgatókönyv, ahol a keretindex paraméterként kerül át a feladathoz.

> [!NOTE]
> A beépített paraméterkezelő csak karakterlánc-karakterlánc szótárakat támogat. Ha összetett JSON-értékeket szeretne paraméterértékként átadni, ezeket karakterláncként kell átadnia, és elemeznie kell `Configuration.GetTaskParameters` őket a feladatfeldolgozóban, vagy módosítania kell a keretrendszer metódusát.
> 
> 

## <a name="next-steps"></a>További lépések
### <a name="persist-job-and-task-output-to-azure-storage"></a>Feladat és feladat kimenetének megőrzése az Azure Storage-ban
A batch-megoldások fejlesztésének másik hasznos eszköze az [Azure Batch File konvenciók.][nuget_package] A Batch .NET-alkalmazásokban ezzel a .NET osztálytárral (jelenleg előzetes verzióban) egyszerűen tárolhatja és lekérheti az Azure Storage-ba és onnan történő feladatkimeneteket. [Az Azure Batch-feladat megőrzése és a feladatkimenet](batch-task-output.md) a könyvtár és annak használatának teljes körű vitafórumát tartalmazza.


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
