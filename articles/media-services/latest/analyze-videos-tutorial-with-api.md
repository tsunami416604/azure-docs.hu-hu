---
title: Videók elemzése a Media Services 3-as v3-as ával
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan elemezheti a videókat az Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b7864d89cc14a1473fd43e94bfe74c368bcb391d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349485"
---
# <a name="tutorial-analyze-videos-with-media-services-v3"></a>Oktatóanyag: Videók elemzése a Media Services v3-as készülékkel

> [!NOTE]
> Annak ellenére, hogy ez az oktatóanyag a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) példákat használja, az általános lépések megegyeznek a [REST API,](https://docs.microsoft.com/rest/api/media/liveevents) [cli](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest), vagy más támogatott [SDK-k](media-services-apis-overview.md#sdks)esetében.

Az oktatóanyag bemutatja, hogyan elemezhet videókat az Azure Media Serviceszel. Bizonyos esetekben szüksége lehet a rögzített video- és audiotartalmak részletesebb elemzésére. Egy vállalatnál például a vásárlói elégedettség növelése érdekében szükség lehet a beszéd szöveggé alakítására, hogy az ügyfélszolgálati beszélgetéseket kereshető katalógussá alakítsák, amelyhez indexek és irányítópultok érhetőek el. Ezután betekintést nyerhetnek az üzletükbe. Ezek az elemzések tartalmazzák a gyakori panaszok listáját, az ilyen panaszok forrásait és egyéb hasznos információkat.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Töltse le a témakörben ismertetett mintaalkalmazást.
> * Vizsgálja meg a megadott videót elemző kódot.
> * Futtassa az alkalmazást.
> * Vizsgálja meg a kimenetet.
> * Az erőforrások megtisztítása.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság
 
Fontos emlékeztetőként be kell tartania a Video Indexer használata során érvényes összes vonatkozó jogszabályt, és nem használhatja a Video Indexert vagy bármely más Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy másokra nézve káros lehet. Mielőtt bármilyen videót, beleértve a biometrikus adatokat is, feltöltené a Video Indexer szolgáltatásba feldolgozásra és tárolásra, önnek rendelkeznie kell a videóban lévő egyén(ek) minden megfelelő jogával, beleértve az összes megfelelő hozzájárulást. Ha többet szeretne megtudni a Megfelelőségről, az adatvédelemről és a biztonságról a Video Indexerben, a Microsoft [Cognitive Services használati feltételei.](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) A Microsoft adatvédelmi kötelezettségeivel és az Ön adatainak kezelésével kapcsolatban olvassa el a Microsoft [adatvédelmi nyilatkozatát,](https://privacy.microsoft.com/PrivacyStatement)az [online szolgáltatási feltételeket](https://www.microsoft.com/licensing/product-licensing/products) ("OST") és [az adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). További adatvédelmi információk, beleértve az adatmegőrzést, törlést / megsemmisítést, elérhetők az OST-ben és [itt.](../video-indexer/faq.md) A Video Indexer használatával ön elfogadja, hogy a Cognitive Services feltételei, az OST, a DPA és az adatvédelmi nyilatkozat kötelező érvényű.

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, kapjuk meg a [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)alkalmazást.
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).<br/>Ügyeljen arra, hogy emlékezzen az erőforráscsoport nevéhez és a Media Services-fiók nevéhez használt értékekre.
- Kövesse az [Access Azure Media Services API-t az Azure CLI-vel,](access-api-cli-how-to.md) és mentse a hitelesítő adatokat. Az API eléréséhez használnia kell őket.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozza a gépre a .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A minta az [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) mappában található.

Nyissa meg [az appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/appsettings.json) fájlt a letöltött projektben. Cserélje le az értékeket az [API-k eléréséből](access-api-cli-how-to.md)származó hitelesítő adatokra.

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>A megadott videót elemző kód vizsgálata

Ez a szakasz az *AnalyzeVideos* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) nevű fájljában megadott függvényeket mutatja be.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítást** és egy **feladatot,** amely elemzi a videót.
2. Létrehoz egy bemeneti **eszközt,** és feltölti a videót. Az objektum lesz a feladat bemenete.
3. Létrehoz egy kimeneti objektumot, amely a feladat kimenetét tárolja.
4. Elküldi a feladatot.
5. Ellenőrzi a feladat állapotát.
6. Letölti a feladat futtatásának eredményeként kapott fájlokat.

> [!NOTE]
> Előzetes Video vagy Audio Analyzer-beállítások használata esetén állítsa be a fiókját 10 S3-as Media szolgáltatás számára fenntartott egységre az Azure Portalon. További információ: [Az adathordozó-feldolgozás méretezése](media-reserved-units-cli-how-to.md).

### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba 

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](https://docs.microsoft.com/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Az objektum lesz a kódolási feladat bemenete. A Media Services 3-as verziójában a feladat bemenete lehet egy objektum vagy egy olyan tartalom, amelyet egy HTTPS URL-cím használatával tesz elérhetővé a Media Services-fiók számára. A HTTPS-URL-címről történő kódolásról [ebben a](job-input-from-http-how-to.md) cikkben olvashat.  

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket fejezi be:

* Eszköz létrehozása.
* Írható [SAS-URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) kap az eszköz [tárolójának tárolójában.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)

    Ha az eszköz [ListContainerSas](https://docs.microsoft.com/rest/api/media/assets/listcontainersas) függvény segítségével sas URL-eket, vegye figyelembe, hogy a függvény több SAS URL-eket ad vissza, mivel minden tárfiókhoz két tárfiók kulcs ad vissza. A tárfiók két kulccsal rendelkezik, mert lehetővé teszi a tárfiók kulcsainak zökkenőmentes elforgatását (például módosítsa az egyiket a másik használata közben, majd kezdje el az új kulcsot, és forgassa el a másik kulcsot). Az első SAS-URL a tárolási kulcsot1 és a második tárolási kulcsot2 jelöli.
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Kimeneti objektum létrehozása a feladat eredményeinek tárolásához

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a feladat eredményeit. A projekt határozza meg a **DownloadResults** függvényt, amely letölti az eredményt ebből a kimeneti objektumból az „output” mappába, hogy megtekinthesse.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Átalakítás és videókat elemző feladat létrehozása

A Media Services tartalmának kódolásakor vagy feldolgozásakor gyakori minta a kódolási beállítások receptként való beállítása. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha minden új videóhoz új állásokat küldesz be, akkor ezt a receptet a könyvtárod összes videójára alkalmazod. A Media Services receptje **it transform-nak nevezzük.** További információt az [Átalakítások és feladatok](transform-concept.md)című témakörben talál. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet a megadott videó elemzésére.

#### <a name="transform"></a>Átalakítás

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. **A TransformOutput** kötelező paraméter. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Ebben a példában a **VideoAnalyzerPreset** előre beállított, és a nyelv ("en-US") átkerül a konstruktor (`new VideoAnalyzerPreset("en-US")`). Ez az előzetes beállítás lehetővé teszi több audio- és videoelemzés elvégzését a videón. Az **AudioAnalyzerPreset** előzetes beállítás akkor lehet hasznos, ha több audioelemzést szeretne elvégezni a videón.

**Átalakítás**létrehozásakor először ellenőrizze, hogy létezik-e már a **Get** metódus használatával, ahogy az a következő kódban látható. A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Feladat

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** olyan információkat ad meg, mint például a bemeneti videó helye és a kimenet helye. A videó helyét a következők használatával adhatja meg: HTTPS URL-címek, SAS URL-címek, vagy a Media Service-fiókban található objektumok.

A jelen példában a feladat bemeneti objektuma egy helyi videó.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A munka elvégzéséhez idő kell. Ha ez megtörténik, értesítést szeretne kapni. Több módon is kérhet értesítést a [feladat](https://docs.microsoft.com/rest/api/media/jobs) végrehajtásáról. A legegyszerűbb lehetőség (ez itt látható) a lekérdezés használata.

A lekérdezés nem ajánlott ajánlott eljárás éles alkalmazások potenciális késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt.

Az Event Grid egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén. További információ: [Események irányítása egyéni webes végpontra](job-state-events-cli-how-to.md)című témakörben talál.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapotot kapja. Ha a feladat megszakítása folyamatban van, akkor a Megszakítás, majd a Visszavonás **parancsra** **kerül,** amikor elkészült.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="download-the-result-of-the-job"></a>A feladat eredményének letöltése

A következő függvény letölti az eredményeket a kimeneti [eszköza](https://docs.microsoft.com/rest/api/media/assets) a "kimeneti" mappába, így megvizsgálhatja a feladat eredményeit.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Erőforrások eltávolítása a Media Services-fiókban

Általában mindent meg kell tisztítania, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra fel kell használnia az átalakításokat, és meg kell tartania a StreamingLocators-okat). Ha azt szeretné, hogy a fiók a kísérletezés után tiszta legyen, törölje azokat az erőforrásokat, amelyeket nem kíván újra felhasználni. A következő kód például törli a Feladatok:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Nyomja le a Ctrl+F5 billentyűkombinációt az *AnalyzeVideos* alkalmazás futtatásához.

A program futtatásakor a feladat külön miniatűrt hoz létre a videóban található minden egyes archoz. Ezenkívül létrehozza az insights.json fájlt.

## <a name="examine-the-output"></a>A kimenet vizsgálata

A videók elemzésekor keletkező kimeneti fájl neve insights.json. Ez a fájl tartalmazza a videó elemzésének eredményeit. A [médiaintelligenciával](intelligence-concept.md) kapcsolatos cikk részletesen leírja a json-fájl elemeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.

Hajtsa végre a következő CLI parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Több szál használata

Az Azure Media Services v3 SDK-k nem szálbiztos. Ha többszálas alkalmazással dolgozik, hozzon létre egy új AzureMediaServicesClient-objektumot szálonként.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: fájlok feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
