---
title: Eszközök és a Media Services .NET SDK-val kapcsolódó entitások kezelése
description: Megtudhatja, hogyan kezelheti az eszközök és a kapcsolódó entitások a Media Services SDK-val .NET-keretrendszerhez készült.
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
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58802677"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Eszközök és a Media Services .NET SDK-val kapcsolódó entitások kezelése
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még [v3 a v2 migrálási útmutató](../latest/migrate-from-v2-to-v3.md)

Ez a témakör bemutatja, hogyan lehet .NET-tel az Azure Media Services-entitások kezelését.

2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. Például 2017. április 1. feladat rekordot a régebbi, mint a 2016. December 31-én fiókjában automatikusan törölve lesznek. Ha a feladatok/műveletek adatainak archiválásához van szüksége, használhatja a jelen témakörben található kódot.

## <a name="prerequisites"></a>Előfeltételek

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

## <a name="get-an-asset-reference"></a>Az Eszközintelligencia-hivatkozás beolvasása
A gyakori feladat, hogy egy meglévő eszköz egy hivatkozást kap a Media Services. Az alábbi példakód bemutatja, hogyan kezdheti egy adategység-hivatkozást a kiszolgálón az eszközök gyűjteményből context objektumot, egy eszköz azonosító alapján Az alábbi példakód egy Linq-lekérdezésekre használatával kérje le a meglévő IAsset objektumhoz hivatkozását.

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

## <a name="list-all-assets"></a>Az összes eszköz listája
Tároló található eszközök száma növekszik, hasznos lehet az eszközök listáját. Az alábbi példakód bemutatja a kiszolgáló context objektumra vonatkozó eszközök gyűjtemény iterálódnak. Az egyes eszközök a példakód is ír a tulajdonságértékek némelyike a konzolon. Minden eszköz például számos media fájlokat tartalmazza. A mintakód ír, az minden eszközhöz hozzárendelt összes fájl ki.

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

## <a name="get-a-job-reference"></a>Kérje le a feladat hivatkozását

Amikor feldolgozási feladatokat a Media Services-code-ban dolgozik, gyakran kell beolvasása azonosító alapján egy meglévő feladat hivatkozás Az alábbi példakód bemutatja a feladatok gyűjteményből egy IJob objektumra mutató hivatkozás beszerzése.

Kérje le a feladat hivatkozását, a hosszú ideig futó kódolási feladat indításakor, és ellenőrizze a feladat állapotát a szál szükségessé. Ilyen esetben, ha a metódus visszaadja a szálból kell lekérni frissített hivatkozás egy feladathoz.

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

## <a name="list-jobs-and-assets"></a>Listázhatók a feladatok és eszközök
Fontos kapcsolódó feladat, hogy a feladat a Media Services eszközök listája. Az alábbi példakód bemutatja, hogyan kell felsorolni az egyes IJob objektumot, és ezután az egyes feladatokhoz, jeleníti meg a feladattal kapcsolatos tulajdonságok, az összes kapcsolódó feladatok, összes bemeneti, eszközök és az összes kimeneti objektumok. Ebben a példában a kódot számos más feladatok elvégzéséhez hasznos lehet. Például ha azt szeretné, a kimeneti objektumok származó egy vagy több kódolási feladatot hajt végre, amelyet korábban futtatott listázásához, ez a kód bemutatja, hogyan érheti el a kimeneti. Ha a kimeneti adategység egy hivatkozást, majd juttathat el a tartalmat más felhasználók vagy alkalmazások letöltésével azt, vagy URL-címek megadása. 

Eszközök kidolgozását lehetőségekről további információkért lásd: [eszközöket biztosít a Media Services SDK for .NET csomaggal](media-services-deliver-streaming-content.md).

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

## <a name="list-all-access-policies"></a>Minden hozzáférési szabályzatok listája
A Media Services szolgáltatásban megadhatja egy hozzáférési házirend egy eszköz vagy a fájlokat. Hozzáférési házirend határozza meg az engedélyeket egy fájl vagy egy eszköz (milyen típusú hozzáférést, és időtartama). A Media Services-kódba általában megadhat egy hozzáférési szabályzatot IAccessPolicy objektum létrehozásával, és társít egy meglévő eszköz. Ezután hozzon létre egy ILocator objektum, amely lehetővé teszi, hogy közvetlen hozzáférést biztosíthat az eszközök a Media Services. A Visual Studio-projekt dokumentáció adatsorozat megnyitásáról több hitelesítésikód-példák azt mutatják be, hogyan hozhat létre és hozzáférési házirendek, keresők hozzárendelése eszközök tartalmazza.

Az alábbi példakód bemutatja, hogyan listázhatja a kiszolgálón lévő összes hozzáférési házirendek és kapcsolódó engedélyek típusát jeleníti meg. Hozzáférési szabályzatok megtekintéséhez másik hasznos módja az, hogy a kiszolgáló összes ILocator objektumok listája, és ezután minden keresőként listázhatja a társított hozzáférési szabályzat a AccessPolicy tulajdonsága segítségével.

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
    
## <a name="limit-access-policies"></a>Korlát hozzáférési szabályzatok 

>[!NOTE]
> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. 

Ha például egy általános házirendkészletben hozhat létre a következő kódra, amely csak akkor futtassa egyszer az alkalmazásban. Azonosítók bejelentkezhet egy naplófájlba későbbi használat céljából:

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

Ezután használhatja a meglévő azonosítók a kódban, ehhez hasonló:

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

## <a name="list-all-locators"></a>Minden kereső listázása
Egy kereső egy URL-címet, amely bemutatja az közvetlen eléréséhez egy eszköz, az eszköz engedélyekkel együtt, a lokátor társított hozzáférési szabályzatban meghatározottak szerint. Minden eszköz rendelkezhet társított a keresők tulajdonában lévő ILocator objektumok gyűjteményét. Kiszolgálói környezetbe is tartalmaz, amely tartalmazza az összes keresőt Lokátorok gyűjtemény.

Az alábbi példakód sorolja fel az összes keresőt a kiszolgálón. Minden kereső azt mutatja be, a kapcsolódó eszköz- és hozzáférési házirend azonosítója. Az eszköz engedélyek, a lejárati dátum és a teljes elérési útja típusát is megjeleníti.

Vegye figyelembe, hogy-e az eszköz csak az alap URL-eszköz kereső elérési útja. Egy felhasználó vagy alkalmazás sikerült keresse meg az egyes fájlok közvetlen elérési utat hoz létre, a kód hozzá kell az adott fájl elérési útja a lokátor elérési utat. Ennek módjáról további információkért lásd a témakör [eszközöket biztosít a Media Services SDK for .NET csomaggal](media-services-deliver-streaming-content.md).

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

## <a name="enumerating-through-large-collections-of-entities"></a>Nagy kolekce entit számbavétele
Entitások lekérdezésekor korlátozva van az 1000 entitások adja vissza egy adott időpontban, mert a nyilvános REST v2 korlátozza az 1000 eredmények lekérdezési eredményeket. Kell használnia a Kihagyás gombra, és végezze el a nagy kolekce entit keresztül számbavétele során. 

A következő függvény végighalad a megadott Media Services-fiók összes feladatot. A Media Services 1000 feladatok feladatok gyűjteményt adja vissza. A funkció lehetővé teszi, hogy használja a Skip, és hajtsa végre a megfelelő, győződjön meg arról, hogy az összes feladatok felsorolása (abban az esetben a fiókjában több mint 1000 feladatok rendelkezik).

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

## <a name="delete-an-asset"></a>Az eszköz törlése
A következő példa törli egy eszköz.

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
Feladat törlése, ellenőrizze a feladat állapotát az állapot tulajdonság. Feladatok, amelyek a befejeződött vagy meg lett szakítva, amíg a feladatok, amelyek az egyes államokban például sorba állított, ütemezett vagy feldolgozásra, először vissza kell vonni, és ezután azok törölhetők lehet törölni.

Az alábbi példakód mutatja be a feladatállapotok majd törlése, ha az állapot befejeződött, vagy meg lett szakítva egy feladat törlése módját. Ez a kód az előző szakaszban ebben a témakörben egy feladat-hivatkozás beszerzése a függ: Kérje le a feladat hivatkozását.

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
Az alábbi példakód bemutatja, hogy miként lehet a házirend-azonosító alapján hozzáférési házirend egy hivatkozást, majd törli a szabályzatot.

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

