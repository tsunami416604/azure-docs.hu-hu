---
title: Feltöltés, kódolás és streamelés az Azure Media Serviceszel | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan tölthet fel egy fájlt, végezheti el egy videó kódolását, és streamelheti tartalmait az Azure Media Serviceszel.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2019
ms.author: juliako
ms.openlocfilehash: 051de9b68b6cf830592a7cf8bdad7808e044fbcc
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888712"
---
# <a name="tutorial-upload-encode-and-stream-videos-using-apis"></a>Oktatóanyag: Feltöltése, kódolása és streamelése videók, API-k használatával

Az Azure Media Services lehetővé teszi a médiafájlok kódolandó, hogy a böngészők és eszközök széles lejátszhatók. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A streamelés előtt érdemes kódolni a jó minőségű digitális médiafájlokat. Kódolással kapcsolatos útmutatásért tekintse meg [a kódolás fogalmát](encoding-concept.md) ismertető cikket. Ez az oktatóanyag feltölt egy helyi videofájlt, és kódolja a feltöltött fájlt. A HTTPS URL-cím segítségével elérhetővé tett tartalmakat is kódolhatja. További információ: [Feladatbemenet létrehozása HTTP(s) URL-címből](job-input-from-http-how-to.md).

![Videó lejátszása](./media/stream-files-tutorial-with-api/final-video.png)

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Hozzáférés a Media Services API-hoz
> * A mintaalkalmazás konfigurálása
> * A feltöltést, kódolást és streamelést végrehajtó kód vizsgálata
> * Az alkalmazás futtatása
> * A streamelési URL-cím tesztelése
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, szerezze be a [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)-et.
- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).

    Ügyeljen arra, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A minta az [UploadEncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/UploadEncodeAndStreamFiles) mappában található.

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-uploads-encodes-and-streams"></a>A feltöltést, kódolást és streamelést végrehajtó kód vizsgálata

Ez a szakasz az *UploadEncodeAndStreamFiles* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs) nevű fájljában megadott függvényeket vizsgálja meg.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy új **átalakítása** (először ellenőrzi, hogy létezik-e a megadott átalakító). 
2. Létrehoz egy kimeneti **eszköz** használt, a kódolási **feladat**kimenetének.
3. Hozzon létre egy bemeneti **eszköz** , és feltölti a megadott helyi videofájl bele. Az objektum lesz a feladat bemenete. 
4. A kódolási feladat a bemeneti és kimeneti létrehozott használatával küldi el.
5. Ellenőrzi a feladat állapotát.
6. Létrehoz egy **lokátor**.
7. Streamelési URL-címeket épít fel.

### <a name="a-idstartusingdotnet-start-using-media-services-apis-with-net-sdk"></a><a id="start_using_dotnet" />A Media Services API-k használatához a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba 

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](https://docs.microsoft.com/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Ez **eszköz** a kódolási feladat bemeneteként használja. A Media Services v3-as, a bemenet egy **feladat** lehetnek, vagy egy **eszköz**, vagy lehet, hogy a Media Services-fiók használatával HTTPS URL-címek számára elérhetővé tenni kívánt tartalmat. Ha többet szeretne tudni a HTTPS URL-címről történő kódolásról, tekintse meg [ezt](job-input-from-http-how-to.md) a cikket.  

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket hajtja végre:

* Létrehoz egy **eszköz** 
* Lekérdezi egy írható [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az eszközre [-tároló](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-a-job"></a>Kimeneti objektum létrehozása feladat eredményeinek tárolásához 

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit. A projekt határozza meg a **DownloadResults** függvényt, amely letölti az eredményt ebből a kimeneti objektumból az „output” mappába, hogy megtekinthesse.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>Átalakítás és a feltöltött fájlt kódoló feladat létrehozása

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Minden egyes új videó új feladatok elküldésével akkor lépnek életbe a recept videókat a tárban. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információkért lásd: [átalakítások és feladatok](transform-concept.md). Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre. 

#### <a name="transform"></a>Átalakítás

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. Az előzetes beállítással kapcsolatos információkért tekintse meg a [sávszélességi skálák automatikus létrehozását](autogen-bitrate-ladder.md) ismertető részt.

Használhatja a beépített EncoderNamedPreset beállítást vagy az egyéni előzetes beállításokat. További információt [a kódoló előzetes beállításainak testreszabását](customize-encoder-presets-how-to.md) ismertető cikkben talál.

[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Feladat

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben a példában a bemeneti videó a helyi számítógépről lett feltöltve. Ha többet szeretne tudni a HTTPS URL-címről történő kódolásról, tekintse meg [ezt](job-input-from-http-how-to.md) a cikket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Az alábbi kódminta bemutatja, hogyan kérdezheti le a [feladat](https://docs.microsoft.com/rest/api/media/jobs) állapotát a szolgáltatásból. Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt.

Az Event Grid egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén.  További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** általában halad végig a következő állapotok: **Ütemezett**, **várólistán**, **feldolgozása**, **befejezett** (a végleges állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#WaitForJobToFinish)]

### <a name="get-a-streaming-locator"></a>A Streamelési Lokátorok beolvasása

A kódolás befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben lehet megvalósítani: először is hozzon létre egy [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), és a második, hozhat létre, amellyel az ügyfelek streamelési URL-címeket. 

A folyamat létrehozásának egy **Streamelési lokátor** közzététel nevezzük. Alapértelmezés szerint a **Streamelési lokátor** érvényes az API-hívások végrehajtása után azonnal, és tart, amíg nem törli, ha nem konfigurál a választható kezdő és befejező időpontok. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben a példában szabadon terjeszthető (vagy nem titkosított) tartalmat fog streamelni, így az előre beállított streamelési szabályzatot (**PredefinedStreamingPolicy.ClearStreamingOnly**) fogja használni.

> [!IMPORTANT]
> Egyéni használatakor [Streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies), korlátozott számú házirendeket tervezzen a Media Services-fiókhoz, és újra alkalmazza őket a StreamingLocators, amikor ugyanazt a titkosítási beállítások és protokollok van szükség. A Media szolgáltatás fiókja rendelkezik Streamelési Hozzáférésiszabályzat-bejegyzések száma tartozó kvótát. Meg kell nem lehet új szabályzatot hoz létre Streamelési az egyes Streamelési lokátor.

Az alábbi kód azt feltételezi, hogy a függvényt egy egyedi locatorName objektummal hívja meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Bár ebben a témakörben a minta azt ismerteti, streaming, használhatja ugyanazt a hívás funkciója a videók progresszív letöltésen keresztül történő továbbítása a Streamelési Lokátorok létrehozásához.

### <a name="get-streaming-urls"></a>Streamelési URL-címek lekérdezése

Most, hogy a [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators) lett hozta létre, megjelenik a streamelési URL-címeket, ahogyan az **GetStreamingURLs**. Egy URL-cím összeállítását, fűzze össze kell a [folyamatos átviteli végponton](https://docs.microsoft.com/rest/api/media/streamingendpoints) állomás neve és a **Streamelési lokátor** elérési útja. Ebben a példában a *alapértelmezett* **folyamatos átviteli végponton** szolgál. Amikor először hozzon létre egy Media Services-fiókját, ezt *alapértelmezett* **folyamatos átviteli végponton** egy leállított állapotba kerül, meg kell hívnia **Start**.

> [!NOTE]
> Ezzel a módszerrel a locatorName létrehozásakor használt kell a **Streamelési lokátor** a kimeneti adategység.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

### <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, StreamingLocator objektumok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

1. Az *EncodeAndStreamFiles* alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
2. Másolja be az egyik streamelési URL-címet a konzolról.

Ebben a példában olyan URL-címek szerepelnek, amelyek lehetővé teszik, hogy a videót többféle protokollal le lehessen játszani:

![Kimenet](./media/stream-files-tutorial-with-api/output.png)

## <a name="test-the-streaming-url"></a>A streamelési URL-cím tesztelése

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Az **URL** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét. 
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Több szál használata

Az Azure Media Services v3 SDK-k nem szálbiztosak. Többszálas alkalmazások fejlesztésekor minden szálhoz ajánlott létrehozni egy új AzureMediaServicesClient objektumot.

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan tölthet fel, kódolhat és streamelhet videókat, tekintse meg a következő cikket: 

> [!div class="nextstepaction"]
> [Videók elemzése](analyze-videos-tutorial-with-api.md)
