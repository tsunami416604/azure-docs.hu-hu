---
title: Az Azure Media Services v3 élő Stream |} A Microsoft Docs
description: Ez az útmutató lépésről lépésre ismerteti, hogyan streamelhet élő tartalmakat a .NET Core-keretrendszert használó Media Services 3-as verziójával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/19/2018
ms.author: juliako
ms.openlocfilehash: 266227b87381fde74efd0fa05c6ea10a0eace985
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724963"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-apis"></a>Oktatóanyag: A Media Services v3 élő Stream API-k használatával

Az Azure Media Services [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) felelősek az élő adatfolyam-tartalmak feldolgozása. A LiveEvent egy bemeneti végpontot (betöltési URL-címet) biztosít, amelyet aztán megadhat egy élő kódolónak. A LiveEvent élő bemeneti streameket fogad az élő kódolótól, és elérhetővé teszi azt a streameléshez egy vagy több [streamvégponton](https://docs.microsoft.com/rest/api/media/streamingendpoints) keresztül. Emellett a csatorna egy előnézeti végpontot (előnézeti URL-címet) is biztosít, amelynek használatával megtekintheti a stream előnézetét, és ellenőrizheti azt, mielőtt feldolgozná és továbbítaná. Ez az oktatóprogram bemutatja, hogyan hozhat létre **átmenő** típusú élő eseményt a .NET Core használatával. 

> [!NOTE]
> Mielőtt folytatná, mindenképp tekintse át [a Media Services 3-as verziójával megvalósított élő streamelést](live-streaming-overview.md) bemutató cikket. 

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Hozzáférés a Media Services API-hoz
> * A mintaalkalmazás konfigurálása
> * Az élő streamelést végrehajtó kód vizsgálata
> * Az esemény megtekintése az [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) használatával az http://ampdemo.azureedge.net címen
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

- Telepítse a Visual Studio Code-ot vagy a Visual Studióban.
- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

    Ügyeljen arra, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve

- Egy kamera vagy egyéb eszköz (például laptop), amely az eseményt közvetíti.
- Egy helyszíni élő kódoló, amely a kamera jeleit streammé alakítja, amelyek aztán továbbítódnak a Media Services élő streamszolgáltatásra. A streamnek **RTMP** vagy **Smooth Streaming** formátumúnak kell lennie.

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Az elő streamelési minta az [Élő](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) mappában található.

> [!IMPORTANT]
> A minta egyedi utótagot használ mindegyik erőforráshoz. Ha megszakítja a hibakeresést, vagy leállítja az alkalmazást anélkül, hogy az végigfutna, végül több LiveEvent eseménye is lesz a fiókban. <br/>
> Ezeket a futó LiveEvent eseményeket mindenképp állítsa le. Ezek ugyanis **díjkötelesek**!

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>Az élő streamelést végrehajtó kód vizsgálata

Ez a szakasz a *MediaV3LiveApp* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta egy egyedi utótagot oszt ki az egyes erőforrások számára, így elkerülhető a névütközés, ha többször is futtatná a mintát az adatok törlése nélkül.

> [!IMPORTANT]
> A minta egyedi utótagot használ mindegyik erőforráshoz. Ha megszakítja a hibakeresést, vagy leállítja az alkalmazást anélkül, hogy az végigfutna, végül több LiveEvent eseménye is lesz a fiókban. <br/>
> Ezeket a futó LiveEvent eseményeket mindenképp állítsa le. Ezek ugyanis **díjkötelesek**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Élő esemény létrehozása

Ez a szakasz azt mutatja be, hogyan lehet **átmenő** típusú LiveEvent eseményt létrehozni (ahol a LiveEventEncodingType értéke Nincs). Ha szeretne létrehozni, amely valós idejű kódolás LiveEventEncodingType Standard beállítása engedélyezve van a videókhoz. 

Néhány egyéb beállítás, amelyet érdemes lehet megadnia az élő események létrehozásakor:

* A Media Services hely 
* Az élő esemény streamprotokollja (jelenleg az RTMP és a Smooth Streaming protokoll támogatott)
       
    Ha a LiveEvent esemény vagy a hozzá tartozó LiveOutput kimenetek futnak, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szükség, hozzon létre külön-külön LiveEvent eseményeket az egyes streamprotokollokhoz.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek jogosultak videókat betölteni erre a LiveEvent eseményre. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.
    
    Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.

Az esemény létrehozásakor megadhatja, hogy az automatikusan induljon el. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

A csatorna létrehozása után beolvashatja a betöltési URL-címeket. Ezeket kell megadnia az élő kódolónak. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Az előnézeti URL-cím lekérése

A previewEndpoint segítségével tekintse meg az előnézetet, és győződjön meg róla, hogy a kódolóról érkező bemenet valóban beérkezik.

> [!IMPORTANT]
> Mielőtt folytatná, győződjön meg róla, hogy a videó valóban továbbítva van az előnézeti URL-címre!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>LiveEvent események és LiveOutput kimenetek létrehozása és kezelése

Ha elvégezte a stream és a LiveEvent összekapcsolását, elindíthatja a streamelési eseményt. Ehhez létre kell hoznia egy adategységet, egy LiveOutputot és egy StreamingLocatort. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára. 

#### <a name="create-an-asset"></a>Adategység létrehozása

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

Hozzon létre egy adategységet, amelyet a LiveOutput használhat.

#### <a name="create-a-liveoutput"></a>LiveOutput létrehozása

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>StreamingLocator létrehozása

> [!NOTE]
> A Media Services-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

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

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Esemény megtekintése

Ha meg szeretne tekinteni egy eseményt, másolja ki a [StreamingLocator létrehozása](#create-a-streaminglocator) szakaszban ismertetett kód futtatásakor kapott streamelési URL-címet, és használjon egy tetszés szerinti lejátszót. A stream kipróbálásához használhatja az [Azure Media Playert](http://amp.azure.net/libs/amp/latest/docs/index.html) a http://ampdemo.azureedge.net címen. 

A leállítása után az élő esemény automatikusan átalakítja az eseményeket igény szerinti tartalommá. Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> A le nem állított LiveEventek díjkötelesek. Felhívjuk a figyelmét, hogy ha a projekt/program összeomlik, vagy bármilyen okból bezáródik, a LiveEvent esetleg tovább futhat, ami költségekkel jár.

## <a name="next-steps"></a>További lépések

[Videofájlok streamelése](stream-files-tutorial-with-api.md)

