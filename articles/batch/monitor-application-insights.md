---
title: Köteg figyelése az Azure Application Insights segítségével | Microsoft dokumentumok
description: Ismerje meg, hogyan lehet egy Azure Batch .NET alkalmazás az Azure Application Insights-függvénytár használatával.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: labrenne
ms.openlocfilehash: b1f4fb0207d4f659861dbd3fdfd1b2d502409935
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022460"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Azure Batch .NET-alkalmazások figyelése és hibakeresése az Application Insights segítségével

[Az Application Insights](../azure-monitor/app/app-insights-overview.md) elegáns és hatékony módot biztosít a fejlesztők számára az Azure-szolgáltatásokban üzembe helyezett alkalmazások figyelésére és hibakeresésére. Az Application Insights segítségével figyelheti a teljesítményszámlálókat és kivételeket, valamint egyéni metrikákkal és nyomkövetéssel is megmérheti a kódot. Az Application Insights integrálása az Azure Batch-alkalmazással lehetővé teszi, hogy mélyreható betekintést nyerjen a viselkedésbe, és közel valós időben vizsgálja meg a problémákat.

Ez a cikk bemutatja, hogyan adhat hozzá és konfigurálhat az Application Insights-függvénytárat az Azure Batch .NET-megoldásba, és hogyan instrumentelheti az alkalmazáskódot. Azt is bemutatja, hogyan figyelheti az alkalmazást az Azure Portalon keresztül, és egyéni irányítópultok létrehozása. Az Application Insights más nyelveken nyújtott támogatása, tekintse meg a [nyelvek, platformok és integrációk dokumentációt.](../azure-monitor/app/platforms.md)

A cikkhez tartozó kóddal ellátott C# mintamegoldás a [GitHubon](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)érhető el. Ez a példa az Application Insights instrumentation kódot adja hozzá a [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) példához. Ha nem ismeri ezt a példát, először próbálja meg a TopNWords felépítését és futtatását. Ezzel segít megérteni egy alapvető batch munkafolyamat feldolgozása egy sor bemeneti blobok párhuzamosan több számítási csomópontok. 

> [!TIP]
> Másik lehetőségként konfigurálja a Batch-megoldást az Application Insights-adatok, például a virtuális gépek teljesítményszámlálóinak megjelenítéséhez a Batch Explorerben. [A Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, gazdag funkcionalitású, önálló ügyféleszköz az Azure Batch-alkalmazások létrehozásához, hibakereséséhez és figyeléséhez. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. Tekintse meg a [batch-insights tárházban](https://github.com/Azure/batch-insights) a gyors lépéseket, amelyek lehetővé teszik az Application Insights-adatok a Batch Explorerben. 
>

## <a name="prerequisites"></a>Előfeltételek
* [Visual Studio 2017 vagy újabb](https://www.visualstudio.com/vs)

* [Kötegelt fiók és csatolt tárfiók](batch-account-create-portal.md)

* [Application Insights-erőforrás](../azure-monitor/app/create-new-resource.md )
  
   * Az Azure Portal használatával hozzon létre egy Application *Insights-erőforrást.* Válassza ki az *Általános* **alkalmazás típust**.

   * Másolja a [műszerezési kulcsot](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) a portálról. Erre a cikk későbbi részében kerül ki.
  
  > [!NOTE]
  > Előfordulhat, hogy az Application Insightsban tárolt [adatokért díjat kell fizetnie.](https://azure.microsoft.com/pricing/details/application-insights/) Ez magában foglalja a jelen cikkben tárgyalt diagnosztikai és figyelési adatokat.
  > 

## <a name="add-application-insights-to-your-project"></a>Application Insights hozzáadása a projekthez

A **Microsoft.ApplicationInsights.WindowsServer** NuGet csomag és annak függőségei szükségesek a projekthez. Adja hozzá vagy állítsa vissza őket az alkalmazás projektjéhez. A csomag telepítéséhez `Install-Package` használja a parancsot vagy a NuGet Csomagkezelőt.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Referencia Application Insights a .NET alkalmazás a **Microsoft.ApplicationInsights** névtér használatával.

## <a name="instrument-your-code"></a>A kód eszköze

A kód eszközének eszközéhez a megoldásnak létre kell hoznia egy Application Insights [TelemetryClient.](/dotnet/api/microsoft.applicationinsights.telemetryclient) A példában a Telemetriai-ügyfél betölti a konfigurációját az [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlból. Ügyeljen arra, hogy frissítse ApplicationInsights.config a következő projektek az Application Insights instrumentation kulcs: Microsoft.Azure.Batch.Samples.TelemetryStartTask és TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Adja hozzá a műszerezési kulcsot is a fájlhoz TopNWords.cs.

A TopNWords.cs példa a következő [instrumentation hívásokat](../azure-monitor/app/api-custom-events-metrics.md) használja az Application Insights API-ból:
* `TrackMetric()`- Nyomon követi, hogy átlagosan mennyi ideig tart egy számítási csomópont a szükséges szövegfájl letöltéséhez.
* `TrackTrace()`- Hozzáadja a hibakeresési hívásokat a kódhoz.
* `TrackEvent()`- Nyomon követi az érdekes eseményeket, amelyeket meg kell örökítenie.

Ebben a példában szándékosan hagyja ki a kivételkezelést. Ehelyett az Application Insights automatikusan jelenti a nem kezelt kivételeket, ami jelentősen javítja a hibakeresési élményt. 

A következő kódrészlet bemutatja, hogyan kell használni ezeket a módszereket.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Az Azure Batch telemetriai inicializáló segítője
Egy adott kiszolgáló és példány telemetriai adatainak jelentésekor az Application Insights az Azure virtuális gép szerepkörés virtuálisgép-nevet használja az alapértelmezett értékekhez. Az Azure Batch környezetében a példa bemutatja, hogyan használhatja a készlet nevét és a számítási csomópont nevét. [Telemetriai inicializáló](../azure-monitor/app/api-filtering-sampling.md#add-properties) használatával felülbírálhatja az alapértelmezett értékeket. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

A telemetriai inicializáló engedélyezéséhez a TopNWordsSample projekt ApplicationInsights.config fájlja a következőket tartalmazza:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>A feladat és a feladatok frissítése az Application Insights bináris fájljaihoz

Annak érdekében, hogy az Application Insights megfelelően fusson a számítási csomópontokon, győződjön meg arról, hogy a bináris fájlok megfelelően vannak elhelyezve. Adja hozzá a szükséges bináris fájlokat a tevékenység erőforrásfájl-gyűjteményéhez, hogy a feladat végrehajtásakor letöltődjenek. A következő kódrészletek hasonlóak a Job.cs kódhoz.

Először hozzon létre egy statikus listát a feltöltendő Application Insights-fájlokról.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Ezután hozza létre a feladat által használt átmeneti fájlokat.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

A `FileToStage` módszer egy segítő függvény a kódmintában, amely lehetővé teszi, hogy egyszerűen töltsön fel egy fájlt a helyi lemezről egy Azure Storage blobba. Minden fájl később letöltődik egy számítási csomópontra, és egy feladat hivatkozik rá.

Végül adja hozzá a feladatokat a feladathoz, és adja meg a szükséges Application Insights bináris fájlokat.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Adatok megtekintése az Azure Portalon

Most, hogy konfigurálta a feladatot és a feladatokat az Application Insights használatára, futtassa a példafeladatot a készletben. Keresse meg az Azure Portalon, és nyissa meg a kiépített Application Insights-erőforrást. A készlet kiépítése után meg kell kezdenie az adatok folyamatos és naplózási megtekintését. A cikk további részében csak néhány Application Insights-funkciót érint, de nyugodtan fedezze fel a teljes szolgáltatáskészletet.

### <a name="view-live-stream-data"></a>Élő közvetítés adatainak megtekintése

Az Applications Insights-erőforrás nyomkövetési naplóinak megtekintéséhez kattintson az **Élő közvetítés**elemre. A következő képernyőkép bemutatja, hogyan tekintheti meg a készlet számítási csomópontjaiból érkező élő adatokat, például a számítási csomópontonkénti CPU-használatot.

![Élő közvetítés számítási csomóadatai](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Nyomkövetési naplók megtekintése

Az Applications Insights erőforrás nyomkövetési naplóinak megtekintéséhez kattintson a **Keresés**gombra. Ez a nézet az Application Insights által rögzített diagnosztikai adatok listáját jeleníti meg, beleértve a nyomkövetéseket, eseményeket és kivételeket. 

A következő képernyőkép bemutatja, hogyan naplózza a rendszer egy feladat egyetlen nyomkövetését, és később lekérdezi a rendszer hibakeresési célokra.

![Nyomkövetési naplók képe](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Nem kezelt kivételek megtekintése

Az alábbi képernyőképek bemutatják, hogyan Naplózza az Application Insights az alkalmazásból kiváltott kivételeket. Ebben az esetben másodperceken belül az alkalmazás a kivétel tetszését, részletezheti egy adott kivétel, és diagnosztizálni a problémát.

![Nem kezelt kivételek](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Blob letöltési idejének mérése

Az egyéni metrikák is értékes eszköz a portálon. Megjelenítheti például azt az átlagos időt, amerre az egyes számítási csomópontoknak kellett letölteniük a szükséges szövegfájlt, amelyet feldolgoztak.

Mintadiagram létrehozása:
1. Az Application Insights-erőforrásban kattintson **a Metrikák kezelődiagram** > **hozzáadása elemre.**
2. Kattintson a hozzáadott diagram **Szerkesztés gombjára.**
2. Frissítse a diagram részleteit az alábbiak szerint:
   * **Diagramtípus** **rácsra**való beállítása.
   * Állítsa **az összesítést** **Átlagos**értékre.
   * Csoport **beállítása** **NodeId szerint.**
   * A **Metrikák alkalmazásban**válassza **az Egyéni** > **blob letöltése másodpercben**lehetőséget.
   * Állítsa be a kijelző **színpalettáját** a megfelelő nek. 

![Blob letöltési ideje csomópontonként](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Számítási csomópontok folyamatos figyelése

Előfordulhat, hogy észrevette, hogy az összes metrika, beleértve a teljesítményszámlálókat is, csak akkor kerül naplózásra, ha a feladatok futnak. Ez a viselkedés azért hasznos, mert korlátozza az Application Insights által naplózandó adatok mennyiségét. Vannak azonban olyan esetek, amikor mindig szeretné figyelni a számítási csomópontokat. Előfordulhat például, hogy olyan háttérmunkát futtatnak, amely nincs ütemezve a Batch szolgáltatáson keresztül. Ebben az esetben állítson be egy figyelési folyamatot a számítási csomópont élettartama szerint. 

Ennek a viselkedésnek az egyik módja, hogy létrehoz egy folyamatot, amely betölti az Application Insights-függvénytárat, és a háttérben fut. A példában a kezdő feladat betölti a bináris fájlokat a számítógépen, és a folyamat ot határozatlan ideig futtatja. Konfigurálja az Application Insights konfigurációs fájlt ehhez a folyamathoz, hogy további, önt érdeklő adatokat, például teljesítményszámlálókat bocsátjon ki.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> A megoldás kezelhetőségének növelése érdekében a szerelvényt egy [alkalmazáscsomagba](./batch-application-packages.md)csomagolhatja. Ezután az alkalmazáscsomag automatikus üzembe helyezéséhez a készletek, adjon hozzá egy alkalmazáscsomag-hivatkozást a készlet konfigurációjához.
>

## <a name="throttle-and-sample-data"></a>Szabályozás és mintaadatok 

Az éles környezetben futó Azure Batch-alkalmazások nagylépték jellege miatt érdemes lehet korlátozni az Application Insights által a költségek kezeléséhez gyűjtött adatok mennyiségét. Tekintse [meg az Application Insights mintavételezése](../azure-monitor/app/sampling.md) néhány mechanizmust ennek eléréséhez.


## <a name="next-steps"></a>További lépések
* További információ az [Application Insights](../azure-monitor/app/app-insights-overview.md)ról.

* Az Application Insights más nyelveken nyújtott támogatása, tekintse meg a [nyelvek, platformok és integrációk dokumentációt.](../azure-monitor/app/platforms.md)


