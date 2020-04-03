---
title: Eszközök kezelése az Azure Media Servicesben
titleSuffix: Azure Media Services
description: Olyan eszköz, ahol az adathordozót (például feltöltés vagy élő betöltés révén), kimeneti adathordozót (egy feladat kimenetéből) és adathordozót tesz közzé (streameléshez). Ez a témakör áttekintést nyújt egy új eszköz létrehozásáról és a fájlok feltöltéséről.
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
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582202"
---
# <a name="manage-assets"></a>Eszközök kezelése

Az Azure Media Servicesben egy [eszköz](https://docs.microsoft.com/rest/api/media/assets) az a hely, ahol 

* médiafájlok feltöltése egy eszközbe,
* élő közvetítések betöltése és archiválása egy eszközbe,
* az elemzési feladat kódolásának eredményeit egy eszközre adja át,
* média közzététele streamelésre, 
* fájlok letöltése egy eszközről.

Ez a témakör áttekintést ad arról, hogyan tölthet fel fájlokat egy eszközbe, és hogyan hajthat végre néhány más gyakori műveletet. Azt is előírja, hogy a linkeket a kód minták és a kapcsolódó témákat.

## <a name="prerequisite"></a>Előfeltétel 

A fejlesztés megkezdése előtt tekintse át a következőket:

* [Alapelvek](concepts-overview.md)
* [Fejlesztés a Media Services 3-as vAPI-jával](media-services-apis-overview.md) (információkat tartalmaz az API-k eléréséről, elnevezési konvenciókról és így tovább) 

## <a name="upload-media-files-into-an-asset"></a>Médiafájlok feltöltése eszközbe

Miután a digitális fájlokat feltöltötte a tárolóba, és társított egy eszköz, akkor fel lehet használni a Media Services kódolás, streamelés, és elemzése tartalmi munkafolyamatok. A Media Services egyik gyakori munkafolyamata egy fájl feltöltése, kódolása és streamelése. Ez a szakasz az általános lépéseket ismerteti.

1. Hozzon létre egy új „bemeneti” adategységet a Media Services v3 API használatával. Ez a művelet létrehoz egy tárolót a Media Services-fiókjához társított tárfiókban. Az API a tároló nevét `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`adja vissza (például).

    Ha már rendelkezik egy blob tároló, amely szeretne társítani egy eszköz, megadhatja a tároló nevét, amikor létrehozza az eszközt. A Media Services jelenleg csak a tároló gyökerében található blobokat támogatja, a fájlnévben elérési utat tartalmazó blobokat nem. Ennélfogva egy „input.mp4” nevű fájlt tartalmazó tároló használható lesz. A "videos/inputs/input.mp4" fájlnévvel rendelkező tároló azonban nem fog működni.

    Az Azure CLI-vel közvetlenül feltölthet bármilyen tárfiókba és tárolóba, amelyhez jogosultsággal rendelkezik az előfizetésében.

    A tárolók nevének egyedinek kell lennie, és követnie kell a tárolók elnevezésére vonatkozó irányelveket. A névnek nem kell követnie a Media Services adategység-tárolójának elnevezési formátumát (Adategység-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Használjon egy olvasási és írási engedélyekkel rendelkező SAS URL-címet a digitális fájlok adategység-tárolóba történő feltöltéséhez.

    A Media Services API segítségével [kilistázhatja az adategység-tárolók URL-címét](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **Az AssetContainerSas.listContainerSas** egy [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) paramétert vesz fel, amelyen beállította a értékét. `expiryTime` Az időt 24 < kell állítani.

    [A ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) több SAS-URL-t ad vissza, mivel minden tárfiókhoz két tárfiók kulcs ad vissza. A tárfiók két kulcs, mert segít a feladatátvétel és a tárfiók kulcsok zökkenőmentes elforgatása. Az első SAS-URL-cím az első tárfiókkulcsot, a második SAS-URL-cím pedig a második kulcsot jelöli.
3. Az Azure Storage API-k vagy SDK-k (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)segítségével fájlokat tölthet fel az eszköztárolóba.
4. A Media Services v3 API-k segítségével hozzon létre egy Átalakítást és egy Feladatot a „bemeneti” adategység feldolgozásához. További információt az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat.
5. A tartalom streamelése a "kimeneti" eszközről.

### <a name="create-a-new-asset"></a>Új adategység létrehozása

> [!NOTE]
> Az eszköz Datetime típusú tulajdonságai mindig UTC formátumúak.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

A REST példa: [Hozzon létre egy eszköz REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) példa.

A példa bemutatja, hogyan hozhat létre a **kérelem törzse,** ahol megadhatja a leírást, a tároló nevét, a tárfiókot és egyéb hasznos adatokat.

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

* [Feladatbevitel létrehozása helyi fájlból](job-input-from-local-file-how-to.md)
* [Feladatbevitel létrehozása HTTPS-URL-címről](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Élő közvetítések betöltése és archiválása egy eszközbe

A Media Services szolgáltatásban az [Élő kimenet](https://docs.microsoft.com/rest/api/media/liveoutputs) objektum olyan, mint egy digitális videofelvevő, amely az élő közvetítést a Media Services-fiók egy eszközébe rögzíti. A rögzített tartalom megmarad az [eszköz](https://docs.microsoft.com/rest/api/media/assets) erőforrás által meghatározott tárolóban.

További információkért lásd:

* [Egy felhőalapú DVR használata](live-event-cloud-dvr.md)
* [Élő oktatóanyag streamelése](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Feladat eredményeinek kimenetele egy eszközre

A Media Services szolgáltatásban a videók feldolgozásakor (például kódolás vagy elemzés) létre kell hoznia egy kimeneti [eszközt](assets-concept.md) a [feladat](transforms-jobs-concept.md)eredményének tárolásához.

További információkért lásd:

* [Videó kódolása](encoding-concept.md)
* [Feladatbevitel létrehozása helyi fájlból](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Eszköz közzététele streameléshez

Egy eszköz streameléshez való közzétételéhez létre kell hoznia egy [streamelési lokátort.](streaming-locators-concept.md) A streamelési lokátornak ismernie kell a közzétenni kívánt eszköznevet. 

További információkért lásd:

[Oktatóanyag: Videók feltöltése, kódolása és streamelése a Media Services v3-as számával](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Feladat eredményeinek letöltése kimeneti eszközről

Ezután letöltheti a feladat ezen eredményeit egy helyi mappába a Media Service és a Storage API-k használatával. 

Lásd a [fájlok letöltése](download-results-howto.md) példa.

## <a name="filtering-ordering-paging"></a>Szűrés, rendelés, lapozás

Lásd: [Media Services-entitások szűrése, rendelése, lapozása.](entities-overview.md)

## <a name="next-steps"></a>További lépések

Tekintse meg a teljes kódpéldákat, amelyek bemutatják, hogyan lehet feltölteni, kódolni, elemezni, streamelni élőben és igény szerint: 

* [Jáva](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [PIHENÉS](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
