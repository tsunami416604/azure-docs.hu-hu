---
title: "A Media Services az Azure Functions fejlesztése"
description: "Ez a témakör bemutatja, hogyan az Azure Functions a Media Services elkezdje az Azure portál használatával."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 999f2cef7d70c4f1b45076300312664defdeb3f5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="develop-azure-functions-with-media-services"></a>A Media Services az Azure Functions fejlesztése

Ez a cikk bemutatja, hogyan Ismerkedés az Azure Functions Media Services használó létrehozása. Ebben a cikkben az Azure függvényt figyeli nevű fiók tárolót **bemeneti** új MP4-fájlokat. Miután egy fájlt a storage tárolóba megszakad, a blob eseményindító végrehajtja a függvény. Az Azure functions ellenőrzéséhez tekintse meg [áttekintése](../azure-functions/functions-overview.md) és egyéb témakörei a **az Azure functions** szakasz.

Ha szeretne vizsgálatát, és a meglévő Azure Functions, amely az Azure Media Services telepíteni, tekintse meg [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Ebben a tárházban található példák bemutatják, amely a Media Services használatával megjelenítése tartalom közvetlenül blobtárolóból, kódolási és tartalmat ír vissza a blob-tároló választásával dolgozhat fel kapcsolatos munkafolyamatok. Feladat értesítések Webhookok és Azure várólisták figyelése példákat is tartalmaz. A funkciók szereplő példák alapján is készíthet a [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) tárházba. Telepíti a Funkciók, nyomja meg a **az Azure telepítéséhez** gombra.

## <a name="prerequisites"></a>Előfeltételek

- Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Ha fog létrehozni az Azure Functions az Azure Media Services (AMS) fiók műveleteket vagy a Media Services által küldött események figyelésére, akkor hozzon létre AMS-fiók, leírtak [Itt](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. Nyissa meg az [Azure Portalt](http://portal.azure.com), és jelentkezzen be az Azure-fiókjával.
2. Függvény-alkalmazás létrehozása leírtak [Itt](../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> A storage-fiók, melyet a **StorageConnection** környezeti változó (lásd a következő lépés) kell lennie az alkalmazás ugyanabban a régióban.

## <a name="configure-function-app-settings"></a>Függvény-Alkalmazásbeállítások konfigurálása

A Media Services funkciók fejlesztésekor célszerű adja hozzá a környezeti változókat, amelyek a funkciók használjuk. Beállítások konfigurálásához kattintson az alkalmazás beállításainak konfigurálása hivatkozásra. További információkért lásd: [konfigurálása az Azure-függvény Alkalmazásbeállítások](../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

A függvényt, ez a cikk feltételezi, hogy a beállítások a következő környezeti változók vannak:

**AMSAADTenantDomain**: az Azure AD bérlő végpont. Az AMS API összekapcsolásával kapcsolatos további információkért lásd: [ez](media-services-use-aad-auth-to-access-ams-api.md) cikk.

**AMSRESTAPIEndpoint**: URI, amely a REST API-végpontot jelenti. 

**AMSClientId**: az Azure AD alkalmazás ügyfél-azonosítót.

**AMSClientSecret**: az Azure AD alkalmazás titkos ügyfélkulcsot.

**StorageConnection**: a Media Services-fiókhoz társított fiók tárolókapcsolat. Ez az érték szerepel a **function.json** fájl és **run.csx** fájlt (lásd lejjebb).

## <a name="create-a-function"></a>Függvény létrehozása

A függvény az alkalmazást telepíti, ha megtalálja között **alkalmazásszolgáltatások** Azure Functions.

1. Válassza ki a függvény alkalmazást, majd kattintson a **új függvény**.
2. Válassza ki a **C#** nyelvi és **adatfeldolgozási** forgatókönyv.
3. Válasszon **BlobTrigger** sablont. Ez a funkció akkor váltódik ki, amikor egy blob be van töltve a **bemeneti** tároló. A **bemeneti** neve van megadva az a **elérési**, a következő lépésben.

    ![fájl megjelenítése](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Miután **BlobTrigger**, néhány további vezérlők megjelenik a lapon.

    ![fájl megjelenítése](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kattintson a **Create** (Létrehozás) gombra. 

## <a name="files"></a>Fájlok

Az Azure-függvény kódot és egyéb fájlokat ebben a szakaszban ismertetett társítva. Ha az Azure-portál használatával hozzon létre egy függvényt **function.json** és **run.csx** , a rendszer létrehozza. Adja hozzá, vagy töltse fel kell egy **project.json** fájlt. Ez a szakasz a többi röviden bemutatja az egyes fájlok biztosít, és bemutatja a definíciójukat.

![fájl megjelenítése](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

A function.json fájl határozza meg, a függvénykötés és egyéb konfigurációs beállításokkal. A futtatókörnyezet ezt a fájlt határozza meg a figyelendő események és adja át az adatokat, és vissza adatokat, a függvény végrehajtása használja. További információkért lásd: [Azure functions HTTP és a webhook kötések](../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Állítsa be a **le van tiltva** tulajdonságot **igaz** megakadályozhatja, hogy a függvény a végrehajtás alatt. 

Cserélje le a meglévő function.json fájl tartalmát az alábbira:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

A project.json függőségeket tartalmaz. Íme egy példa **project.json** fájlt, amely tartalmazza a kötelező .NET Azure Media Services Nuget-csomagot. Vegye figyelembe, hogy a verziószámok módosítása legújabb frissítéseit a csomagokhoz, ellenőrizze a legújabb verzióját. 

Adja hozzá a következő definícióját project.json. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```
    
### <a name="runcsx"></a>run.csx

Ez a C#-kódban a függvénynél.  Figyelők alatt meghatározni a függvény egy fiókot a tároló nevű **bemeneti** (Ez mi az az elérési út lett megadva) új MP4-fájlokat. Miután egy fájlt a storage tárolóba megszakad, a blob eseményindító végrehajtja a függvény.
    
A jelen szakaszban meghatározott példa bemutatja 

1. hogyan tölti be az eszköz a Media Services-fiók (által másolás blob egy AMS adategységbe) és 
2. Hogyan egy kódolási feladat elküldése használó Media Encoder Standard által beállított "Adaptív Streameléshez".

A valós életben esetben valószínűleg szeretne nyomon követheti a feladat előrehaladását, és tegye közzé a kódolt objektumhoz. További információkért lásd: [használata Azure Webhookok figyelése a Media Services feladat értesítések](media-services-dotnet-check-job-progress-with-webhooks.md). További példákért lásd [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Cserélje le a meglévő run.csx fájl tartalmát az alábbira: Miután a kattintson a függvényt definiáló **mentéséhez, és futtassa**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

##<a name="test-your-function"></a>A függvény tesztelése

A függvény teszteléséhez töltse fel az MP4-fájlokat kell a **bemeneti** a tárfiók a kapcsolati karakterláncban megadott tároló.  

1. Válassza ki a megadott tárfiók a **StorageConnection** környezeti változó.
2. Kattintson a **Blobok**.
3. Kattintson a **+ tároló**. A tároló neve **bemeneti**.
4. Nyomja le az **feltöltése** , és keresse meg a feltölteni kívánt .mp4-fájlba.

>[!NOTE]
> Egy blob eseményindító használatakor a fogyasztás terven létezhet legfeljebb 10 perces késleltetést új blobok feldolgozása után egy függvény app inaktív állapotba került. A függvény alkalmazás futtatása után blobok feldolgozása azonnal megtörténik. További információkért lásd: [Blob-tároló eseményindítók és kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob#blob-storage-triggers-and-bindings).

## <a name="next-steps"></a>További lépések

Ekkor készen áll a Media Services-alkalmazás elkezdje. 
 
További részletek és a teljes mintát/megoldások Azure Functions és a Logic Apps használatának az Azure Media Services egyéni létrehozási munkafolyamatokat hozhat létre, a [Media Services .NET funkciók integrációs mintát a Githubon](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Lásd még [használata Azure Webhookok Media Services feladat értesítések küldése .NET a figyelheti](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

