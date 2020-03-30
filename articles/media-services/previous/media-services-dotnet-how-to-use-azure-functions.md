---
title: Azure-funkciók fejlesztése a Media Services szolgáltatással
description: Ez a témakör bemutatja, hogyan kezdheti el az Azure Functions fejlesztését a Media Services használatával az Azure Portalhasználatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251166"
---
# <a name="develop-azure-functions-with-media-services"></a>Azure-funkciók fejlesztése a Media Services szolgáltatással

Ez a cikk bemutatja, hogyan kezdheti el a Media Services t használó Azure-függvények létrehozását. A cikkben definiált Azure-függvény egy új MP4-fájlok **bemenetének** nevezett tárfiók-tárolót figyel. Miután egy fájlt a tárolóba dobott, a blob eseményindító végrehajtja a függvényt. Az Azure-függvények áttekintéséhez [tekintse át az áttekintést](../../azure-functions/functions-overview.md) és az Azure-függvények szakasz egyéb **témaköreit.**

Ha az Azure Media Services szolgáltatást használó meglévő Azure-függvényeket szeretné feltárni és telepíteni, olvassa el a [Media Services Azure Functions című itt- és állapotban való használatát.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) Ez a tárház olyan példákat tartalmaz, amelyek a Media Services használatával jelenítik meg a tartalom közvetlenül a blobstorageból, kódolásból és tartalom blobstorage-ból történő tartalombetöltésével kapcsolatos munkafolyamatokat. Emellett példákat is tartalmaz a webhookokon és az Azure-várólistákon keresztüli feladatértesítések figyelésére. A [Függvények](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) is fejlesztheti a példák alapján a Media Services Azure Functions tárházban. A függvények üzembe helyezéséhez nyomja meg a **Központi telepítés az Azure-ba** gombot.

## <a name="prerequisites"></a>Előfeltételek

- Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Ha olyan Azure Functions-et hoz létre, amely műveleteket hajt végre az Azure Media Services (AMS) fiókjában, vagy meghallgatja a Media Services által küldött eseményeket, hozzon létre egy AMS-fiókot az [itt](media-services-portal-create-account.md)leírtak szerint.
    
## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be az Azure-fiókjával.
2. Hozzon létre egy függvényalkalmazást [az itt](../../azure-functions/functions-create-function-app-portal.md)leírtak szerint.

>[!NOTE]
> A **StorageConnection** környezeti változóban megadott tárfióknak (lásd a következő lépést) ugyanabban a régióban kell lennie, mint az alkalmazásnak.

## <a name="configure-function-app-settings"></a>A függvényalkalmazás beállításainak konfigurálása

A Media Services-funkciók fejlesztése során hasznos olyan környezeti változókhozzáadása, amelyeket a funkciók során használni fog. Az alkalmazásbeállítások konfigurálásához kattintson az Alkalmazásbeállítások konfigurálása hivatkozásra. További információ: [Az Azure Function alkalmazásbeállításainak konfigurálása.](../../azure-functions/functions-how-to-use-azure-function-app-settings.md) 

A jelen cikkben definiált függvény feltételezi, hogy az alkalmazásbeállításokban a következő környezeti változók vannak:

**AMSAADTenantDomain**: Azure AD-bérlői végpont. Az AMS API-hoz való csatlakozásról ebben a [cikkben](media-services-use-aad-auth-to-access-ams-api.md) olvashat bővebben.

**AMSRESTAPIEndpoint**: URI, amely a REST API-végpontot jelöli. 

**AMSClientId**: Azure AD alkalmazásügyfél-ügyfélazonosító.

**AMSClientSecret**: Azure AD alkalmazásügyfél-titkos.

**StorageConnection**: a Media Services-fiókhoz társított fiók tárolókapcsolata. Ez az érték a **function.json** fájlban és a **run.csx** fájlban használatos (lásd alább).

## <a name="create-a-function"></a>Függvény létrehozása

Miután a függvényalkalmazás üzembe került, megtalálhatja azt az **App Services** Azure Functions között.

1. Válassza ki a függvényalkalmazást, és kattintson **az Új függvény gombra.**
2. Válassza ki a **C#** nyelvet és **az adatfeldolgozási forgatókönyvet.**
3. Válassza **a BlobTrigger** sablont. Ez a függvény akkor aktiválódik, ha egy blob feltölti a **bemeneti** tárolóba. A **bemeneti** név a következő lépésben az **Elérési útban**van megadva.

    ![Fájlokat](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Miután kiválasztotta a **BlobTrigger**lehetőséget, néhány további vezérlő jelenik meg a lapon.

    ![Fájlokat](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kattintson **a Létrehozás gombra.** 

## <a name="files"></a>Fájlok

Az Azure-függvény kódfájlokhoz és más, ebben a szakaszban ismertetett fájlokhoz van társítva. Amikor az Azure Portalon hozzon létre egy függvényt, **function.json** és **run.csx** jönnek létre az Ön számára. **Project.json** fájlt kell hozzáadnia vagy feltöltenie. A szakasz további részei röviden ismertetik az egyes fájlokat, és ismertetik azok definícióit.

![Fájlokat](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

A function.json fájl határozza meg a függvénykötéseket és más konfigurációs beállításokat. A futásidejű használja ezt a fájlt, hogy meghatározza az eseményeket a figyeléshez, és hogyan lehet adatokat átadni, és adatokat afüggvény-végrehajtás. További információ: [Azure-függvények HTTP és webhook kötések.](../../azure-functions/functions-reference.md#function-code)

>[!NOTE]
>Állítsa a **letiltott** tulajdonságot **true** értékre, hogy megakadályozza a funkció végrehajtását. 

Cserélje le a meglévő function.json fájl tartalmát a következő kódra:

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

### <a name="projectjson"></a>projekt.json

A project.json fájl függőségeket tartalmaz. Íme egy példa a **project.json** fájlra, amely tartalmazza a Nuget szükséges .NET Azure Media Services-csomagjait. Ne feledje, hogy a verziószámok a csomagok legújabb frissítéseivel változnak, ezért erősítse meg a legújabb verziókat. 

Adja hozzá a következő definíciót a project.json hoz. 

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
    
### <a name="runcsx"></a>futtatás.csx

Ez a funkció C# kódja.  Az alábbiakban meghatározott függvény egy **bemenetnevű** tárfiók-tárolót figyel (ez az, ami az elérési úton meg van adva) az új MP4-fájlokhoz. Miután egy fájlt a tárolóba dobott, a blob eseményindító végrehajtja a függvényt.
    
Az ebben a szakaszban meghatározott példa bemutatja a 

1. hogyan lehet bedolgozni egy eszközt egy Media Services-fiókba (egy blob aMS-eszközbe történő kezelésével), és 
2. a Media Encoder Standard "Adaptive Streaming" készletét használó kódolási feladat elküldése.

A valós életben valószínűleg szeretné nyomon követni a feladat előrehaladását, majd közzé tenni a kódolt eszköz. További információ: [Az Azure WebHooks használata a Media Services-feladatértesítések figyeléséhez](media-services-dotnet-check-job-progress-with-webhooks.md)című témakörben található. További példák: [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Cserélje le a meglévő run.csx fájl tartalmát a következő kódra: Miután befejezte a funkció definiálását, kattintson a **Mentés és futtatás gombra.**

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

## <a name="test-your-function"></a>A függvény tesztelése

A funkció teszteléséhez fel kell töltenie egy MP4-fájlt a kapcsolati karakterláncban megadott tárfiók **bemeneti** tárolójába.  

1. Válassza ki a **StorageConnection** környezeti változóban megadott tárfiókot.
2. Kattintson **a Blobs gombra.**
3. Kattintson a **+ Tároló** elemre. Nevezze el a **tárolóbemenetet**.
4. Nyomja **le a Feltöltés gombot,** és keresse meg a feltölteni kívánt .mp4 fájlt.

>[!NOTE]
> Ha egy blob eseményindító t használ egy felhasználási terv, előfordulhat, hogy akár 10 perces késéssel feldolgozása új blobok után egy függvényalkalmazás tétlen. A függvényalkalmazás futása után a blobok feldolgozása azonnal. További információ: [Blob storage triggers and bindings](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>További lépések

Ezen a ponton készen áll a Media Services-alkalmazások fejlesztésének megkezdésére. 
 
Az Azure Functions és a Logic Apps azure Media Services használatával egyéni tartalom-létrehozási munkafolyamatok létrehozásához szükséges további részletekért és teljes mintákért/megoldásokért tekintse meg a [Media Services .NET Functions integrációs mintáját a GitHubon.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Lásd még: [Az Azure WebHooks használata a Media Services-feladatértesítések figyeléséhez a .NET](media-services-dotnet-check-job-progress-with-webhooks.md)használatával. 

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

