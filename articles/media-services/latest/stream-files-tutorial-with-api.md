---
title: Feltöltés, kódolás és adatfolyam a Media Services 3-as számával
titleSuffix: Azure Media Services
description: Oktatóanyag, amely bemutatja, hogyan tölthet fel egy fájlt, kódoljon videókat és streamelhet tartalmakat az Azure Media Services v3-as számával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/22/2019
ms.author: juliako
ms.openlocfilehash: 4e40d26e392219fb751328bc54855d87e80bae19
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345996"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-media-services-v3"></a>Oktatóanyag: Videók feltöltése, kódolása és streamelése a Media Services v3-as számával

> [!NOTE]
> Annak ellenére, hogy ez az oktatóanyag [.NET SDK-példákat](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) használ, az általános lépések megegyeznek a [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [cli](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)vagy más támogatott [SDK-k](media-services-apis-overview.md#sdks)esetében.

Az Azure Media Services lehetővé teszi a médiafájlok kódolását olyan formátumokba, amelyek a legkülönbözőbb böngészőkben és eszközökön játszanak. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A streamelés előtt érdemes kódolni a jó minőségű digitális médiafájlokat. A kódolással kapcsolatos segítségért olvassa el [a Kódolás iszatát.](encoding-concept.md) Ez az oktatóanyag feltölt egy helyi videofájlt, és kódolja a feltöltött fájlt. A HTTPS URL-címen keresztül elérhetővé teáltal elérhetővé tesztartalmakat is kódolhatja. További információ: [Feladatbemenet létrehozása HTTP(s) URL-címből](job-input-from-http-how-to.md).

![Videó lejátszása az Azure Media Playerrel](./media/stream-files-tutorial-with-api/final-video.png)

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a témakörben ismertetett mintaalkalmazást.
> * Vizsgálja meg a feltöltést, kódolást és adatfolyamot tartalmazó kódot.
> * Futtassa az alkalmazást.
> * Tesztelje a streamelési URL-címet.
> * Az erőforrások megtisztítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, beszerezheti a [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)alkalmazást.
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).<br/>Ügyeljen arra, hogy emlékezzen az erőforráscsoport nevéhez és a Media Services-fiók nevéhez használt értékekre.
- Kövesse az [Access Azure Media Services API-t az Azure CLI-vel,](access-api-cli-how-to.md) és mentse a hitelesítő adatokat. Az API eléréséhez használnia kell őket.

## <a name="download-and-set-up-the-sample"></a>A minta letöltése és beállítása

Klónozzon egy GitHub-tárházat, amely a streamelési .NET mintát a következő paranccsal használja a számítógépre:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A minta az [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles) mappában található.

Nyissa meg [az appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/appsettings.json) fájlt a letöltött projektben. Cserélje le az értékeket az [API-k eléréséből](access-api-cli-how-to.md)származó hitelesítő adatokkal.

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>A feltöltést, kódolást és streamelést végrehajtó kód vizsgálata

Ez a szakasz az *UploadEncodeAndStreamFiles* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta a következő műveleteket hajtja végre:

1. Új **átalakítást hoz** létre (először ellenőrzi, hogy a megadott átalakítás létezik-e).
2. Létrehoz egy kimeneti **eszközt,** amely a kódolási **feladat**kimeneteként használatos.
3. Hozzon létre egy bemeneti **eszközt,** és töltse fel a megadott helyi videofájlt. Az objektum lesz a feladat bemenete.
4. Elküldi a kódolási feladatot a létrehozott bemeneti és kimeneti használatával.
5. Ellenőrzi a feladat állapotát.
6. Létrehoz egy **streamelési lokátort.**
7. Streamelési URL-címeket épít fel.

### <a name="start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia az ügyfél nek az Azure-hoz az Azure AD használatával való csatlakozásához szükséges hitelesítő adatokat. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](https://docs.microsoft.com/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Ez **az eszköz** a kódolási feladat bemeneti bemeneteként használatos. A Media Services 3-as verzióban a **feladatbemenet** lehet egy **eszköz** vagy tartalom, amelyet HTTPS-URL-címeken keresztül tesz elérhetővé a Media Services-fiók számára. A HTTPS-URL-címről történő kódolásról [ebben a](job-input-from-http-how-to.md) cikkben olvashat.

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket hajtja végre:

* **Eszköz**létrehozása .
* Írható [SAS-URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) kap az eszköz [tárolójának tárolójában.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)

    Ha az eszköz [ListContainerSas](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) függvény segítségével sas URL-eket, vegye figyelembe, hogy a függvény több SAS URL-eket ad vissza, mivel minden tárfiókhoz két tárfiók kulcs ad vissza. A tárfiók két kulccsal rendelkezik, mert lehetővé teszi a tárfiók kulcsainak zökkenőmentes elforgatását (például módosítsa az egyiket a másik használata közben, majd kezdje el az új kulcsot, és forgassa el a másik kulcsot). Az első SAS-URL a tárolási kulcsot1 és a második tárolási kulcsot2 jelöli.
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Kimeneti objektum létrehozása feladat eredményeinek tárolásához

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit. A projekt határozza meg a **DownloadResults** függvényt, amely letölti az eredményt ebből a kimeneti objektumból az „output” mappába, hogy megtekinthesse.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Átalakítás és a feltöltött fájlt kódoló feladat létrehozása

A Media Services tartalmának kódolásakor vagy feldolgozásakor gyakori minta a kódolási beállítások receptként való beállítása. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha minden új videóhoz új állásokat küldesz be, akkor ezt a receptet a könyvtárod összes videójára alkalmazod. A Media Services receptje **it transform-nak nevezzük.** További információt az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre.

#### <a name="transform"></a>Átalakítás

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. Az előzetes beállítással kapcsolatos információkért tekintse meg a [sávszélességi skálák automatikus létrehozását](autogen-bitrate-ladder.md) ismertető részt.

Használhatja a beépített EncoderNamedPreset beállítást vagy az egyéni előzetes beállításokat. További információt [a kódoló előzetes beállításainak testreszabását](customize-encoder-presets-how-to.md) ismertető cikkben talál.

[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Feladat

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben a példában a bemeneti videó a helyi számítógépről lett feltöltve. Ha meg szeretné tudni, hogyan kódolhat HTTPS-URL-címről, olvassa el [ezt a](job-input-from-http-how-to.md) cikket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. A lekérdezés nem ajánlott ajánlott eljárás éles alkalmazások potenciális késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt.

Az Event Grid egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén.  További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítása folyamatban van, akkor a **Megszakítás** és a **Canceled** is megkapja, amikor elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="get-a-streaming-locator"></a>Streamelési lokátor beszereznie

A kódolás befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Két lépésben elérhetővé teheti: először hozzon létre egy [streamelési lokátort,](https://docs.microsoft.com/rest/api/media/streaminglocators)másodszor pedig hozza létre az ügyfelek által használható streamelési URL-címeket.

A **streamelési lokátor** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **streamelési lokátor** az API-hívások kezdeményezése után azonnal érvényes, és a törlésig tart, kivéve, ha konfigurálja a választható kezdési és befejezési időpontokat.

[A StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)létrehozásakor meg kell adnia a kívánt **StreamingPolicyName nevet.** Ebben a példában a rendszer a tiszta (vagy nem titkosított tartalmat) streamelést fogja használni, így az előre definiált titkos adatfolyam-továbbítási házirend (**PredefinedStreamingPolicy.ClearStreamingOnly**) lesz használva.

> [!IMPORTANT]
> Egyéni [streamelési szabályzat](https://docs.microsoft.com/rest/api/media/streamingpolicies)használataesetén a Media Service-fiókhoz korlátozott számú ilyen házirendet kell megterveznie, és újra fel kell használnia őket a StreamingLocatorok számára, ha ugyanazokra a titkosítási beállításokra és protokollokra van szükség. A Media Service-fiók rendelkezik kvótával a streamelési szabályzat-bejegyzések számára. Nem kell létrehoznia egy új streamelési szabályzatot minden streamelési lokátorhoz.

A következő kód feltételezi, hogy a függvény tanoncnév nevű.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Míg a témakörben szereplő minta a streamelést tárgyalja, ugyanazzal a hívással létrehozhat egy streamelési lokátort a videó progresszív letöltéssel történő kézbesítéséhez.

### <a name="get-streaming-urls"></a>Streamelési URL-címek lekérdezése

Most, hogy létrejött a [streamelési lokátor,](https://docs.microsoft.com/rest/api/media/streaminglocators) lekaphatja a streamelési URL-eket, ahogy az a **GetStreamingURLs-ban**látható. URL-cím létrehozásához össze kell fűznie a [streamelési végpont](https://docs.microsoft.com/rest/api/media/streamingendpoints) gazdagép nevét és a **streamelési lokátor** elérési útját. Ebben a példában az *alapértelmezett* **streamelési végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, ez az *alapértelmezett* **streamelési végpont** leállított állapotban lesz, ezért meg kell hívnia a Start **programot.**

> [!NOTE]
> Ebben a módszerben a kimeneti eszköz **streamelési lokátorának** létrehozásakor használt lokátornév szükséges.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában mindent meg kell tisztítania, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra fel fogja használni az átalakításokat, és meg kell tartania a StreamingLocators stb.). Ha azt szeretné, hogy a fiók a kísérletezés után tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a Feladatok:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

1. Nyomja le a Ctrl+F5 billentyűkombinációt az *EncodeAndStreamFiles* alkalmazás futtatásához.
2. Másolja be az egyik streamelési URL-címet a konzolról.

Ebben a példában olyan URL-címek szerepelnek, amelyek lehetővé teszik, hogy a videót többféle protokollal le lehessen játszani:

![Példa kimenetre a Media Services streamelési videójának URL-címeivel](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>A streamelési URL-cím tesztelése

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez.

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)webböngészőt, és keresse meg a ját.
2. Az **URL-cím:** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét.
3. Válassza **a Player frissítése**lehetőséget.

Az Azure Media Player tesztelésre használható, de éles környezetben nem használható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Több szál használata

Az Azure Media Services v3 SDK-k nem szálbiztos. Többszálas alkalmazás fejlesztésekor hozzon létre és használjon egy új AzureMediaServicesClient-objektumot szálonként.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan tölthet fel, kódolhat és streamelhet videókat, tekintse meg a következő cikket: 

> [!div class="nextstepaction"]
> [Videók elemzése](analyze-videos-tutorial-with-api.md)
