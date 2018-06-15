---
title: Az Azure Application insights szolgáltatással kötegelt figyelése |} Microsoft Docs
description: Útmutató az Azure Application Insights könyvtár használata az Azure Batch .NET alkalmazások állíthatnak be.
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
ms.openlocfilehash: 9f989ada01a2ffced509b42df9e46aa001386ab6
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077394"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Figyelheti és az Application insights szolgáltatással az Azure Batch .NET alkalmazások hibakeresése

[Az Application Insights](../application-insights/app-insights-overview.md) egy elegáns és hatékony módot kínál a fejlesztők számára a központilag telepített Azure-szolgáltatások figyelése és a hibakeresési alkalmazások. Az Application Insights figyelő teljesítményszámlálók és kivételeket, valamint az eszköz a kód használata egyéni metrikákkal és nyomkövetés. Az Application Insights integrálása az Azure Batch alkalmazás lehetővé teszi viselkedések átfogó megismerésében, és problémák kivizsgálásában közel valós időben.

Ez a cikk bemutatja, hogyan lehet felvenni és az Application Insights-könyvtár konfigurálása az Azure Batch .NET megoldásba történő és állíthatnak be az alkalmazás kódjában. Ezenfelül itt látható módon figyelheti az alkalmazást az Azure-portálon, és egyéni irányítópultok létrehozása. Az Application Insights más nyelven támogatása érdekében tekintse meg a [nyelveket, platformokról és integrációk dokumentáció](../application-insights/app-insights-platforms.md).

Egy minta C# kóddal: Ez a cikk kísérő megoldás a [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights). Ebben a példában az Application Insights instrumentation kódot, hogy hozzáadja a [TopNWords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) példa. Ha nem ismeri az adott példa, próbálja meg kialakításához és futtatásához először TopNWords. Ez segítségére lesz a bemeneti BLOB több számítási csomóponton párhuzamosan készlete feldolgozási alapvető kötegelt munkafolyamat. 

## <a name="prerequisites"></a>Előfeltételek
* [Visual Studio IDE](https://www.visualstudio.com/vs) (Visual Studio 2015-öt vagy újabb verzió)

* [A Batch-fiók és a kapcsolt tárfiókra](batch-account-create-portal.md)

* [Application Insights-erőforrás](../application-insights/app-insights-create-new-resource.md)
  
   * Az Azure-portál használatával hozzon létre egy Application Insights *erőforrás*. Válassza ki a *általános* **alkalmazástípus**.

   * Másolás a [instrumentation kulcs](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key) a portálról. Szükség van a cikk későbbi részében.
  
  > [!NOTE]
  > Előfordulhat, hogy [felszámított](https://azure.microsoft.com/pricing/details/application-insights/) az Application Insights tárolt adatokat. Ez magában foglalja a diagnosztikai és a figyelési cikkben szereplő adatok.
  > 

## <a name="add-application-insights-to-your-project"></a>Az Application Insights hozzáadása a projekthez

A **Microsoft.ApplicationInsights.WindowsServer** NuGet-csomagot és annak függőségeit szükségesek a projekthez. Adja hozzá, vagy állítsa vissza őket az alkalmazás-projekthez. A csomag telepítéséhez használja a `Install-Package` parancs vagy a NuGet Package Manager.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
Az Application Insights hivatkozzon a .NET-alkalmazás használatával az **Microsoft.ApplicationInsights** névtér.

## <a name="instrument-your-code"></a>Állíthatnak be a kódot

Állíthatnak be a kódot, hogy a megoldás létre kell hoznia egy Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient). A példában a TelemetryClient a konfigurációját az betölti a [ApplicationInsights.config](../application-insights/app-insights-configuration-with-applicationinsights-config.md) fájlt. Ügyeljen arra, hogy az ApplicationInsights.config frissíteni fogja a következő projektek az Application Insights instrumentation kulccsal: Microsoft.Azure.Batch.Samples.TelemetryStartTask és TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Is hozzáadhat a instrumentation kulcs TopNWords.cs fájlban.

TopNWords.cs a példában a következő [instrumentation hívások](../application-insights/app-insights-api-custom-events-metrics.md) a Application Insights API:
* `TrackMetric()` -Nyomon követi, milyen hosszú, átlagos, a számítási csomópont a szükséges szövegfájl letöltéséhez szükséges.
* `TrackTrace()` -Hozzáadja a kódot hibakeresési hívások.
* `TrackEvent()` -A nyomait érdekes eseményeket rögzíteni.

Ebben a példában a kivételkezelés szándékosan kihagyása. Ehelyett az Application Insights automatikusan a jelentés nem kezelt kivételek, ami jelentősen növeli a hibakeresési élményt. 

Az alábbi kódrészletben azt ábrázolja, hogyan alkalmazza ezeket a módszereket.

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

### <a name="azure-batch-telemetry-initializer-helper"></a>Azure Batch telemetriai inicializáló segítő
Egy adott kiszolgálót és példányt a telemetriai adatokat a jelentést az Application Insights használja az alapértelmezett értékeket Azure Virtuálisgép-szerepkör és a virtuális gép nevét. Azure Batch összefüggésében a példa bemutatja, hogyan használja a készlet nevét, és ehelyett számítási csomópont neve. Használja a [telemetriai inicializáló](../application-insights/app-insights-api-filtering-sampling.md#add-properties) az alapértelmezett érték felülírására. 

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

Ahhoz, hogy a telemetria-inicializáló, az ApplicationInsights.config fájl a TopNWordsSample projektben az alábbiakat tartalmazza:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>A feladat és a feladatok közé tartozik az Application Insights bináris fájljainak frissítése

Ahhoz, hogy az Application Insights működéséhez a számítási csomóponton győződjön meg arról, a bináris fájlok megfelelően vannak elhelyezve. Adja hozzá a szükséges bináris fájlokat a feladat erőforrásgyűjtemény-fájlok, így azok letöltése a feladatütemezés végrehajtja időpontjában. Az alábbi kódtöredékek Job.cs kód hasonlóak.

Először hozzon létre az Application Insights fájl feltöltéséhez statikus listáját.

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

Ezután hozzon létre az átmeneti fájlok, a feladat által használt.
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

A `FileToStage` metódus egy segítő függvényt a kódminta, amely lehetővé teszi, hogy egyszerűen feltölteni a fájlt a helyi lemez egy Azure Storage-blobba. Minden fájl később egy számítási csomópont, letöltése és a feladatütemezés által hivatkozott.

Végül adja hozzá a feladatokat a feladathoz, és az Application Insights szükséges bináris fájlokat tartalmazza.
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

## <a name="view-data-in-the-azure-portal"></a>Az Azure portálon adatok megtekintése

Most, hogy a feladat és az Application Insights használatához feladatok konfigurálását, a példában feladat futtatása a készletben. Navigáljon az Azure portálra, és nyissa meg az Application Insights-erőforrás kiépített. A készlet üzembe helyezése után el kell kezdenie, adatok továbbítására, és első naplózza. Ez a cikk többi csak néhány Application Insights szolgáltatás koppint, de nyugodtan felfedezése, mely a teljes körével.

### <a name="view-live-stream-data"></a>Élő adatfolyam-adatok megtekintése

Az alkalmazások Insights-erőforrás nyomkövetési napló megtekintéséhez kattintson **élő adatfolyam**. Az alábbi képernyőfelvételen látható a számítási csomópontok a készlet, például a CPU-használat egyes számítási csomópontjain származó élő adatok megtekintése.

![Élő stream számítási csomópont adatait](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>Nyomkövetési naplók megtekintése

Az alkalmazások Insights-erőforrás nyomkövetési napló megtekintéséhez kattintson **keresési**. Ez a nézet megjeleníti az Application Insights nyomkövetési adatokat, eseményeket és kivételek által rögzített diagnosztikai adatok listáját. 

Az alábbi képernyőfelvételen látható, hogyan tevékenység egyetlen nyomkövetés naplózott és hibakeresési célra később lekérdezése.

![Nyomkövetési naplók kép](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>Nem kezelt kivételek megtekintése

Az alábbi képek jeleníti meg, hogy miként naplózza az Application Insights a kivételek az alkalmazásból. Ebben az esetben az alkalmazás a Kivétel kiváltása, másodpercen belül is egy adott kivétel elemezze és diagnosztizálhatja a problémát.

![Nem kezelt kivételek](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Mérték blob letöltési ideje

Egyéni metrikák megtalálhatók a portálon értékes eszközt. Például megjelenítheti a átlagos idő alatt jutott minden számítási csomópont letölteni a szükséges szöveges fájlt dolgozott fel azt.

A minta-diagram létrehozásához:
1. Kattintson az Application Insights-erőforrás **Metrikaböngésző** > **Hozzáadás diagram**.
2. Kattintson a **szerkesztése** vettek fel a diagramon.
2. A diagram adatainak frissítéséhez az alábbiak szerint:
   * Állítsa be **diagramtípus** való **rács**.
   * Állítsa be **összesítési** való **átlagos**.
   * Állítsa be **szerint kell csoportosítani a** való **NodeId**.
   * A **metrikák**, jelölje be **egyéni** > **Blob letöltési másodpercben**.
   * Állítsa be a megjelenítendő **színpaletta** a megadott beállítás. 

![Egyes csomópontok BLOB letöltési ideje](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>Figyelő csomópontok folyamatosan

Talán észrevette, hogy minden metrika, többek között a teljesítményszámlálók csak rendszer naplóz, amikor a feladatok futnak. Ez a viselkedés akkor hasznos, mert azt korlátozza, hogy az Application Insights naplózza az adatokat. Van azonban esetekben ha mindig szeretne figyelni, a számítási csomópontok. Például akkor lehet, hogy fut nem ütemezett háttérműveletek a Batch szolgáltatás használatával. Ebben az esetben futtatásához hozzon létre egy felügyeleti folyamatot a számítási csomópont során. 

Ez a viselkedés egyik módja hozható létre egy folyamatot, amely betölti az Application Insights könyvtárban, és a háttérben fut. A példában a kezdő tevékenység betölti a bináris fájlok a gépen, és határozatlan ideig futó folyamatot követi. Konfigurálja az Application Insights konfigurációs fájlt a folyamat létrehozza a további adatok továbbra is érdekli, például a teljesítményszámlálókat.

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
    virtualMachineSize: "small",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));
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
> A megoldás kezelhetőség javítása érdekében a szerelvény a szolgáltatásajánlatban egy [alkalmazáscsomag](./batch-application-packages.md). Ezt követően az alkalmazáscsomag automatikusan a készletek, vegye fel a készlet konfiguráció egy alkalmazáscsomag-hivatkozást.
>

## <a name="throttle-and-sample-data"></a>Adatátviteli és a minta adatok 

Éles környezetben futó Azure Batch-alkalmazások nagyméretű jellege miatt érdemes korlátozni a költségek kezelésére az Application Insights által gyűjtött adatok mennyisége. Lásd: [az Application Insightsban mintavételi](../application-insights/app-insights-sampling.md) néhány szerkezetek ennek érdekében.


## <a name="next-steps"></a>További lépések
* További információ [Application Insights](../application-insights/app-insights-overview.md).

* Az Application Insights más nyelven támogatása érdekében tekintse meg a [nyelveket, platformokról és integrációk dokumentáció](../application-insights/app-insights-platforms.md).


