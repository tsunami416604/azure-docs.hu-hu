---
title: Stream videofájlok az Azure Media Services – parancssori felület |} A Microsoft Docs
description: Ezen rövid útmutató lépéseivel egy új Azure Media Services-fiókot hozhat létre, fájlt kódolhat, és streamelheti azt az Azure Media Playerbe.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: ''
ms.date: 02/15/2019
ms.author: juliako
ms.openlocfilehash: be1f65b291613997466d9c82782256dc28a5590f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417397"
---
# <a name="quickstart-stream-video-files---cli"></a>Gyors útmutató: Videófájlok streamelése – parancssori felület

Ez a rövid útmutató bemutatja, hogy milyen könnyen kezdheti meg a videók kódolását és streamelését számos különféle böngészőben és eszközön az Azure Media Services használatával. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.
A témakörben szereplő minta olyan tartalmakat kódol, amelyeket HTTPS URL-cím segítségével tehet elérhetővé. Az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

A rövid útmutató végére képes lesz videók streamelésére.  

![Videó lejátszása](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Indítsa el a titkosítása, kódolás, elemzése, kezelése és médiafolyam az Azure-ban, szüksége a Media Services-fiók létrehozása. A Media Services-fiókba kell lennie legalább egy storage-fiókokhoz kapcsolódik.

A Media Services-fiók és a társított tárfiókok az Azure-előfizetéshez kell lennie. Erősen ajánlott a storage-fiókok használata a Media Services-fiók ugyanazon a helyen a további késleltetés és az adatok kimenő adatforgalmi költségek elkerülése érdekében.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account-general-purpose-v2-standard-ragrs"></a>Hozzon létre egy azure storage-fiókot, általános célú v2, Standard-RAGRS

```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_RAGRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-service-account"></a>Egy azure médiaszolgáltatási fiók létrehozása

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-streaming-endpoint"></a>A streamvégpont elindítása

Az alábbi CLI elindítja az alapértelmezett **folyamatos átviteli végponton**.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Miután elindul, kap egy válasz ehhez hasonló:

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Ha a tartalomstreameléshez használt streamvégpont már fut,

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Hozzon létre egy-egy átalakítási adaptív átviteli sebességű kódolás

Hozzon létre egy **átalakítása** és videók elemzése gyakori feladatok konfigurálása. Ebben a példában szeretnénk tenni egy adaptív sávszélességű kódolást. Ezt követően művelettel elküldhet egy **feladat** alatt létrehozott megtekintheti az átalakítás. A feladat a Media Services tényleges kérést az átalakítás alkalmazni a megadott bemeneti video- és audiotartalmak tartalom.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Ehhez hasonló kapott választ:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása

Létrehoz egy kimeneti **eszköz** használt, a kódolási feladat kimenetének.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Ehhez hasonló kapott választ:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-job-with-https-input"></a>A HTTPS bemeneti feladat indítása

A Media Services v3-as a videók feldolgozásához feladatok elküldésekor meg kell mondanunk a Media Services, hogy hol található a bemeneti videó. A beállításokat egyik HTTPS URL-címet adja meg a feladat bemeneti (ahogyan az ebben a példában látható). 

Futtatásakor `az ams job start`, a feladat kimenetének állíthat be egy címkét. A címke később azonosíthatja, hogy a kimeneti adategység Mi az a használható. 

- A címkét rendel egy értéket, ha "--kimeneti-objektumok," assetname = label "
- A címke nem rendel egy értéket, ha "--kimeneti-objektumok," assetname = ".
  Figyelje meg, hogy, adja hozzá a "=" való a `output-assets`. 

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

Ehhez hasonló kapott választ:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Állapot ellenőrzése

Ellenőrizze a feladat állapota 5 perc. Akkor érdemes lehet "befejeződött". Nem, ellenőrizze a pár perc türelmet kérünk. Annak hollétéről"", a következő lépéssel, és hozzon létre egy **Streamelési lokátor**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-streaming-locator-and-get-path"></a>Streamelési lokátor létrehozása és elérési út lekérése

A kódolás befejezését követően a következő lépés az, hogy a videó a kimeneti adategység elérhető az ügyfelek számára a lejátszást. Ezt két lépésben lehet megvalósítani: először is hozzon létre egy **Streamelési lokátor**, és a második, hozhat létre, amellyel az ügyfelek streamelési URL-címeket.

### <a name="create-a-streaming-locator"></a>A Streamelési Lokátorok létrehozásához

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Ehhez hasonló kapott választ:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Streamelési lokátor elérési utak lekérése

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Ehhez hasonló kapott választ:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Másolja a Hls elérési útja. Ebben az esetben: `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-url"></a>URL-cím létrehozása 

### <a name="get-streaming-endpoint-host-name"></a>Le adatfolyam-továbbítási végpont gazdagépnevére

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Másolás a `hostName` értéket. Ebben az esetben: `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-url"></a>Állítsa össze a URL-címe

"https://" + &lt;hostName értékét&gt; + &lt;Hls elérési útjának értéke&gt;

#### <a name="example"></a>Példa

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-with-azure-media-player"></a>Az Azure Media Player lejátszás tesztelése

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Az a **URL-cím:** mezőbe illessze be az előző szakaszban létrehozott URL-CÍMÉT. 

  A Dash, HLS, beillesztheti az URL-címet, vagy Smooth formátum és az Azure Media Player átvált egy megfelelő streamelési protokoll az eszközre lejátszás céljából automatikusan.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportjában lévő egyik erőforrásra sem, beleértve a jelen rövid útmutatóban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott teljes erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Lásd még

Lásd: [hibakódok feladat](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [CLI-minták](cli-samples.md)
