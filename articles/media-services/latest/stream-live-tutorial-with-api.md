---
title: Az Azure Media Services v3 élő Stream .NET használatával |} A Microsoft Docs
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
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 3500463cc57fb236de2a591181a5dc7054780404
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240694"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-net"></a>Oktatóanyag: A Media Services v3 élő Stream .NET használatával

Az Azure Media Services [élő események](https://docs.microsoft.com/rest/api/media/liveevents) felelősek az élő adatfolyam-tartalmak feldolgozása. Egy élő eseményt bemeneti végpontot biztosít (betöltési URL-címe), hogy, majd adja meg az élő kódoló. Az élő esemény az élő kódoló a bemeneti élő Streamek fogad, és lehetővé teszi egy vagy több keresztül [adatfolyam-továbbítási végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints). Élő események előzetes verziójú végpont (előzetes verzió URL-cím), amellyel és ellenőrzéséhez a stream előnézetének a feldolgozás folytatása és a továbbítás előtti is biztosítanak. Ez az oktatóprogram bemutatja, hogyan hozhat létre **átmenő** típusú élő eseményt a .NET Core használatával. 

> [!NOTE]
> Mielőtt folytatná, mindenképp tekintse át [a Media Services 3-as verziójával megvalósított élő streamelést](live-streaming-overview.md) bemutató cikket. 

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * A témakörben ismertetett mintaalkalmazás letöltése
> * Az élő streamelést végrehajtó kód vizsgálata
> * Az esemény megtekintése az [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) használatával az http://ampdemo.azureedge.net címen
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének a következők a feltételei.

- Telepítse a Visual Studio Code-ot vagy a Visual Studióban.
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).<br/>Ellenőrizze, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve.
- Kövesse a [hozzáférés az Azure Media Services API az Azure CLI-vel](access-api-cli-how-to.md) és menteni a hitelesítő adatokat. Az API eléréséhez használandó kell.
- Egy kamera vagy egyéb eszköz (például laptop), amely az eseményt közvetíti.
- Egy helyszíni élő kódoló, amely a kamera jeleit streammé alakítja, amelyek aztán továbbítódnak a Media Services élő streamszolgáltatásra. A streamnek **RTMP** vagy **Smooth Streaming** formátumúnak kell lennie.

## <a name="download-and-configure-the-sample"></a>Töltse le és a minta konfigurálásához

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Az elő streamelési minta az [Élő](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) mappában található.

Nyissa meg [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) a letöltött projektet. Cserélje le az értékeket a portáltól kapott hitelesítő adatokkal [API-k elérése](access-api-cli-how-to.md).

> [!IMPORTANT]
> A minta egyedi utótagot használ mindegyik erőforráshoz. Ha a hibakeresés, vagy az alkalmazás leállítása nélkül futó keresztül, akkor lesz megtörténhet több élő esemény-fiókjában található. <br/>Ellenőrizze, hogy állítsa le a futó élő eseményeket. Ezek ugyanis **díjkötelesek**!

## <a name="examine-the-code-that-performs-live-streaming"></a>Az élő streamelést végrehajtó kód vizsgálata

Ez a szakasz a *MediaV3LiveApp* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta egy egyedi utótagot oszt ki az egyes erőforrások számára, így elkerülhető a névütközés, ha többször is futtatná a mintát az adatok törlése nélkül.

> [!IMPORTANT]
> A minta egyedi utótagot használ mindegyik erőforráshoz. Ha a hibakeresés, vagy az alkalmazás leállítása nélkül futó keresztül, akkor lesz megtörténhet több élő esemény-fiókjában található. <br/>
> Ellenőrizze, hogy állítsa le a futó élő eseményeket. Ezek ugyanis **díjkötelesek**!
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Élő esemény létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy **átmenő** írja be az élő esemény (LiveEventEncodingType beállítása None). Élő események elérhető típusaival kapcsolatos további információkért lásd: [élő eseménytípusok](live-events-outputs-concept.md#live-event-types). 
 
Néhány dolgot, amelyeket érdemes az élő esemény létrehozásakor adja meg a következők:

* A Media Services hely 
* Az élő esemény az adatfolyam-továbbítási protokoll (jelenleg az RTMP és Smooth Streaming protokollokat támogatottak).<br/>Az élő esemény vagy kapcsolódó Live Kimenetétől futása közben, a protokollbeállítás nem módosítható. Ha eltérő protokollok használatára van szüksége, létre kell hoznia az egyes streamprotokollokhoz külön élő esemény.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja, hogy az IP-címek, amelyek jogosultak videókat az élő esemény betölteni. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nem ad meg IP-címeket, és nem határoz meg szabálydefiníciót, a rendszer egyetlen IP-címet sem engedélyez. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeket kell lennie a következő formátumok egyikében: IpV4-cím 4 számjegyből, CIDR-címtartományt.
* Az esemény létrehozásakor megadhatja, hogy az automatikusan induljon el. <br/>Ha autostart értéke igaz, az élő esemény létrehozása után fog elindulni. Ez azt jelenti, a számlázási elindul, amint az élő esemény startsrunning. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson. További információkért lásd: [élő esemény állapotok és számlázási](live-event-states-billing.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

Az élő esemény létrehozása után megjelenik a betöltési URL-címek, amelyek ezeket a élőadás-kódoló kell megadnia. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Az előnézeti URL-cím lekérése

A previewEndpoint segítségével tekintse meg az előnézetet, és győződjön meg róla, hogy a kódolóról érkező bemenet valóban beérkezik.

> [!IMPORTANT]
> Mielőtt folytatná, győződjön meg róla, hogy a videó valóban továbbítva van az előnézeti URL-címre!

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Élő események és élő kimenetek létrehozása és kezelése

Miután a stream az élő esemény beérkeznek, megkezdheti a streamelési eseményt hoz létre egy eszköz, a kimeneti Live és a Streamelési lokátor. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára. 

#### <a name="create-an-asset"></a>Adategység létrehozása

A használata élő kimeneti adategység létrehozása.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Hozzon létre egy élő kimenet

Létrehozás kimenetek start Live, és állítsa le a törlésekor. Az élő kimeneti törlésekor nem törlünk az alapul szolgáló eszköz és az eszköz tartalmát.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>A Streamelési Lokátorok létrehozásához

> [!NOTE]
> A Media Services-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, és kihasználhatja [dinamikus csomagolási](dynamic-packaging-overview.md) és dinamikus titkosítást, a tartalomstreameléshez használt streamvégpont, ahonnan tartalomstreameléshez kell lennie a a **futó** állapota. 

Az élő kimeneti objektum egy Streamelési lokátor segítségével történő közzétételekor az élő esemény (akár a DVR időszak hossza) továbbra is csak megtekinthető a Streamelési lokátor lejárati vagy törlés, amelyiket hamarabb.

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
* Leállítja az élő eseményt. Az élő esemény leállítását követően nem számítunk fel díjakat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. Ha az élő esemény leállított állapotban van, nem számítunk fel díjakat.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

A következő kód bemutatja, hogyan megtisztítani a fiók az összes élő esemény:

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>Esemény megtekintése

Esemény megtekintése, másolása a streamelési URL-cím, a kód futtatásakor ismertetett létrehozása a Streamelési lokátor, és egy tetszőleges player használja. A stream kipróbálásához használhatja az [Azure Media Playert](http://amp.azure.net/libs/amp/latest/docs/index.html) a http://ampdemo.azureedge.net címen. 

Élő esemény automatikusan átalakítja az eseményeket igény szerinti tartalommá. Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Az élő esemény futó elhagyása tekintetében a számlázási költségeket. Vegye figyelembe, ha a projekt/program biztosan összeomlik, vagy valamilyen okból lezárták, maradna számlázási állapotban fut az élő esemény.

## <a name="next-steps"></a>További lépések

[Videofájlok streamelése](stream-files-tutorial-with-api.md)
 
