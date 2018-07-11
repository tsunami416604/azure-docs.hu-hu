---
title: Videók elemzése az Azure Media Serviceszel | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan elemezhet videókat az Azure Media Serviceszel.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/28/2018
ms.author: juliako
ms.openlocfilehash: e5b8cc9285d9637a04c4bec47b0185652aa7c84e
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114224"
---
# <a name="tutorial-analyze-videos-with-azure-media-services"></a>Oktatóanyag: Videók elemzése az Azure Media Serviceszel 

Az oktatóanyag bemutatja, hogyan elemezhet videókat az Azure Media Serviceszel. Bizonyos esetekben szüksége lehet a rögzített video- és audiotartalmak részletesebb elemzésére. Egy vállalatnál például a vásárlói elégedettség növelése érdekében szükség lehet a beszéd szöveggé alakítására, hogy az ügyfélszolgálati beszélgetéseket kereshető katalógussá alakítsák, amelyhez indexek és irányítópultok érhetőek el. Ezt elemezve azonosíthatók a leggyakoribb panaszok, a panaszok forrásai és egyéb problémák.

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Media Services-fiók létrehozása
> * Hozzáférés a Media Services API-hoz
> * A mintaalkalmazás konfigurálása
> * A megadott videót elemző kód vizsgálata
> * Az alkalmazás futtatása
> * A kimenet vizsgálata
> * Az erőforrások eltávolítása

> [!Note]
> A [médiafeldolgozás méretezésével](../previous/media-services-scale-media-processing-overview.md) foglalkozó szakaszban leírt módon állítsa be a Media Services-fiókot 10 S3-as Media szolgáltatás számára fenntartott egységre az Azure Portalon.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha nincs telepítve a Visual Studio, szerezze be a [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)-et.

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a gépre a .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

A minta az [AnalyzeVideos](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/AnalyzeVideos) mappában található.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-analyzes-the-specified-video"></a>A megadott videót elemző kód vizsgálata

Ez a szakasz az *AnalyzeVideos* projekt [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/AnalyzeVideos/Program.cs) nevű fájljában megadott függvényeket mutatja be.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy átalakítást és egy feladatot, amely elemzi a videót.
2. Létrehoz egy bemeneti objektumot, és feltölti a videót az objektumba. Az objektum lesz a feladat bemenete.
3. Létrehoz egy kimeneti objektumot, amely a feladat kimenetét tárolja. 
4. Elküldi a feladatot.
5. Ellenőrzi a feladat állapotát.
6. Letölti a feladat futtatásának eredményeként kapott fájlokat. 

### <a name="start-using-media-services-apis-with-net-sdk"></a>A Media Services API-k használatának megkezdése a .NET SDK-val

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. A cikk elején klónozott kódban a **GetCredentialsAsync** függvény létrehozza a ServiceClientCredentials objektumot a helyi konfigurációs fájlban szereplő hitelesítési adatok alapján. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateMediaServicesClient)]

### <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba 

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](https://docs.microsoft.com/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Az objektum lesz a kódolási feladat bemenete. A Media Services 3-as verziójában a feladat bemenete lehet egy objektum vagy egy olyan tartalom, amelyet egy HTTPS URL-cím használatával tesz elérhetővé a Media Services-fiók számára. Ha többet szeretne tudni a HTTPS URL-címről történő kódolásról, tekintse meg [ezt](job-input-from-http-how-to.md) a cikket.  

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket hajtja végre:

* Létrehoz egy adategységet. 
* Lekér egy írható [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az objektum [tárolójába](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container).
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateInputAsset)]

### <a name="create-an-output-asset-to-store-the-result-of-the-job"></a>Kimeneti objektum létrehozása a feladat eredményeinek tárolásához 

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a feladat eredményeit. A projekt határozza meg a **DownloadResults** függvényt, amely letölti az eredményt ebből a kimeneti objektumból az „output” mappába, hogy megtekinthesse.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CreateOutputAsset)]

### <a name="create-a-transform-and-a-job-that-analyzes-videos"></a>Átalakítás és videókat elemző feladat létrehozása

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha minden egyes új videó esetében elküld egy új feladatot, ezt a receptet fogja alkalmazni a könyvtár összes videójára. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információkat az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet a megadott videó elemzésére. 

#### <a name="transform"></a>Átalakítás

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az a fenti kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Ebben a példában a **VideoAnalyzerPreset** előzetes beállítást használjuk, és a nyelvet ("en-US") átadjuk a konstruktorának. Ez az előzetes beállítás lehetővé teszi több audio- és videoelemzés elvégzését a videón. Az **AudioAnalyzerPreset** előzetes beállítás akkor lehet hasznos, ha több audioelemzést szeretne elvégezni a videón. 

**Átalakítások** létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services 3-as verziója esetében a **Get** metódusok **null** értéket adnak vissza, ha az entitás nem létezik (a kis- és nagybetűket meg nem különböztető névellenőrzés történik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#EnsureTransformExists)]

#### <a name="job"></a>Feladat

Ahogy korábban említettük, az [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) objektum a recept, a [feladat](https://docs.microsoft.com/en-us/rest/api/media/jobs) pedig maga a kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét. A videó helyét a következők használatával adhatja meg: HTTPS URL-címek, SAS URL-címek, vagy a Media Service-fiókban található objektumok. 

A jelen példában a feladat bemeneti objektuma egy helyi videó.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#SubmitJob)]

### <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Több módon is kérhet értesítést a [feladat](https://docs.microsoft.com/en-us/rest/api/media/jobs) végrehajtásáról. Ezek közül a legegyszerűbb az itt bemutatott lekérdezés. 

Éles alkalmazások esetében nem javasolt a lekérdezés használata a lehetséges késés miatt. Túlzott használat esetén a lekérdezés kapacitása korlátozott lehet egy adott fiókban. Fejlesztőknek inkább az Event Grid használata javasolt.

Az Event Grid egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén. További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#WaitForJobToFinish)]

### <a name="download-the-result-of-the-job"></a>A feladat eredményének letöltése

A következő függvény letölti az eredményeket a kimeneti [objektumból](https://docs.microsoft.com/rest/api/media/assets) az output nevű mappába, hogy megvizsgálhassa a feladat eredményeit. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#DownloadResults)]

### <a name="clean-up-resource-in-your-media-services-account"></a>Erőforrások eltávolítása a Media Services-fiókban

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, állandó StreamingLocatorok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni. A következő kóddal például törölheti a feladatokat.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/AnalyzeVideos/Program.cs#CleanUp)]

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Az *AnalyzeVideos* alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűparancsot.

A program futtatásakor a feladat külön miniatűrt hoz létre a videóban található minden egyes archoz. Ezenkívül létrehozza az insights.json fájlt.

## <a name="examine-the-output"></a>A kimenet vizsgálata

A videók elemzésekor keletkező kimeneti fájl neve insights.json. Ez a fájl tartalmazza a videó elemzésének eredményeit. A [médiaintelligenciával](intelligence-concept.md) kapcsolatos cikk részletesen leírja a json-fájl elemeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot. Ehhez használhatja a **CloudShell** eszközt.

Hajtsa végre az alábbi parancsot a **CloudShellben**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="multithreading"></a>Több szál használata

Az Azure Media Services v3 SDK-k nem szálbiztosak. Többszálas alkalmazások használatakor minden szálhoz ajánlott létrehozni egy új AzureMediaServicesClient objektumot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: fájlok feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
