---
title: A Media Services az Azure Functions fejlesztése
description: Ez a témakör bemutatja, hogyan és kezdje el fejleszteni a Media Services az Azure Functions az Azure portal használatával.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 428934a6e93ced56274bd27123bea9d92cd45240
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997940"
---
# <a name="develop-azure-functions-with-media-services"></a>A Media Services az Azure Functions fejlesztése

Ez a cikk bemutatja, hogyan létrehozása, amely a Media Services használata az Azure Functions használatába. Az Azure-függvény ebben a cikkben meghatározott figyeli a tárfiók tárolója nevű **bemeneti** új MP4-fájlokhoz. Ha egy fájl megszakad, a storage-tárolóba, a blob eseményindító függvény hajtja végre. Az Azure functions áttekintéséhez lásd: [áttekintése](../../azure-functions/functions-overview.md) és a témaköröket a **az Azure functions** szakaszban.

Ha azt szeretné, megismerését és üzembe helyezése meglévő Azure Functions, amely az Azure Media Services használatával, tekintse meg az [Media Services az Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Ez a tárház, amely a Media Services használatával munkafolyamatok fürtjét kapcsolódó tartalom közvetlenül a blob storage-ban kódolási és afelé biztonsági másolatot a blob storage megjelenítése Példák tartalmazza. Webhookok és az Azure-üzenetsorok használatával feladatértesítések figyelése példákat is tartalmaz. A függvények a példák alapján is fejleszthet a [Media Services az Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) tárház. A functions üzembe helyezése, nyomja le az **üzembe helyezés az Azure** gombra.

## <a name="prerequisites"></a>Előfeltételek

- Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Ha fog létrehozni, amelyek műveleteket hajthat végre az Azure Media Services (AMS) fiók vagy -Media Services által küldött események figyelése az Azure Functions, létre kell hoznia egy AMS-fiók, leírtak szerint [Itt](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. Nyissa meg az [Azure Portalt](http://portal.azure.com), és jelentkezzen be az Azure-fiókjával.
2. Hozzon létre egy függvényalkalmazást, amint [Itt](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> A megadott tárfiók a **StorageConnection** környezeti változót (lásd a következő lépésre) és az alkalmazás ugyanabban a régióban kell lennie.

## <a name="configure-function-app-settings"></a>A funkció beállításainak konfigurálása

A Media Services-funkciók fejlesztése, esetén praktikus környezeti változókat, amelyek fogja használni a teljes a függvények hozzáadásához. Az Alkalmazásbeállítások konfigurálása hivatkozásra kattintva beállításainak konfigurálása. További információkért lásd: [konfigurálása az Azure-Függvényalkalmazás-beállításokat](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

A függvény ebben a cikkben meghatározott feltételezi, hogy az alábbi környezeti változókat a alkalmazás beállításai:

**AMSAADTenantDomain**: Az Azure AD-bérlő végpont. Az AMS API-t kapcsolódás kapcsolatos további információkért lásd: [ez](media-services-use-aad-auth-to-access-ams-api.md) cikk.

**AMSRESTAPIEndpoint**:  URI-t, amely a REST API-végpont jelöli. 

**AMSClientId**: Az Azure AD alkalmazás ügyfél-azonosítót.

**AMSClientSecret**: Az Azure AD alkalmazás titkos ügyfélkulcsát.

**StorageConnection**: a Media Services-fiókhoz társított fiók tárolási kapcsolat. Ez az érték szerepel a **function.json** fájl és **run.csx** fájlt (lásd alább).

## <a name="create-a-function"></a>Függvény létrehozása

A függvényalkalmazás üzembe helyezése után található között **App Services** Azure Functions.

1. Válassza ki a függvényalkalmazást, és kattintson a **új függvény**.
2. Válassza ki a **C#** nyelvi és **adatfeldolgozási** forgatókönyv.
3. Válasszon **BlobTrigger** sablont. Ez a függvény akkor aktiválódik, amikor egy blob feltöltése a **bemeneti** tároló. A **bemeneti** neve van meghatározva a **elérési út**, a következő lépésben.

    ![fájl megjelenítése](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Miután kiválasztotta **BlobTrigger**, néhány további vezérlők jelennek meg az oldalon.

    ![fájl megjelenítése](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Kattintson a **Create** (Létrehozás) gombra. 

## <a name="files"></a>Fájlok

Az Azure-függvény kódja és egyéb fájlokat, az ebben a szakaszban leírt társítva. Ha az Azure portal használatával hozzon létre egy függvényt, **function.json** és **run.csx** jönnek létre az Ön számára. Adja hozzá, vagy töltse fel a **project.json** fájlt. Ez a szakasz a többi biztosít, amely röviden elmagyarázza az egyes fájlok, és megjeleníti a definíciójukat.

![fájl megjelenítése](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

A function.json fájlt határozza meg, a függvény kötéseket és egyéb konfigurációs beállításokkal. A modul használja ezt a fájlt határozza meg az események figyelésére, és adja át az adatokat, és adatokat adja vissza a függvény végrehajtási. További információkért lásd: [az Azure functions HTTP- és webhookkötések](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Állítsa be a **le van tiltva** tulajdonságot **igaz** megakadályozza, hogy a függvény végrehajtása. 

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

A project.json fájlban függőségeket tartalmaz. Íme egy példa **project.json** fájlt, amely tartalmazza a szükséges .NET Azure Media Services Nuget-csomagot. Vegye figyelembe, hogy a verziószámok módosítsa legújabb frissítéseit a csomagokat, ezért ellenőrizze a legújabb verziójú. 

Adja hozzá a következő definíciót project.json. 

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

Ez a C# kódot a függvényhez.  Figyelők az alábbiakban meghatározott függvény a tárfiók tárolója nevű **bemeneti** (Ez az elérési út megadottaknak) új MP4-fájlokhoz. Ha egy fájl megszakad, a storage-tárolóba, a blob eseményindító függvény hajtja végre.
    
Az ebben a szakaszban definiált példa bemutatja, hogyan 

1. hogyan lehet, hogy az eszköz egy Media Services-fiókba (által kiugrások egy blob egy AMS adategységbe) és a 
2. Hogyan elküldeni egy kódolási feladatot, amely használja a Media Encoder Standard a "Adaptív Streamelés" előbeállítást.

A valós életben a forgatókönyvben valószínűleg érdemes nyomon követheti a feladat állapotát, és tegye közzé a kódolt objektumhoz. További információkért lásd: [Azure Webhookok használata a Media Services feladatértesítések figyelésére használt](media-services-dotnet-check-job-progress-with-webhooks.md). További példák: [Media Services az Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Cserélje le a meglévő run.csx fájl tartalmát az alábbira: Ha elkészült a függvény meghatározása kattintson **mentés és Futtatás**.

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

A függvény teszteléséhez, fel kell töltenie az MP4-fájlokat a **bemeneti** tároló, amely a kapcsolati karakterláncban megadott tárfiók.  

1. Válassza ki a tárfiókot, amelyet a a **StorageConnection** környezeti változót.
2. Kattintson a **Blobok**.
3. Kattintson a **+ tároló**. A tároló neve **bemeneti**.
4. Nyomja meg **feltöltése** és keresse meg a feltölteni kívánt .mp4-fájlt.

>[!NOTE]
> Amikor egy blob eseményindító a Használatalapú csomag használata esetén lehet akár egy 10 perces késleltetést új blobok feldolgozása után egy függvényalkalmazás inaktív volt. Miután a függvényalkalmazás fut, blobok feldolgozása azonnal megtörténik. További információkért lásd: [Blob storage-eseményindítók és kötések](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>További lépések

Ezen a ponton készen áll egy Media Services-alkalmazás fejlesztése elindításához. 
 
További részletek és a teljes minták és-megoldásokat az Azure Functions és a Logic Apps az Azure Media Services használatával hozhat létre egyéni tartalomkészítés munkafolyamatokat, tekintse meg a [Media Services .NET funkciók integrációs minta a Githubon](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Lásd még [Azure Webhookok használata a .NET-keretrendszerrel történő Media Services feladatértesítések figyelése](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

