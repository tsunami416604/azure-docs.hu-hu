---
title: Videofájlok streamelése az Azure Media Services és az Azure CLI szolgáltatással
description: Az oktatóanyag lépéseit követve hozzon létre egy új Azure Media Services-fiókot, kódtasson egy fájlt, és továbbítsa azt az Azure Media Playerbe.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: ''
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 91259e10966173cb701b867f5b3ed362112beef3
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382783"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Oktatóanyag: Távoli fájl kódolása URL-cím alapján és a videó streamelése - Azure CLI

Ez az oktatóanyag bemutatja, hogyan kódolhatja és streamelheti a videókat különböző böngészőkben és eszközökön az Azure Media Services és az Azure CLI használatával. A bemeneti tartalom https- vagy SAS-URL-címek vagy az Azure Blob storage-beli fájlok elérési útjai használatával adható meg.

A jelen cikkben szereplő példa kódolja a HTTPS URL-címen keresztül elérhetővé tett tartalmakat. A Media Services 3-as v3-as alkalmazása jelenleg nem támogatja a HTTPS URL-címeken keresztüli darabolt átviteli kódolást.

Az oktatóanyag végére videót streamelhet.  

![Videó lejátszása](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Mielőtt titkosíthatja, kódolhatja, elemezheti, kezelheti és streamelheti a médiatartalmakat az Azure-ban, létre kell hoznia egy Media Services-fiókot. Ezt a fiókot egy vagy több tárfiókhoz kell társosan kezelni.

A Media Services-fióknak és az összes társított tárfióknak ugyanabban az Azure-előfizetésben kell lennie. Azt javasoljuk, hogy a Media Services-fiókkal azonos helyen lévő tárfiókok at használja a késés és az adatok kimenő forgalom költségeinek korlátozására.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

Ebben a példában egy általános célú v2 standard LRS-fiókot hozunk létre.

Ha tárfiókokkal szeretne kísérletezni, használja a használatát. `--sku Standard_LRS` Amikor termelési termékváltozatot választ, fontolja `--sku Standard_RAGRS`meg a használatát, amely földrajzi replikációt biztosít az üzletmenet folytonosságához. További információt a [tárfiókok című témakörben talál.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Kapsz egy választ, mint ez:

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

## <a name="start-the-streaming-endpoint"></a>A streamvégpont elindítása

A következő Azure CLI parancs elindítja az alapértelmezett **streamelési végpontot.**

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Kapsz egy választ, mint ez:

```
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

Ha a streamelési végpont már fut, ez az üzenet jelenik meg:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Átalakítás létrehozása adaptív sávszélességű kódoláshoz

Hozzon létre egy **átalakítást** a videók kódolásához vagy elemzéséhez használt gyakori feladatok konfigurálásához. Ebben a példában adaptív sávszélességű kódolást végezünk. Ezután benyújtunk egy munkát az általunk létrehozott átalakítás alatt. A feladat az a kérés a Media Services-hez, hogy alkalmazza az átalakítást az adott video- vagy hangtartalom-bemenetre.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Kapsz egy választ, mint ez:

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

Hozzon létre egy kimeneti **eszközt** a kódolási feladat kimeneteként használva.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Kapsz egy választ, mint ez:

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

## <a name="start-a-job-by-using-https-input"></a>Feladat indítása HTTPS-bemenettel

Amikor feladatokat küld be a videók feldolgozásához, meg kell mondania a Media Services-nek, hogy hol találja a bemeneti videót. Az egyik lehetőség egy HTTPS URL-cím megadása a feladat bemeneteként, ahogy az ebben a példában látható.

Futtatásakor `az ams job start`a feladat kimenetén címke is beállítható. Ezután a címke segítségével azonosíthatja, hogy mi a kimeneti eszköz.

- Ha értéket rendel a címkéhez, állítsa a "--output-assets" értéket "assetname=label" értékre.
- Ha nem rendel értéket a címkéhez, állítsa a "--output-assets" értéket "assetname=" értékre.

  Figyelje meg, hogy a `output-assets`"=" értéket adjuk hozzá a hoz.

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Kapsz egy választ, mint ez:

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

Öt perc múlva ellenőrizze a feladat állapotát. "Késznek" kellene lennie. Még nincs kész, ellenőrizze újra néhány perc múlva. Ha elkészült, folytassa a következő lépéssel, és hozzon létre egy **streamelési lokátort.**

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Hozzon létre egy streamelési lokátort, és szerezzen be egy elérési utat

A kódolás befejezése után a következő lépés az, hogy a videó a kimeneti eszköz elérhetővé az ügyfelek számára a lejátszás. Ehhez először hozzon létre egy streamelési lokátort. Ezután hozzon létre streamelési URL-címeket, amelyeket az ügyfelek használhatnak.

### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Kapsz egy választ, mint ez:

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

### <a name="get-streaming-locator-paths"></a>Streamelési lokátorelérési utak beszereznie

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Kapsz egy választ, mint ez:

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

Másolja a HTTP élő streamelési (HLS) elérési útját. Ebben az esetben, `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`ez .

## <a name="build-the-url"></a>Az URL-cím összeállítása

### <a name="get-the-streaming-endpoint-host-name"></a>A streamelési végpont állomásnevének beszereznie

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Másolja `hostName` az értéket. Ebben az esetben, `amsaccount-usw22.streaming.media.azure.net`ez .

### <a name="assemble-the-url"></a>Az URL összeállítása

"https:// " &lt;+&gt; + &lt;hostName érték Hls elérési út értéke&gt;

Például:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>A lejátszás tesztelése az Azure Media Player használatával

> [!NOTE]
> Ha egy játékos HTTPS-webhelyen található, mindenképpen "https" használatával indítsa el az URL-t.

1. Nyisson meg egy [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)webböngészőt, és nyissa meg a megnyitását.
2. Az **URL-cím** mezőbe illessze be az előző szakaszban beépített URL-címet. Az URL-címet HLS, Dash vagy Smooth formátumba illesztheti be. Az Azure Media Player automatikusan egy megfelelő streamelési protokollt fog használni az eszközön történő lejátszáshoz.
3. Válassza **a Player frissítése**lehetőséget.

>[!NOTE]
>Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyetlen erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services- és tárfiókokat, törölje az erőforráscsoportot.

Futtassa ezt az Azure CLI parancsot:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>További lépések

[A Media Services áttekintése](media-services-overview.md)
