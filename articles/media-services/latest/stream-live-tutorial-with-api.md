---
title: Élő streamelés az Azure Media Services v3 és a .NET Core használatával | Microsoft Docs
description: Ez az útmutató lépésről lépésre ismerteti, hogyan streamelhet élő tartalmakat a .NET Core-keretrendszert használó Media Services 3-as verziójával.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: f02820f8382268dfedeee8d07de2438660e5e33e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236882"
---
# <a name="stream-live-with-azure-media-services-v3-using-net-core"></a>Élő streamelés az Azure Media Services v3 és a .NET Core használatával

A Media Services szolgáltatásban a [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) események feladata a streamelt élő tartalmak feldolgozása. A LiveEvent egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet aztán megadhat egy élő kódolónak. A LiveEvent élő bemeneti streameket fogad az élő kódolótól, és elérhetővé teszi azt a streameléshez egy vagy több [streamvégponton](https://docs.microsoft.com/rest/api/media/streamingendpoints) keresztül. Emellett a csatorna egy előnézeti végpontot (előnézeti URL-címet) is biztosít, amelynek használatával megtekintheti a stream előnézetét, és ellenőrizheti azt, mielőtt feldolgozná és továbbítaná. Ez az oktatóprogram bemutatja, hogyan hozhat létre **átmenő** típusú élő eseményt a .NET Core használatával. 

> [!NOTE]
> Mielőtt folytatná, mindenképp tekintse át [a Media Services 3-as verziójával megvalósított élő streamelést](live-streaming-overview.md) bemutató cikket. 

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Media Services-fiók létrehozása
> * Hozzáférés a Media Services API-hoz
> * A mintaalkalmazás konfigurálása
> * Az élő streamelést végrehajtó kód vizsgálata
> * Az esemény megtekintése az [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) használatával az http://ampdemo.azureedge.net címen
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

* A Visual Studio Code vagy a Visual Studio telepítése
* Egy kamera vagy egyéb eszköz (például laptop), amely az eseményt közvetíti.
* Egy helyszíni élő kódoló, amely a kamera jeleit streammé alakítja, amelyek aztán továbbítódnak a Media Services élő streamszolgáltatásra. A streamnek **RTMP** vagy **Smooth Streaming** formátumúnak kell lennie.

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Az elő streamelési minta az [Élő](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) mappában található.

> [!IMPORTANT]
> A minta egyedi utótagot használ mindegyik erőforráshoz. Ha megszakítja a hibakeresést, vagy leállítja az alkalmazást anélkül, hogy az végigfutna, végül több LiveEvent eseménye is lesz a fiókban. <br/>
> Ezeket a futó LiveEvent eseményeket mindenképp állítsa le. Ezek ugyanis **díjkötelesek**!

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Az élő streamelést végrehajtó kód vizsgálata

Ez a szakasz a *MediaV3LiveApp* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta egy egyedi utótagot oszt ki az egyes erőforrások számára, így elkerülhető a névütközés, ha többször is futtatná a mintát az adatok törlése nélkül.

> [!IMPORTANT]
> A minta egyedi utótagot használ mindegyik erőforráshoz. Ha megszakítja a hibakeresést, vagy leállítja az alkalmazást anélkül, hogy az végigfutna, végül több LiveEvent eseménye is lesz a fiókban. <br/>
> Ezeket a futó LiveEvent eseményeket mindenképp állítsa le. Ezek ugyanis **díjkötelesek**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.  

```csharp
private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
{
    var credentials = await GetCredentialsAsync(config);

    return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
    {
        SubscriptionId = config.SubscriptionId,
    };
}
```

### <a name="create-a-live-event"></a>Élő esemény létrehozása

Ez a szakasz azt mutatja be, hogyan lehet **átmenő** típusú LiveEvent eseményt létrehozni (ahol a LiveEventEncodingType értéke Nincs). Ha olyan LiveEvent eseményt szeretne létrehozni, amely használható élő kódolásra, állítsa a LiveEventEncodingType paramétert Alapszintű értékre. 

Néhány egyéb beállítás, amelyet érdemes lehet megadnia az élő események létrehozásakor:

* A Media Services hely 
* Az élő esemény streamprotokollja (jelenleg az RTMP és a Smooth Streaming protokoll támogatott)
       
    Ha a LiveEvent esemény vagy a hozzá tartozó LiveOutput kimenetek futnak, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szükség, hozzon létre külön-külön LiveEvent eseményeket az egyes streamprotokollokhoz.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek jogosultak videókat betölteni erre a LiveEvent eseményre. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.
    
    Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

Az esemény létrehozásakor megadhatja, hogy az automatikusan induljon el. 

```csharp
Console.WriteLine($"Creating a live event named {liveEventName}");
Console.WriteLine();

LiveEventPreview liveEventPreview = new LiveEventPreview
{
    AccessControl = new LiveEventPreviewAccessControl(
        ip: new IPAccessControl(
            allow: new IPRange[]
            {
                new IPRange (
                    name: "AllowAll",
                    address: "0.0.0.0",
                    subnetPrefixLength: 0
                )
            }
        )
    )
};

// This can sometimes take awhile. Be patient.
LiveEvent liveEvent = new LiveEvent(
    location: mediaService.Location, 
    description:"Sample LiveEvent for testing",
    vanityUrl:false,
    encoding: new LiveEventEncoding(
                // Set this to Basic to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                encodingType:LiveEventEncodingType.None, 
                presetName:null
            ),
    input: new LiveEventInput(LiveEventInputProtocol.RTMP), 
    preview: liveEventPreview,
    streamOptions: new List<StreamOptionsFlag?>()
    {
        // Set this to Default or Low Latency.
        // Low latency reduces the amount of buffering Media Services does.
        // Low latency can also reduce the stability of the live stream. 
        StreamOptionsFlag.Default
    }
);

Console.WriteLine($"Creating the LiveEvent, be patient this can take time...");
liveEvent = client.LiveEvents.Create(config.ResourceGroup, config.AccountName, liveEventName, liveEvent, autoStart:true);
```

### <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.


```csharp
// Get the input endpoint to configure the on premise encoder with
string ingestUrl = liveEvent.Input.Endpoints.First().Url;
Console.WriteLine($"The ingest url to configure the on premise encoder with is:");
Console.WriteLine($"\t{ingestUrl}");
Console.WriteLine();
```

### <a name="get-the-preview-url"></a>Az előnézeti URL-cím lekérése

A previewEndpoint segítségével tekintse meg az előnézetet, és győződjön meg róla, hogy a kódolóról érkező bemenet valóban beérkezik.

> [!IMPORTANT]
> Mielőtt folytatná, győződjön meg róla, hogy a videó valóban továbbítva van az előnézeti URL-címre!

```sharp
string previewEndpoint = liveEvent.Preview.Endpoints.First().Url;
Console.WriteLine($"The preview url is:");
Console.WriteLine($"\t{previewEndpoint}");
Console.WriteLine();

Console.WriteLine($"Open the live preview in your browser and use the Azure Media Player to monitor the preview playback:");
Console.WriteLine($"\thttps://ampdemo.azureedge.net/?url={previewEndpoint}");
Console.WriteLine();
```

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>LiveEvent események és LiveOutput kimenetek létrehozása és kezelése

Ha elvégezte a stream és a LiveEvent összekapcsolását, elindíthatja a streamelési eseményt. Ehhez létre kell hoznia egy adategységet, egy LiveOutputot és egy StreamingLocatort. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára. 

#### <a name="create-an-asset"></a>Adategység létrehozása

Hozzon létre egy adategységet, amelyet a LiveOutput használhat.

```csharp
string assetName = "archiveAsset" + uniqueness;
Console.WriteLine($"Creating an asset named {assetName}");
Console.WriteLine();
Asset asset = client.Assets.CreateOrUpdate(config.ResourceGroup, config.AccountName, assetName, new Asset());
```

#### <a name="create-a-liveoutput"></a>LiveOutput létrehozása

```csharp
string manifestName = "output";
string liveOutputName = "liveOutput" + uniqueness;
Console.WriteLine($"Creating a live output named {liveOutputName}");
Console.WriteLine();

LiveOutput liveOutput = new LiveOutput(assetName: asset.Name, manifestName: manifestName, archiveWindowLength: TimeSpan.FromMinutes(10));
liveOutput = client.LiveOutputs.Create(config.ResourceGroup, config.AccountName, liveEventName, liveOutputName, liveOutput);
```

#### <a name="create-a-streaminglocator"></a>StreamingLocator létrehozása

> [!NOTE]
> A Media Services-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 


```csharp
StreamingLocator locator = new StreamingLocator(assetName: assetName, streamingPolicyName: PredefinedStreamingPolicy.ClearStreamingOnly);
locator = client.StreamingLocators.Create(config.ResourceGroup, config.AccountName, streamingLocatorName, locator);

// Get the default Streaming Endpoint on the account
StreamingEndpoint streamingEndpoint = client.StreamingEndpoints.Get(config.ResourceGroup, config.AccountName, "default");

// If it's not running, Start it. 
if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
{
    Console.WriteLine("Streaming Endpoint was Stopped, restarting now..");
    client.StreamingEndpoints.Start(config.ResourceGroup, config.AccountName, "default");
}

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>A Media Services-fiók erőforrásainak eltávolítása

Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

* Állítsa le a stream továbbítását a kódolóban.
* Állítsa le a LiveEvent eseményt. A LiveEvent a leállítását követően nem von maga után díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* A streamvégpontot is leállíthatja, kivéve, ha az élő esemény archívumát szeretné igényalapú streamelésre elérhetővé tenni. A leállított állapotú LiveEvent események nem vonnak maguk után díjakat.

```csharp
private static void CleanupLiveEventAndOutput(IAzureMediaServicesClient client, string resourceGroup, string accountName, string liveEventName, string liveOutputName)
{
    // Delete the LiveOutput
    client.LiveOutputs.Delete(resourceGroup, accountName, liveEventName, liveOutputName);

    // Stop and delete the LiveEvent
    client.LiveEvents.Stop(resourceGroup, accountName, liveEventName);
    client.LiveEvents.Delete(resourceGroup, accountName, liveEventName);
}

private static void CleanupLocatorAssetAndStreamingEndpoint(IAzureMediaServicesClient client, string resourceGroup, string accountName, string streamingLocatorName, string assetName)
{
    // Delete the Streaming Locator
    client.StreamingLocators.Delete(resourceGroup, accountName, streamingLocatorName);

    // Delete the Archive Asset
    client.Assets.Delete(resourceGroup, accountName, assetName);
}

private static void CleanupAccount(IAzureMediaServicesClient client, string resourceGroup, string accountName)
{
    try{
        Console.WriteLine("Cleaning up the resources used, stopping the LiveEvent. This can take a few minutes to complete.");
        Console.WriteLine();

        var events = client.LiveEvents.List(resourceGroup, accountName);
        
        foreach (LiveEvent l in events)
        {
            if (l.Name == liveEventName){
                var outputs = client.LiveOutputs.List(resourceGroup, accountName, l.Name);

                foreach (LiveOutput o in outputs)
                {
                    client.LiveOutputs.Delete(resourceGroup, accountName, l.Name, o.Name);
                    Console.WriteLine($"LiveOutput: {o.Name} deleted from LiveEvent {l.Name}. The archived Asset and Streaming URLs are still retained for on-demand viewing.");
                }

                if (l.ResourceState == LiveEventResourceState.Running){
                    client.LiveEvents.Stop(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Stopped.");
                    client.LiveEvents.Delete(resourceGroup, accountName, l.Name);
                    Console.WriteLine($"LiveEvent: {l.Name} Deleted.");
                    Console.WriteLine();
                }
            }
        }
    } 
    catch(ApiErrorException e)
    {
        Console.WriteLine("Hit ApiErrorException");
        Console.WriteLine($"\tCode: {e.Body.Error.Code}");
        Console.WriteLine($"\tCode: {e.Body.Error.Message}");
        Console.WriteLine();

    }
}
```        

## <a name="watch-the-event"></a>Esemény megtekintése

Ha meg szeretne tekinteni egy eseményt, másolja ki a [StreamingLocator létrehozása](#create-a-streaminglocator) szakaszban ismertetett kód futtatásakor kapott streamelési URL-címet, és használjon egy tetszés szerinti lejátszót. A stream kipróbálásához használhatja az [Azure Media Playert](http://amp.azure.net/libs/amp/latest/docs/index.html) a http://ampdemo.azureedge.net címen. 

A leállítása után az élő esemény automatikusan átalakítja az eseményeket igény szerinti tartalommá. Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot. Ehhez használhatja a **CloudShell** eszközt.

Hajtsa végre az alábbi parancsot a **CloudShellben**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> A le nem állított LiveEventek díjkötelesek. Felhívjuk a figyelmét, hogy ha a projekt/program összeomlik, vagy bármilyen okból bezáródik, a LiveEvent esetleg tovább futhat, ami költségekkel jár.

## <a name="next-steps"></a>További lépések

[Videofájlok streamelése](stream-files-tutorial-with-api.md)

