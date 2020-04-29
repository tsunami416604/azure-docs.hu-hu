---
title: Eszközök kezelése a Azure Media Servicesban
titleSuffix: Azure Media Services
description: Olyan eszköz, amelybe be kell töltenie az adathordozót (például feltöltéssel vagy élő betöltéssel), kimeneti adathordozóval (a feladatok kimenetéről), és közzé kell tennie az adathordozót (adatfolyamként). Ez a témakör áttekintést nyújt az új eszközök létrehozásáról és a fájlok feltöltéséről.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80582202"
---
# <a name="manage-assets"></a>Eszközök kezelése

A Azure Media Servicesban egy [eszköz](https://docs.microsoft.com/rest/api/media/assets) , ahol 

* médiafájlok feltöltése egy eszközre
* élő streamek beolvasása és archiválása egy eszközbe
* elemzési feladatok egy eszközre való kódolásának eredményeinek kimenete
* adathordozó közzététele a folyamatos átvitelhez 
* fájlok letöltése egy eszközről.

Ez a témakör áttekintést nyújt arról, hogyan tölthet fel fájlokat egy eszközre, és hogyan végezhet el más gyakori műveleteket. Emellett a kódokra és a kapcsolódó témakörökre mutató hivatkozásokat is tartalmaz.

## <a name="prerequisite"></a>Előfeltétel 

A fejlesztés megkezdése előtt tekintse át a következőt:

* [Alapelvek](concepts-overview.md)
* [Fejlesztés Media Services V3 API](media-services-apis-overview.md) -kkal (beleértve az API-k elérésére, az elnevezési konvenciók stb. vonatkozó információkat) 

## <a name="upload-media-files-into-an-asset"></a>Médiafájlok feltöltése egy eszközre

Miután a digitális fájlokat feltöltötte a Storage-ba, és egy objektumhoz társítva van, a Media Services kódolás, a folyamatos átvitel és a tartalom elemzése munkafolyamatokban is használhatók. Az egyik gyakori Media Services munkafolyamat egy fájl feltöltése, kódolása és továbbítása. Ez a szakasz az általános lépéseket ismerteti.

1. Hozzon létre egy új „bemeneti” adategységet a Media Services v3 API használatával. Ez a művelet létrehoz egy tárolót a Media Services-fiókjához társított tárfiókban. Az API a tároló nevét adja vissza (például: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Ha már rendelkezik egy eszközhöz társítandó blob-tárolóval, megadhatja a tároló nevét az eszköz létrehozásakor. A Media Services jelenleg csak a tároló gyökerében található blobokat támogatja, a fájlnévben elérési utat tartalmazó blobokat nem. Ennélfogva egy „input.mp4” nevű fájlt tartalmazó tároló használható lesz. Azonban a "videos/Inputs/input. mp4" fájlnévvel rendelkező tároló nem fog működni.

    Az Azure CLI-vel közvetlenül feltölthet bármilyen tárfiókba és tárolóba, amelyhez jogosultsággal rendelkezik az előfizetésében.

    A tárolók nevének egyedinek kell lennie, és követnie kell a tárolók elnevezésére vonatkozó irányelveket. A névnek nem kell követnie a Media Services adategység-tárolójának elnevezési formátumát (Adategység-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Használjon egy olvasási és írási engedélyekkel rendelkező SAS URL-címet a digitális fájlok adategység-tárolóba történő feltöltéséhez.

    A Media Services API segítségével [kilistázhatja az adategység-tárolók URL-címét](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    A **AssetContainerSas. listContainerSas** a [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) beállított `expiryTime`ListContainerSasInput paramétert veszi igénybe. Az időt < 24 órára kell beállítani.

    A [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) több sas URL-címet ad vissza, mivel minden egyes Storage-fiókhoz két Storage-fiók kulcsa van. A Storage-fiók két kulccsal rendelkezik, mert segít a Storage-fiókok kulcsainak feladatátvételében és zökkenőmentes elforgatásában. Az első SAS URL-cím az első Storage-fiók kulcsa, a második SAS URL-cím pedig a második kulcsot jelöli.
3. Az Azure Storage API-jait vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t) használva tölthet fel fájlokat az Asset tárolóba.
4. A Media Services v3 API-k segítségével hozzon létre egy Átalakítást és egy Feladatot a „bemeneti” adategység feldolgozásához. További információt az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat.
5. Továbbítsa a tartalmat a "output" objektumból.

### <a name="create-a-new-asset"></a>Új adategység létrehozása

> [!NOTE]
> A DateTime típusú tulajdonságok mindig UTC formátumban jelennek meg.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

A REST-példákat lásd: [eszköz létrehozása Rest](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) -példával.

A példa bemutatja, hogyan hozhatja létre a **kérelem törzsét** , ahol megadhatja a leírást, a tároló nevét, a Storage-fiókot és más hasznos információkat.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>Lásd még

* [Feladathoz tartozó bevitel létrehozása helyi fájlból](job-input-from-local-file-how-to.md)
* [Feladathoz tartozó bemenet létrehozása HTTPS URL-címről](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Élő streamek beolvasása és archiválása egy eszközbe

A Media Services egy [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) objektum, például egy digitális videomagnó, amely az élő streamet az Media Services-fiókban lévő adategységbe fogja fogni és rögzíteni. A rögzített tartalom az [eszköz](https://docs.microsoft.com/rest/api/media/assets) erőforrása által meghatározott tárolóban marad.

További információkért lásd:

* [Egy felhőalapú DVR használata](live-event-cloud-dvr.md)
* [Streaming Live-oktatóanyag](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Feladatok eredményének kimenete egy eszközre

Media Services a videók feldolgozásakor (például kódolás vagy elemzés) létre kell hoznia egy kimeneti [eszközt](assets-concept.md) a [feladatok](transforms-jobs-concept.md)eredményének tárolásához.

További információkért lásd:

* [Videó kódolása](encoding-concept.md)
* [Feladathoz tartozó bevitel létrehozása helyi fájlból](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Eszköz közzététele adatfolyamként

Egy eszköz a folyamatos átvitelhez való közzétételéhez létre kell hoznia egy [folyamatos átviteli lokátort](streaming-locators-concept.md). A folyamatos átviteli lokátornak ismernie kell a közzétenni kívánt eszköz nevét. 

További információkért lásd:

[Oktatóanyag: videók feltöltése, kódolása és továbbítása a Media Services v3 segítségével](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Feladatok eredményeinek letöltése kimeneti eszközről

Ezután letöltheti a feladatok eredményeit egy helyi mappába a Media Service és a Storage API-k használatával. 

Lásd: [fájlok letöltése](download-results-howto.md) példa.

## <a name="filtering-ordering-paging"></a>Szűrés, rendezés, lapozás

Lásd: [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md).

## <a name="next-steps"></a>További lépések

Tekintse meg a teljes kód példáit, amelyek bemutatják, hogyan tölthet fel, kódolhat, elemezheti és közvetítheti élőben és igény szerint: 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.Net](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [Rest](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
