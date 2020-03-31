---
title: Jelleíró hangsávok az Azure Media Services v3-as számával | Microsoft dokumentumok
description: Az oktatóanyag lépéseit követve töltsön fel egy fájlt, kódolja a videót, adjon hozzá leíró hangsávokat, és streamelje a tartalmat a Media Services v3-mal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 09/25/2019
ms.author: juliako
ms.openlocfilehash: 0d8f88e6c2fe273efa969278146de67ba18eaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392188"
---
# <a name="signal-descriptive-audio-tracks"></a>Jelleíró hangsávok

Kísérőszöveg-sávot adhat a videóhoz, hogy a látássérült ügyfelek a kísérőszöveg meghallgatásával kövessék a videofelvételt. A Media Services v3-as számban a leíró hangsávokat a jegyzékfájlban lévő hangsáv feliratozásával jelezheti.

Ez a cikk bemutatja, hogyan kódolhatja a videót, hogyan tölthet fel csak hanggal csak MP4-fájlt (AAC kodek), amely leíró hangot tartalmaz a kimeneti eszközbe, és hogyan szerkesztheti az .ism fájlt, hogy tartalmazza a leíró hangot.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).
- Kövesse az [Access Azure Media Services API-t az Azure CLI-vel,](access-api-cli-how-to.md) és mentse a hitelesítő adatokat. Az API eléréséhez használnia kell őket.
- Tekintse át [a dinamikus csomagolást.](dynamic-packaging-overview.md)
- Tekintse át a [Videók feltöltését, kódolását és streamelését](stream-files-tutorial-with-api.md) bemutatóból.

## <a name="create-an-input-asset-and-upload-a-local-file-into-it"></a>Bemeneti objektum létrehozása és helyi fájl feltöltés az objektumba 

A **CreateInputAsset** függvény létrehoz egy új bemeneti [objektumot](https://docs.microsoft.com/rest/api/media/assets), és feltölti az objektumba a megadott helyi videofájlt. Ez **az eszköz** a kódolási feladat bemeneti bemeneteként szolgál. A Media Services v3-as verzióban a **feladat** bemenete lehet **eszköz,** vagy lehet olyan tartalom, amelyet https-URL-címeken keresztül tesz elérhetővé a Media Services-fiók számára. 

Ha meg szeretné tudni, hogyan kódolhat HTTPS-URL-címről, olvassa el [ezt a cikket.](job-input-from-http-how-to.md)  

A Media Services 3-as verziójában Azure Storage API-k használatával tölthet fel fájlokat. Ennek módját a következő .NET-kódrészlet mutatja be.

A következő függvény ezeket a műveleteket hajtja végre:

* **Eszköz** létrehozása 
* Írható [SAS-URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) kap az eszköz [tárolójának tárolójára](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container)
* Feltölti a fájlt a tárolóba a SAS URL-cím használatával.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Ha a létrehozott bemeneti eszköz nevét más metódusok számára is `Name` át kell adnia, győződjön meg arról, hogy a tulajdonságot az eszközobjektumon például inputAsset.Name visszaadott. `CreateInputAssetAsync` 

## <a name="create-an-output-asset-to-store-the-result-of-the-encoding-job"></a>Kimeneti eszköz létrehozása a kódolási feladat eredményének tárolásához

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit. A következő függvény bemutatja, hogyan hozhat létre egy kimeneti eszközt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Ha a létrehozott kimeneti eszköz nevét más metódusok számára is `Name` át kell adnia, győződjön meg arról, hogy a tulajdonságot az eszközobjektumon például outputAsset.Name visszaadott. `CreateIOutputAssetAsync` 

Ebben a cikkben adja `outputAsset.Name` át az `SubmitJobAsync` `UploadAudioIntoOutputAsset` értéket a és a függvényeknek.

## <a name="create-a-transform-and-a-job-that-encodes-the-uploaded-file"></a>A feltöltött fájlt kódoló átalakítás és feladat létrehozása

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha minden új videóhoz új állásokat küldesz be, akkor ezt a receptet a könyvtárad összes videójára alkalmazod. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információt az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre. 

A következő példa létrehoz egy átalakítót (ha nem létezik).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#EnsureTransformExists)]

A következő függvény beküld egy feladatot.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Várakozás a feladat befejeződésére

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. Azt javasoljuk, hogy az Event Grid használatával várja meg a feladat befejezését.

A feladat általában a következő állapotokon megy keresztül: **Ütemezett**, **Várólistán,** **Feldolgozás**, **Befejezett** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

További információt az [Eseményrács eseményeinek kezelése című témakörben talál.](reacting-to-media-services-events.md)

## <a name="upload-the-audio-only-mp4-file"></a>A csak hanggal audio-MP4 fájl feltöltése

Töltse fel a további csak audio-MP4 fájlt (AAC codec), amely leíró hangot tartalmaz a kimeneti eszközbe.  

```csharp
private static async Task UpoadAudioIntoOutputAsset(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string outputAssetName,
    string fileToUpload)
{
    // Use the Assets.Get method to get the existing asset. 
    // In Media Services v3, the Get method on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).

    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = await client.Assets.GetAsync(resourceGroupName, accountName, outputAssetName);
    
    if (asset != null)
    {
      // Use Media Services API to get back a response that contains
      // SAS URL for the Asset container into which to upload blobs.
      // That is where you would specify read-write permissions 
      // and the exparation time for the SAS URL.
      var response = await client.Assets.ListContainerSasAsync(
          resourceGroupName,
          accountName,
          outputAssetName,
          permissions: AssetContainerPermission.ReadWrite,
          expiryTime: DateTime.UtcNow.AddHours(4).ToUniversalTime());

      var sasUri = new Uri(response.AssetContainerSasUrls.First());

      // Use Storage API to get a reference to the Asset container
      // that was created by calling Asset's CreateOrUpdate method.  
      CloudBlobContainer container = new CloudBlobContainer(sasUri);
      var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));

      // Use Strorage API to upload the file into the container in storage.
      await blob.UploadFromFileAsync(fileToUpload);
    }
}
```

Íme egy példa a `UpoadAudioIntoOutputAsset` függvény hívására:

```csharp
await UpoadAudioIntoOutputAsset(client, config.ResourceGroup, config.AccountName, outputAsset.Name, "audio_description.m4a");
```

## <a name="edit-the-ism-file"></a>Az .ism fájl szerkesztése

Amikor a kódolási feladat befejeződött, a kimeneti eszköz tartalmazza a kódolási feladat által létrehozott fájlokat. 

1. Az Azure Portalon keresse meg a Media Services-fiókhoz társított tárfiókot. 
1. Keresse meg a tárolót a kimeneti eszköz nevével. 
1. A tárolóban keresse meg az .ism fájlt, és kattintson a **Blob szerkesztése** elemre (a jobb oldali ablakban). 
1. Az .ism fájlt szerkesztheti a csak hanggal feltöltött MP4-fájl (AAC kodek) használatával, amely leíró hangot tartalmaz, majd ha végzett, nyomja le a **Mentés** gombot.

    A leíró hangsávok jelzéséhez hozzá kell adnia a "kisegítő lehetőségek" és a "szerep" paramétereket az .ism fájlhoz. Az Ön felelőssége, hogy ezeket a paramétereket helyesen állítsa be, hogy a hangsávot hangleírásként jelezze. Például adja `<param name="accessibility" value="description" />` `<param name="role" value="alternate" />` hozzá és adja hozzá az .ism fájlt egy adott hangsávhoz, ahogy az a következő példában látható.
 
```xml
<?xml version="1.0" encoding="utf-8"?>
<smil xmlns="http://www.w3.org/2001/SMIL20/Language">
  <head>
    <meta name="clientManifestRelativePath" content="ignite.ismc" />
    <meta name="formats" content="mp4-v3" />
  </head>
  <body>
    <switch>
      <audio src="ignite_320x180_AACAudio_381.mp4" systemBitrate="128041" systemLanguage="eng">
        <param name="systemBitrate" value="128041" valuetype="data" />
        <param name="trackID" value="2" valuetype="data" />
        <param name="trackName" value="aac_eng_2_128041_2_1" valuetype="data" />
        <param name="systemLanguage" value="eng" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_2.mpi" valuetype="data" />
      </audio>
      <audio src="audio_description.m4a" systemBitrate="194000" systemLanguage="eng">
        <param name="trackName" value="aac_eng_audio_description" />
        <param name="accessibility" value="description" />
        <param name="role" value="alternate" />     
      </audio>          
      <video src="ignite_1280x720_AACAudio_3549.mp4" systemBitrate="3549855">
        <param name="systemBitrate" value="3549855" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_1280x720_AACAudio_3549_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_960x540_AACAudio_2216.mp4" systemBitrate="2216764">
        <param name="systemBitrate" value="2216764" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_960x540_AACAudio_2216_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_640x360_AACAudio_1154.mp4" systemBitrate="1154569">
        <param name="systemBitrate" value="1154569" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_640x360_AACAudio_1154_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_480x270_AACAudio_721.mp4" systemBitrate="721893">
        <param name="systemBitrate" value="721893" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_480x270_AACAudio_721_1.mpi" valuetype="data" />
      </video>
      <video src="ignite_320x180_AACAudio_381.mp4" systemBitrate="381027">
        <param name="systemBitrate" value="381027" valuetype="data" />
        <param name="trackID" value="1" valuetype="data" />
        <param name="trackName" value="video" valuetype="data" />
        <param name="trackIndex" value="ignite_320x180_AACAudio_381_1.mpi" valuetype="data" />
      </video>
    </switch>
  </body>
</smil>
```

## <a name="get-a-streaming-locator"></a>Streamelési lokátor beszereznie

A kódolás befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben teheti meg: először hozzon létre egy [streamelési lokátort,](https://docs.microsoft.com/rest/api/media/streaminglocators)másodszor pedig hozza létre az ügyfelek által használható streamelési URL-címeket. 

A **streamelési lokátor** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **streamelési lokátor** az API-hívások kezdeményezése után azonnal érvényes, és a törlésig tart, kivéve, ha konfigurálja a választható kezdési és befejezési időpontokat. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben a példában a rendszer a tiszta (vagy nem titkosított tartalmat) streamelést fogja használni, így az előre definiált titkos adatfolyam-továbbítási házirend (**PredefinedStreamingPolicy.ClearStreamingOnly**) lesz használva.

> [!IMPORTANT]
> Egyéni [streamelési szabályzat](https://docs.microsoft.com/rest/api/media/streamingpolicies)használataesetén a Media Service-fiókhoz korlátozott számú ilyen házirendet kell megterveznie, és újra fel kell használnia őket a StreamingLocatorok számára, ha ugyanazokra a titkosítási beállításokra és protokollokra van szükség. A Media Service-fiók rendelkezik kvótával a streamelési szabályzat-bejegyzések számára. Nem hozhat létre új streamelési szabályzatot az egyes streamelési lokátorokhoz.

Az alábbi kód azt feltételezi, hogy a függvényt egy egyedi locatorName objektummal hívja meg.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateStreamingLocator)]

Míg a témakörben szereplő minta a streamelést tárgyalja, ugyanazzal a hívással létrehozhat egy streamelési lokátort a videó progresszív letöltéssel történő kézbesítéséhez.

### <a name="get-streaming-urls"></a>Streamelési URL-címek lekérdezése

Most, hogy létrejött a [streamelési lokátor,](https://docs.microsoft.com/rest/api/media/streaminglocators) lekaphatja a streamelési URL-eket, ahogy az a **GetStreamingURLs-ban**látható. URL-cím létrehozásához össze kell fűznie a [streamelési végpont](https://docs.microsoft.com/rest/api/media/streamingendpoints) gazdagép nevét és a **streamelési lokátor** elérési útját. Ebben a példában az *alapértelmezett* **streamelési végpontot** használja a rendszer. Amikor először hoz létre egy Media Service-fiókot, ez az *alapértelmezett* **streamelési végpont** leállított állapotban lesz, ezért meg kell hívnia a Start **programot.**

> [!NOTE]
> Ebben a módszerben a kimeneti eszköz **streamelési lokátorának** létrehozásakor használt lokátornév szükséges.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#GetStreamingURLs)]

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)webböngészőt, és keresse meg a ját.
2. Az **URL-cím:** mezőbe illessze be az alkalmazásból kapott streamelési URL-értékek egyikét. 
 
     Az URL-címet HLS, Dash vagy Smooth formátumba illesztheti be, és az Azure Media Player automatikusan átvált a megfelelő streamelési protokollra az eszközön való lejátszáshoz.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="next-steps"></a>További lépések

[Videók elemzése](analyze-videos-tutorial-with-api.md)
