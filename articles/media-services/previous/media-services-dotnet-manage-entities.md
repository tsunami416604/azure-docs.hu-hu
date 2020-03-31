---
title: Eszközök és kapcsolódó entitások kezelése a Media Services .NET SDK szolgáltatással
description: Ismerje meg, hogyan kezelheti az eszközöket és a kapcsolódó entitásokat a Media Services SDK for .NET szolgáltatással.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a686465b0006c2e9aac6e06cb4ab12d30921e8c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251140"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Eszközök és kapcsolódó entitások kezelése a Media Services .NET SDK szolgáltatással
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [Többi](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez a témakör bemutatja, hogyan kezelheti az Azure Media Services-entitásokat a .NET használatával.

2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. 2017. április 1-jén például a fiókban lévő bármely feladatrekord automatikusan törlődik. Ha archiválnia kell a feladat/feladat adatait, használhatja a jelen témakörben leírt kódot.

## <a name="prerequisites"></a>Előfeltételek

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 

## <a name="get-an-asset-reference"></a>Eszközhivatkozás beszerzése
Gyakori feladat egy meglévő eszközre való hivatkozás beszerzése a Media Servicesszolgáltatásban. A következő kód példa bemutatja, hogyan kaphat egy eszköz hivatkozást a kiszolgáló környezeti objektum eszközök gyűjteménye alapján egy eszköz azonosító. A következő kód példa egy Linq lekérdezést használ egy meglévő IAsset-objektumra való hivatkozás lekéréséhez.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Az összes eszköz listázása
A storage-ban lévő eszközök számának növekedésével hasznos lehet az eszközök listázása. A következő kód példa bemutatja, hogyan kell iterálni a kiszolgálói környezet objektum eszközök gyűjteménye. Minden egyes eszköz, a kód példa is írja néhány tulajdonság értékeit a konzolra. Például minden eszköz sok médiafájlt tartalmazhat. A példakód az egyes eszközökhöz társított összes fájlt kiírja.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Álláshivatkozás beszerezni

Ha a Media Services-kódban dolgozik a feldolgozási feladatokkal, gyakran kell egy azonosítón alapuló meglévő feladatra mutató hivatkozást kapnia. A következő kód példa bemutatja, hogyan lehet egy iJob-objektumra mutató hivatkozást beszerezni a Feladatok gyűjteményből.

Előfordulhat, hogy egy hosszú ideig futó kódolási feladat indításakor álláshivatkozást kell beszereznie, és ellenőriznie kell a feladat állapotát egy szálon. Ilyen esetekben, amikor a metódus visszatér egy szál, be kell olvasnia egy feladatra frissített hivatkozást.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Állások és eszközök listázása
Fontos kapcsolódó feladat az eszközök listázása a hozzájuk tartozó feladattal a Media Services szolgáltatásban. A következő kód példa bemutatja, hogyan kell felsorolni az egyes IJob-objektumok, majd minden feladat, megjeleníti a feladat tulajdonságait, az összes kapcsolódó feladatok, az összes bemeneti eszközök és az összes kimeneti eszközök. A példában szereplő kód számos más feladathoz is hasznos lehet. Ha például egy vagy több korábban futtatott kódolási feladat kimeneti eszközeit szeretné felsorolni, ez a kód bemutatja, hogyan érheti el a kimeneti eszközöket. Ha egy kimeneti eszközre hivatkozik, a tartalmat letöltheti vagy URL-eket adhat meg más felhasználóknak vagy alkalmazásoknak. 

Az eszközök szállításának lehetőségeiről további információt [az Eszközök szállítása a Media Services SDK for .NET szolgáltatással című témakörben talál.](media-services-deliver-streaming-content.md)

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Az összes hozzáférési házirend listázása
A Media Services ben hozzáférési szabályzatot definiálhat egy eszközön vagy annak fájljain. A hozzáférési házirend határozza meg egy fájl vagy egy eszköz engedélyeit (milyen típusú hozzáférés és időtartam). A Media Services-kódban általában egy hozzáférési szabályzatot úgy határoz meg, hogy létrehoz egy IAccessPolicy objektumot, majd társítja azt egy meglévő eszközhöz. Ezután hozzon létre egy ILocator objektumot, amely lehetővé teszi, hogy közvetlen hozzáférést biztosítson az eszközökhöz a Media Services-ben. A dokumentációs sorozatot kísérő Visual Studio-projekt számos kódpéldát tartalmaz, amelyek bemutatják, hogyan hozhat létre és rendelhet hozzá hozzáférési szabályzatokat és helymeghatározókat az eszközökhöz.

A következő példa kód bemutatja, hogyan listázhatja a kiszolgáló összes hozzáférési házirendét, és megmutatja az egyes engedélyek típusát. A hozzáférési házirendek megtekintésének másik hasznos módja, ha felsorolja a kiszolgálón található összes ILocator objektumot, majd minden lokátor esetében az AccessPolicy tulajdonság használatával listázhatja a kapcsolódó hozzáférési házirendet.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Hozzáférési házirendek korlátozása 

>[!NOTE]
> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. 

Létrehozhat például egy általános szabályzatkészletet a következő kóddal, amely csak egyszer futna az alkalmazásban. Az azonosítókat naplózhatja egy naplófájlba későbbi használatra:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Ezután a kódban lévő azonosítókat a következőkkel használhatja:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Az összes lokátor felsorolása
A lokátor egy URL-cím, amely közvetlen elérési utat biztosít egy eszköz eléréséhez, valamint az eszközhöz a lokátor társított hozzáférési szabályzata által meghatározott engedélyeket. Minden eszköz rendelkezhet egy iLocator-objektumok gyűjteménye a Lokátorok tulajdonságához társítva. A kiszolgálókörnyezet rendelkezik egy lokátorok gyűjteményével is, amely tartalmazza az összes lokátort.

A következő kódpélda a kiszolgáló összes lokátorát sorolja fel. Minden lokátor, azt mutatja, a kapcsolódó eszköz és hozzáférési szabályzat azonosítóját. Azt is megjeleníti az engedélyek típusát, a lejárati dátumot, és a teljes elérési utat az eszközhöz.

Vegye figyelembe, hogy egy eszköz lokátor elérési útja csak egy alap URL-címet az eszközhöz. Ha közvetlen elérési utat szeretne létrehozni az egyes fájlokhoz, amelyeket egy felhasználó vagy alkalmazás megkereshet, a kódnak hozzá kell adnia az adott fájl elérési útját a lokátor elérési úthoz. Ennek módjáról az [Eszközök szállítása a Media Services SDK for .NET szolgáltatással](media-services-deliver-streaming-content.md)című témakörben olvashat bővebben.

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Számbavétel entitások nagy gyűjteményein keresztül
Entitások lekérdezésekénél, egyszerre legfeljebb 1000 entitás tért vissza, mert a nyilvános REST v2 1000-es eredményeket korlátozza a lekérdezési eredményeket. A Kihagyás és a Take műveletet kell használnia, amikor entitások nagy gyűjteményein keresztül kell felsorolnia. 

A következő függvény végighalad a megadott Media Services-fiók összes feladatán. A Media Services 1000 feladatot ad vissza a Feladatgyűjteményben. A függvény a Kihagyás és a Take funkciót használja annak érdekében, hogy az összes feladat fel legyen sorolva (abban az esetben, ha több mint 1000 feladat van a fiókjában).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Eszköz törlése
A következő példa töröl egy eszközt.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Feladat törlése
Egy feladat törléséhez ellenőriznie kell a feladat állapotát az állapot tulajdonságban jelzettmódon. A befejezett vagy visszavont feladatok törölhetők, míg az egyes más állapotokban lévő feladatokat, például a várólistán lévő, ütemezett vagy feldolgozási feladatokat először meg kell szakítani, majd törölhetők.

A következő kódpélda egy feladat törlésének módját mutatja be a feladatállapotok ellenőrzésével, majd az állapot befejezésekor vagy megszakításakor történő törléssel. Ez a kód a témakör előző szakaszát, a feladatra mutató hivatkozás megszerzéséhez: Álláshivatkozás beszerzése.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Hozzáférési házirend törlése
A következő kód példa bemutatja, hogyan lehet egy hivatkozást egy házirend-azonosító alapján egy hozzáférési szabályzatra, majd törölni a házirendet.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

