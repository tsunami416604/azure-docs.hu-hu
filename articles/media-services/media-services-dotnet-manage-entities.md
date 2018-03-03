---
title: "Eszközök és a kapcsolódó entitásokból Media Services .NET SDK kezelése"
description: "Megtudhatja, hogyan kezelheti az eszközök és a kapcsolódó entitásokból a Media Services SDK-val a .NET-hez."
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: fd8f89bc842b33576dc0f85ab606dfe3628480ed
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Eszközök és a kapcsolódó entitásokból Media Services .NET SDK kezelése
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

Ez a témakör bemutatja, hogyan kezelheti az Azure Media Services entitások a .NET. 

>[!NOTE]
> 2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. Például a 2017. április 1. a régebbi, mint a 2016. December 31-én fiókjában feladat rekordot automatikusan törlődni fog. Ha a feladat vagy tevékenység információk archiválása van szüksége, használhatja a kód a jelen témakörben ismertetett.

## <a name="prerequisites"></a>Előfeltételek

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

## <a name="get-an-asset-reference"></a>Egy eszköz mutató hivatkozás beszerzése
Gyakran egy meglévő eszköz mutató hivatkozás a Media Services segítségével. Az alábbi példakód bemutatja, hogyan kaphat az eszköz hivatkozás a kiszolgálón, az eszközök gyűjteményből környezeti objektumot, egy eszköz azonosító alapján Az alábbi példakód a Linq lekérdezés segítségével IAsset objektum való hivatkozás.

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

## <a name="list-all-assets"></a>Az összes eszköz felsorolása
Tároló található eszközök száma növekedésével fontos az eszközök listáját. Az alábbi példakód bemutatja, hogyan találta az eszközök gyűjtemény a kiszolgáló környezeti objektumon keresztül. Az egyes eszközök a Kódpélda is írja a tulajdonságértékek némelyike a konzolt. Például minden eszköz sok media fájlokat tartalmazza. A Kódpélda írja ki minden eszközhöz társított összes fájlt.

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

## <a name="get-a-job-reference"></a>Feladat hivatkozás

Használatakor a Media Services kódban feladatok feldolgozása, gyakran kell beszereznie egy hivatkozást egy meglévő feladat alapján azonosítóval. Az alábbi példakód bemutatja, hogyan lekérni a feladatok gyűjteményből IJob objektumra mutató hivatkozást.

Szükség lehet feladat hivatkozás beszerzése a hosszan futó kódolási feladat indításakor, és ellenőrizze a feladat állapotát a szálban. Ilyen esetekben amikor a metódus visszaadja egy olyan szálból, kell frissíteni hivatkozik egy feladat beolvasása.

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

## <a name="list-jobs-and-assets"></a>Lista feladatok és eszközök
Fontos kapcsolódó feladat lista eszközök és az ahhoz tartozó feladatot a Media Services-hoz. Az alábbi példakód bemutatja, hogyan listázhat minden IJob objektumon, és majd az egyes feladatokhoz, a feladat tulajdonságait jeleníti meg, az összes kapcsolódó feladatok, összes bemeneti, eszközök és az összes kimeneti eszközöket. Ebben a példában a kód számos más feladatok hasznos lehet. Például ha ki szeretné listázni a kimeneti eszközök egy vagy több kódolási feladatokból, korábban már futott, ez a kód bemutatja, hogyan a kimeneti eszközök eléréséhez. Ha egy kimeneti eszköz hivatkozást, majd biztosíthat a tartalmat más felhasználók vagy alkalmazások letöltheti, vagy URL-címek megadása. 

Eszközök kézbesítéséhez lehetőségekről további információkért lásd: [eszközöket biztosítanak a Media Services SDK for .NET](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Minden hozzáférési házirendek felsorolása
A Media Services szolgáltatásban a hozzáférési házirendek meg egy eszköz vagy annak fájljait. A hozzáférési házirendek meghatározása az engedélyeket egy fájl vagy egy eszköz (milyen típusú hozzáférést, és időtartama). A Media Services kódban általában definiált hozzáférési házirend egy IAccessPolicy objektum létrehozása és társítása egy meglévő eszközt. Ezután hozzon létre egy ILocator objektum, amely lehetővé teszi, hogy közvetlen hozzáférést biztosítani a Media Services eszközök. A Visual Studio-projekt, amely a dokumentáció sorozat társul több hitelesítésikód-példák bemutatják, hogyan hozzon létre és hozzáférési házirendek és a lokátorokat rendelje eszközök tartalmazza.

Az alábbi példakód mutatja be a kiszolgálón lévő összes hozzáférési házirendek listáját, és a egyes társított olyan engedélyek. Másik hasznos módja hozzáférési házirendeket, hogy a kiszolgálón az összes ILocator objektumok listája, majd az egyes lokátor listázhatja a társított hozzáférési házirendet a AccessPolicy tulajdonság használatával.

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
    
## <a name="limit-access-policies"></a>Korlát hozzáférési házirendek 

>[!NOTE]
> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. 

Például egy általános házirendcsoport hozhat létre a következő kóddal, amely csak akkor futtassa egyszer az alkalmazásban. A naplófájl későbbi használatra azonosítók jelentkezhet:

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

Ezt követően a meglévő azonosítók is használhatja a kódban ehhez hasonló:

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

## <a name="list-all-locators"></a>Minden keresők felsorolása
Egy kereső egy URL-címet, amely egy eszköz, az eszköz engedélyekkel együtt eléréséhez a lokátor társított hozzáférési házirend által meghatározott közvetlen útvonalat biztosít. Minden eszköz lehet a Lokátorokat tulajdonsága kapcsolódó ILocator objektumok gyűjteménye. A kiszolgáló a környezetben is, amely tartalmazza az összes keresők keresők gyűjteményével rendelkezik.

Az alábbi példakód a kiszolgálón az összes keresők sorolja fel. Az egyes lokátor mutatja a kapcsolódó eszköz és a hozzáférési házirend azonosítója. Az eszköz típusát és az engedélyek, a lejárati dátum, a teljes elérési útja is megjeleníti.

Vegye figyelembe, hogy-e az eszköz lokátor elérési útja csak egy alap URL-címet az eszközhöz. Amely egy felhasználó vagy alkalmazás sikerült keresse meg a fájlokat közvetlen elérési utat hoz létre, a kódot kell adnia a megadott fájl elérési útját a lokátor elérési utat. Ennek módjáról további információkért lásd: a témakör [eszközöket biztosítanak a Media Services SDK for .NET](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Az entitások nagy gyűjteményekre számbavétele
Entitások lekérdezésekor korlátozás van adja vissza egy időben, mert a nyilvános REST v2 korlátozza a lekérdezési eredmények 1000 eredmények 1000 entitások. Használja a Skip, és hajtsa végre a megfelelő, ha nagy gyűjteményekre entitások számbavétele kell. 

A következő függvény a megadott Media Services-fiók levő összes feladatnak végighalad. A Media Services 1000 feladatok Feladatgyűjteménynek adja vissza. A funkció lehetővé teszi, hogy használja a Skip, és győződjön meg arról, hogy az összes feladat hajtsa végre a megfelelő enumerálása (Ha a fiók rendelkezik 1000-nél több feladatok).

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

## <a name="delete-an-asset"></a>Egy eszköz törlése
A következő példa egy eszköz törlése.

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

## <a name="delete-a-job"></a>Egy feladat törlése
Törli a feladatot, ellenőrizze a feladat állapotát a State tulajdonsága. Feladatok befejeződött vagy érvénytelenített törölhetők, feladatok, amelyek az egyes állapotok például aszinkron, ütemezett vagy feldolgozásra, vissza kell vonni, és ezután törölje őket.

Az alábbi példakód mutatja egy módszert feladatállapotok ellenőrzése és majd törlésével a állapota nem fejeződött be, vagy visszavont egy feladat törlése. Ez a kód függ az előző szakaszban ebben a témakörben egy hivatkozást egy feladathoz kapcsolódnak: feladat hivatkozás.

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
Az alábbi példakód bemutatja, hogyan beszerzése egy házirend-azonosító alapján hozzáférési házirend egy hivatkozást, majd törölni szeretné a házirendet.

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


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

