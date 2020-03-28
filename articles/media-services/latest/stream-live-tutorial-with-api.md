---
title: Élő közvetítés a Media Services 3-as v3-as ával
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan streamelhet élőben az Azure Media Services v3-as használatával.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 0b6667965ddd1fce30bb2da2593e2a9274b595ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472016"
---
# <a name="tutorial-stream-live-with-media-services"></a>Oktatóanyag: Élő közvetítés a Media Services szolgáltatással

> [!NOTE]
> Annak ellenére, hogy az oktatóanyag [.NET SDK-példákat](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) használ, az általános lépések megegyeznek a [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [a CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)vagy más támogatott [SDK-k](media-services-apis-overview.md#sdks)esetében.

Az Azure Media Services szolgáltatásban [az élő események](https://docs.microsoft.com/rest/api/media/liveevents) felelősek az élő közvetítési tartalmak feldolgozásáért. Egy élő esemény egy bemeneti végpontot (betöltési URL-t) biztosít, amelyet ezután egy élő kódolónak biztosít. Az élő esemény élő bemeneti adatfolyamokat fogad az élő kódolótól, és elérhetővé teszi egy vagy több [streamelési végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints)keresztüli streamelésre. Az élő események egy előnézeti végpontot (előnézeti URL-t is) biztosítanak, amelyet a stream további feldolgozás és kézbesítés előtt történő előnézeti megtekintéséhez és érvényesítéséhez használhat. Ez az oktatóprogram bemutatja, hogyan hozhat létre **átmenő** típusú élő eseményt a .NET Core használatával.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a témakörben ismertetett mintaalkalmazást.
> * Vizsgálja meg az élő streamelést végző kódot.
> * Tekintse meg az eseményt [https://ampdemo.azureedge.net](https://ampdemo.azureedge.net)az Azure Media Player [rel](https://amp.azure.net/libs/amp/latest/docs/index.html) a itt: .
> * Az erőforrások megtisztítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzésének feltételei a következők:

- Telepítse a Visual Studio Code vagy a Visual Studio alkalmazást.
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).<br/>Ügyeljen arra, hogy emlékezzen az erőforráscsoport nevéhez és a Media Services-fiók nevéhez használt értékekre.
- Kövesse az [Access Azure Media Services API-t az Azure CLI-vel,](access-api-cli-how-to.md) és mentse a hitelesítő adatokat. Az API eléréséhez használnia kell őket.
- Egy esemény közvetítésére használt kamera vagy eszköz (például laptop).
- A helyszíni élő kódoló, amely a kamera jeleit a Media Services élő közvetítési szolgáltatásának küldött adatfolyamokká alakítja, lásd az [ajánlott helyszíni élő kódolókat.](recommended-on-premises-live-encoders.md) A streamnek **RTMP** vagy **Smooth Streaming** formátumúnak kell lennie.

> [!TIP]
> Mielőtt folytatná, mindenképp tekintse át [a Media Services 3-as verziójával megvalósított élő streamelést](live-streaming-overview.md) bemutató cikket. 

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

Az elő streamelési minta az [Élő](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) mappában található.

Nyissa meg [az appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/appsettings.json) fájlt a letöltött projektben. Cserélje le az értékeket az [API-k eléréséből](access-api-cli-how-to.md)származó hitelesítő adatokra.

> [!IMPORTANT]
> Ez a minta minden erőforráshoz egyedi utótagot használ. Ha megszakítja a hibakeresést, vagy anélkül szünteti meg az alkalmazást, hogy végigfuttatna, a fiókjában több élő esemény is megjelenik. <br/>Győződjön meg róla, hogy leállítja a futó élő eseményeket. Ellenkező esetben, akkor **kell fizetnie!**

## <a name="examine-the-code-that-performs-live-streaming"></a>Az élő streamelést végrehajtó kód vizsgálata

Ez a szakasz a *MediaV3LiveApp* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta létrehoz egy egyedi utótagot minden erőforráshoz, így nem lesz névütközés, ha a mintát többször futtatja tisztítás nélkül.

> [!IMPORTANT]
> Ez a minta minden erőforráshoz egyedi utótagot használ. Ha megszakítja a hibakeresést, vagy anélkül szünteti meg az alkalmazást, hogy végigfuttatna, a fiókjában több élő esemény is megjelenik. <br/>
> Győződjön meg róla, hogy leállítja a futó élő eseményeket. Ellenkező esetben, akkor **kell fizetnie!**

### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>Élő esemény létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre **egy áteresített** típusú élő esemény (LiveEventEncodingType beállítása Nincs). Az élő események elérhető típusairól az [Élő események típusai](live-events-outputs-concept.md#live-event-types)című témakörben talál további információt. 
 
Az élő esemény létrehozásakor érdemes lehet megadni a következőket:

* Media Services helye.
* Az élő esemény streamelési protokollja (jelenleg az RTMP és a Smooth Streaming protokollok támogatottak).<br/>A protokollbeállítás nem módosítható, amíg az élő esemény vagy a hozzá tartozó Élő kimenetek futnak. Ha különböző protokollokra van szüksége, hozzon létre külön élő eseményt minden egyes streamelési protokollhoz.  
* IP-korlátozások a betöltési és az előnézeti címen. Megadhatja azokat az IP-címeket, amelyek lehetővé teszik egy videó betöltését ebbe az élő eseménybe. Az engedélyezett IP-címek köre tartalmazhat egyetlen IP-címet (például „10.0.0.1”), vagy egy IP-tartományt, amelyet egy IP-cím és egy CIDR alhálózati maszk (például„10.0.0.1/22”) vagy egy IP-cím és egy pontozott decimális alhálózati maszk (például „10.0.0.1(255.255.252.0)”) segítségével lehet megadni.<br/>Ha nincs megadva IP-cím, és nincs szabálydefiníció, akkor nem lesz engedélyezett AZ IP-cím. Ha az összes IP-címnek szeretne engedélyt adni, hozzon létre egy szabályt, és állítsa be a következő értéket: 0.0.0.0/0.<br/>Az IP-címeknek a következő formátumok egyikében kell lenniük: IpV4-cím négy számmal vagy CIDR-címtartományban.
* Az esemény létrehozásakor megadhatja az automatikus indítást. <br/>Ha az automatikus indítás értéke igaz, az élő esemény a létrehozás után indul el. Ez azt jelenti, hogy a számlázás akkor kezdődik, amikor az élő esemény elindul. A további számlázás leállításához kifejezetten meg kell hívnia a Stop on the Live Event erőforrást. További információt az [Élő esemény állapotaés a számlázás](live-event-states-billing.md)című témakörben talál.
* Ahhoz, hogy egy betöltési URL-t prediktív, állítsa be a "hiúság" mód. Részletes információt az [Élő esemény betöltési URL-címei című témakörben talál.](live-events-outputs-concept.md#live-event-ingest-urls)

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>A betöltési URL-címek beolvasása

Az élő esemény létrehozása után beszerezheti az élő kódolónak biztosított URL-címeket. A kódoló ezekre az URL-címekre küldi a bemeneti élő streamet.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>Az előnézeti URL-cím lekérése

A previewEndpoint segítségével tekintse meg az előnézetet, és győződjön meg róla, hogy a kódolóról érkező bemenet valóban beérkezik.

> [!IMPORTANT]
> A folytatás előtt győződjön meg arról, hogy a videó az előnézeti URL-címhez áramlik.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>Élő események és élő kimenetek létrehozása és kezelése

Miután az adatfolyam az élő eseménybe áramlik, megkezdheti a streamelési eseményt egy eszköz, az élő kimenet és a streamelési lokátor létrehozásával. Ezzel archiválja a streamet, és a streamvégponton keresztül elérhetővé teszi a nézők számára.

#### <a name="create-an-asset"></a>Adategység létrehozása

Hozzon létre egy eszközt az élő kimenet hez.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>Élő kimenet létrehozása

Az élő kimenetek létrehozásakor kezdődnek, és törléskor leállnak. Az élő kimenet törlésekor nem törli a mögöttes eszközt és az eszköz tartalmát.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

> [!NOTE]
> A Media Services-fiók létrehozásakor a rendszer **leállított** állapotban hozzáadja a fiókhoz az **alapértelmezett** streamelési végpontot. A tartalom streamelésének megkezdéséhez és a [dinamikus csomagolás](dynamic-packaging-overview.md) és a dinamikus titkosítás előnyeinek kihasználásához a streamelési végpontnak, amelyből tartalmat szeretne streamelni, **futó** állapotban kell lennie.

Amikor egy streamelési lokátorral teszi közzé az élő kimeneti eszközt, az élő esemény (a DVR-ablak hosszáig) továbbra is látható marad a streamelési lokátor lejáratáig vagy törléséig, attól függően, hogy melyik következik be előbb.

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

Ha végzett a streamelési eseményekkel, és szeretné megtisztítani a korábban kiépített erőforrásokat, kövesse az alábbi eljárást:

* Állítsa le a stream továbbítását a kódolóban.
* Állítsa le az élő eseményt. Az élő esemény leállítása után nem számítunk fel díjat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
* A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. Ha az élő esemény leállított állapotban van, nem számítunk fel díjat.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>Esemény megtekintése

Az esemény megtekintéséhez másolja a streamelési URL-címet, amelyet akkor kapott, amikor futtatta a Streamelési lokátor létrehozása című könyvében leírt kódot. Használhatja az Ön által választott médialejátszót. [Az Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) elérhető https://ampdemo.azureedge.netaz adatfolyam teszteléséhez a .

Az Élő esemény leállított esetben automatikusan igény szerinti tartalommá alakítja az eseményeket. Még az esemény leállítása és törlése után is a felhasználók igény szerint videóként streamelhetik az archivált tartalmat, amíg nem törli az eszközt. Egy eszköz nem törölhető, ha egy esemény használja; az eseményt először törölni kell.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI parancsot:

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> Az élő esemény futásának elhagyása számlázási költségekkel jár. Ne feledje, hogy ha a projekt/program összeomlik, vagy bármilyen okból be van zárva, akkor az élő esemény számlázási állapotban maradhat.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

[Videofájlok streamelése](stream-files-tutorial-with-api.md)
 
