---
title: Batch az Azure Application Insights figyelése |} A Microsoft Docs
description: 'Útmutató: Azure Batch .NET-alkalmazás, az Azure Application Insights-könyvtár használatával alakítsa ki.'
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: danlep
ms.openlocfilehash: 986a05dab29226ff492269587ab6c0f49585cef6
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119907"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Figyelheti és hibakeresése az Azure Batch .NET-alkalmazás, az Application insights segítségével

[Az Application Insights](../azure-monitor/app/app-insights-overview.md) elegáns és hatékony megoldást kínál a fejlesztők számára üzembe helyezett Azure-szolgáltatások figyelése és hibakeresési alkalmazásokat. Application insights szolgáltatás segítségével figyelő teljesítményszámlálók és kivételeket, valamint az eszköz a kód egyéni metrikákkal és nyomon követését. Az Application Insights integrációja az Azure Batch-alkalmazás lehetővé teszi viselkedések mélyebb betekintést, és közel valós időben problémák kivizsgálásában.

Ez a cikk bemutatja, hogyan adhat hozzá és Application Insights-könyvtár konfigurálása az Azure Batch .NET-megoldásba, és alakítsa ki az alkalmazás kódjában. Azt is bemutatja, monitorozhatók az alkalmazások az Azure Portalon, és egyéni irányítópultokat hozhat létre. Az Application Insights támogatja a más nyelveken, tekintse meg a [nyelvek, platformok és integrációk dokumentáció](../azure-monitor/app/platforms.md).

Minta C#-megoldásból, és ez a cikk kísérő kódot érhető el a [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Ebben a példában az Application Insights-kialakítási kód hozzáadja a [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) példa. Ha még nem ismeri az, hogy a példában készítése és TopNWords először próbálja meg. Ennek a segítségével megismerheti a bemeneti blobokat több számítási csomóponton párhuzamosan készletét feldolgozási alapszintű Batch munkafolyamat. 

> [!TIP]
> Alternatív megoldásként konfigurálhatja a Batch-megoldás, például a virtuális gép teljesítményszámlálók Application Insights-adatok megjelenítése a Batch Explorer. [A Batch Explorer](https://github.com/Azure/BatchExplorer) egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz létrehozásához, hibakereséséhez és monitorozásához az Azure Batch-alkalmazások segítségével. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre. Tekintse meg a [batch-insights tárház](https://github.com/Azure/batch-insights) gyors lépések az Application Insights-adatok a Batch Explorer engedélyezze a. 
>

## <a name="prerequisites"></a>Előfeltételek
* [Visual Studio 2017](https://www.visualstudio.com/vs)

* [Batch-fiók és a társított storage-fiók](batch-account-create-portal.md)

* [Application Insights-erőforrás](../azure-monitor/app/create-new-resource.md )
  
   * Az Azure portal használatával hozzon létre egy Application Insights *erőforrás*. Válassza ki a *általános* **alkalmazástípus**.

   * Másolás a [kialakítási kulcs](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) a portálról. Ez a cikk későbbi részében szükséges.
  
  > [!NOTE]
  > Előfordulhat, hogy [felszámított](https://azure.microsoft.com/pricing/details/application-insights/) az Application insights szolgáltatásban tárolt adatokhoz. Ez magában foglalja a diagnosztikai és a monitorozási adatok, a cikkben leírtak szerint.
  > 

## <a name="add-application-insights-to-your-project"></a>Az Application Insights hozzáadása a projekthez

A **Microsoft.ApplicationInsights.WindowsServer** NuGet-csomagot és annak függőségeit a projekt számára szükségesek. Adjon hozzá, vagy állítsa vissza őket az application-projektet. A csomag telepítéséhez használja a `Install-Package` parancsot vagy a NuGet-Csomagkezelőt.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
A .NET-alkalmazás az Application Insights használatával hivatkozik a **Microsoft.ApplicationInsights** névtér.

## <a name="instrument-your-code"></a>A kód alkalmazásáról

A kód alkalmazásáról a megoldáshoz szükséges, hozzon létre egy Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). A példában a TelemetryClient betölti a konfigurációját az a [ApplicationInsights.config](../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt. Ügyeljen arra, hogy a következő projektek az ApplicationInsights.config frissítse az Application Insights-eszközkulcs: Microsoft.Azure.Batch.Samples.TelemetryStartTask és TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
A kialakítási kulcsot is hozzáadhat a TopNWords.cs fájlban.

TopNWords.cs a példában a következő [instrumentation hívások](../azure-monitor/app/api-custom-events-metrics.md) az Application Insights API-ból:
* `TrackMetric()` -Követi nyomon, milyen hosszú, az átlagos számítási csomópont szükséges időt a szükséges fájlt.
* `TrackTrace()` -Hozzáadja a kódhoz hibakeresési hívások.
* `TrackEvent()` – Érdekes események rögzítése nyomon követi.

Ebben a példában a kivételkezelés szándékosan kihagyása. Ehelyett az Application Insights automatikusan jelentések nem kezelt kivételeket, ami jelentősen javítja a hibakeresési folyamatot. 

Az alábbi kódrészlet ezen metódusok használatát mutatja be.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Az Azure Batch telemetriai inicializáló segítő
Egy adott kiszolgáló és a példány a telemetriai jelentéskészítés, amikor az Application Insights használja az alapértelmezett értékeket az Azure Virtuálisgép-szerepkör és a virtuális gép nevét. Az Azure Batch környezetben a példa bemutatja, hogyan használhatja a készlet nevét, és ehelyett számítási csomópont neve. Használja a [telemetriainicializáló](../azure-monitor/app/api-filtering-sampling.md#add-properties) felülbírálhatja az alapértelmezett értékeket. 

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

Ahhoz, hogy a telemetriainicializálót, az ApplicationInsights.config fájlt a TopNWordsSample projektben a következőket tartalmazza:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>A feladatok és tevékenységek közé tartozik az Application Insights bináris fájljainak frissítése

Ahhoz, hogy az Application Insights működéséhez a számítási csomópontokon győződjön meg arról, a bináris fájlok megfelelően vannak elhelyezve. Adja hozzá a szükséges bináris fájlokat a feladat erőforrás fájlok gyűjtése, hogy azok a tevékenységek a végrehajtásuk időben letöltése. Az alábbi kódrészletek Job.cs kód hasonlóak.

Először hozzon létre egy Application Insights feltölteni kívánt fájlok statikus listáján.

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

Ezután hozza létre az átmeneti fájlok, a feladat által használt.
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

A `FileToStage` módszer a segédfüggvény a kódmintában, amely lehetővé teszi, hogy könnyedén tölthet fel egy fájlt a helyi lemez egy Azure Storage-blobba. Minden egyes fájl később, a számítási csomópontra töltődik le, és a feladat által hivatkozott.

Végül adja hozzá a feladatokat a feladat, és az Application Insights szükséges bináris fájljait tartalmazza.
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

Most, hogy a feladat és a tevékenységek az Application Insights használatára konfigurálta, a példában feldolgozással a készletben. Keresse meg az Azure Portalon, és nyissa meg az Application Insights-erőforrást, amely az Ön által üzembe helyezett. A készlet kiépítése, után szeretné elkezdeni áramló adatok és az első naplózza. Ez a cikk további részének koppint a csak néhány az Application Insights-szolgáltatások, de nyugodtan teljes funkciókészlete megismerése.

### <a name="view-live-stream-data"></a>Élő adatfolyam-adatok megtekintése

Nyomkövetési naplók megtekintése az Applications Insights-erőforrást, kattintson a **élő Stream**. Az alábbi képernyőfelvételen a számítási csomópontok a készlethez, például a CPU-használat számítási csomópontok származó élő adatok megtekintése.

![Élő stream számítási csomópont adatai](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Nyomkövetési naplók megtekintése

Nyomkövetési naplók megtekintése az Applications Insights-erőforrást, kattintson a **keresési**. Ebben a nézetben látható az Application Insights, beleértve a nyomkövetéseket, eseményeket és kivételek által rögzített diagnosztikai adatok listája. 

A következő képernyőképen az látható, hogyan tevékenység egyetlen nyomkövetési naplózza, és később lekérik a hibakeresési célra.

![Nyomkövetési naplók kép](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Nem kezelt kivételek megtekintéséhez

Az alábbi képernyőfelvételnek megfelelően jeleníti meg, hogy miként naplózza az Application Insights a az alkalmazás által létrehozott kivételek gyűjtését. Ebben az esetben az alkalmazás a kivétel értesítő másodpercen belül akkor is egy konkrét kivétel részletes és diagnosztizálhatja a problémát.

![Nem kezelt kivételek](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mérték blob letöltési idő

Egyéni metrikák is rendelkezésre állnak olyan értékes eszköz a portálon. Megjelenítheti például átlagos idő alatt jutott, hogy minden számítási csomóponton, és töltse le a szükséges szöveges fájlt dolgozott fel azt.

Példa diagram létrehozásához:
1. Kattintson az Application Insights-erőforrás **Metrikaböngésző** > **Hozzáadás diagram**.
2. Kattintson a **szerkesztése** a diagram, amely hozzá lett adva.
2. A diagram részletei a következő frissítés:
   * Állítsa be **diagramtípus** való **rács**.
   * Állítsa be **összesítési** való **átlagos**.
   * Állítsa be **csoportosítás** való **NodeId**.
   * A **metrikák**válassza **egyéni** > **Blob download másodpercek alatt**.
   * Állítsa be a megjelenítendő **színpaletta** az Ön által választott. 

![BLOB letöltési idő csomópontonként](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Figyelő csomópontok folyamatosan

Talán már észrevette, hogy az összes mérőszámokat, például teljesítményszámlálók, csak rendszer naplóz, amikor a feladatok futnak. Ez a viselkedés akkor hasznos, mivel az Application Insights-naplók adatok mennyisége korlátozza. Azonban előfordulhatnak olyan esetek, ha mindig szeretne figyelni, a számítási csomópontok. Például előfordulhat, hogy lehet futnak nem ütemezett háttérműveletek a Batch szolgáltatáson keresztül. Ebben az esetben állítsa be a monitorozási folyamat futtatásához a számítási csomópont élettartama. 

Egy érhet el ezt a viselkedést módja spustit novou kopii egy folyamat, amely az Application Insights-könyvtár betölti és futtatja a háttérben. A példában az indítási tevékenység betölti a bináris fájlokat a gépen, és határozatlan ideig futó folyamat tartja. Konfigurálja az Application Insights konfigurációs fájlt a folyamat gridre bocsáthatja ki az Önt érdeklő, például teljesítményszámlálók további adatokat.

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
> A kezelhetőség, hogy a megoldás növelése érdekében a szerelvény is csomagot egy [alkalmazáscsomag](./batch-application-packages.md). Az alkalmazáscsomag automatikusan telepíti a készletek, adja hozzá a készletkonfigurációhoz egy alkalmazáscsomag-hivatkozást.
>

## <a name="throttle-and-sample-data"></a>Szabályozás és mintaadatok 

Az éles környezetben futó Azure Batch-alkalmazások nagyméretű jellege miatt előfordulhat, hogy szeretné korlátozni a költségek kezelése az Application Insights által gyűjtött adatok mennyisége. Lásd: [Application Insights-mintavétel](../azure-monitor/app/sampling.md) ennek érdekében bizonyos mechanizmusok számára.


## <a name="next-steps"></a>További lépések
* Tudjon meg többet [Application Insights](../azure-monitor/app/app-insights-overview.md).

* Az Application Insights támogatja a más nyelveken, tekintse meg a [nyelvek, platformok és integrációk dokumentáció](../azure-monitor/app/platforms.md).


