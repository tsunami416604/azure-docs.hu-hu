---
title: Stream videofájlok az Azure Media Services és az Azure CLI |} A Microsoft Docs
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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: a323cbe4188207fa77525648297b366c9c57121b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244723"
---
# <a name="quickstart-stream-video-files---cli"></a>Gyors útmutató: Videófájlok streamelése – parancssori felület

Ez a rövid útmutató bemutatja, hogyan könnyen kódolása és streamelése videók a különböző böngészők és eszközök az Azure Media Services és az Azure CLI használatával. Megadhatja a bemeneti tartalom HTTPS vagy az SAS URL-címek vagy az elérési utak a fájlokat az Azure Blob storage használatával.

A példában ez a cikk a tartalmak, hogy az URL-címének HTTPS-en keresztül elérhető kódolja. A Media Services v3 jelenleg nem támogatja a darabolásos átvitel kódolási HTTPS URL-címek keresztül.

Ez a rövid útmutató végéig, képes lesz a videó továbbításához.  

![Videó lejátszása](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Media Services-fiók létrehozása

Mielőtt titkosítására, kódolására, elemezheti, kezelése, és az Azure-ban a médiatartalmak streamelése, szüksége a Media Services-fiók létrehozása. A fióknak kell lennie legalább egy storage-fiókokhoz kapcsolódik.

A Media Services-fiók és a társított tárfiókok az Azure-előfizetéshez kell lennie. Azt javasoljuk, hogy használja-e tárfiókok, amelyek ugyanazon a helyen, a Media Services-fiók, késés és az adatok kimenő adatforgalmi költségek korlátozására.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Azure-tárfiók létrehozása

Ebben a példában létrehozunk egy általános célú v2 standard szintű LRS-fiókra.

Ha azt szeretné, kísérletezhet a storage-fiókok, `--sku Standard_LRS`. Ha éles környezetben a Termékváltozat exportálná, érdemes `--sku Standard_RAGRS`, amely biztosítja az üzletmenet folytonosságának földrajzi replikáció. További információkért lásd: [tárfiókok](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest).
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

A következőhöz hasonló választ kap:

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

A következő Azure CLI-parancs elindítja az alapértelmezett **Sstreaming végpont**.

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

A következőhöz hasonló választ kap:

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

Ha a tartalomstreameléshez használt streamvégpont már fut, ezt az üzenetet kapja:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Hozzon létre egy-egy átalakítási adaptív sávszélességű Encoding

Hozzon létre egy **átalakítása** és videók elemzése gyakori feladatok konfigurálása. Ebben a példában végzünk az adaptív bitsebességű kódolást. Hogy majd alatt létrehozott az átalakítási feladat elküldése. A feladat a Media Services az átalakítás alkalmazhat az adott videó vagy a tartalom hangbemenet kérést.

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

A következőhöz hasonló választ kap:

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

Hozzon létre egy kimeneti **eszköz** használatára, mert a kódolási feladat kimenetének.

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

A következőhöz hasonló választ kap:

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

## <a name="start-a-job-by-using-https-input"></a>A HTTPS bemeneti feladat indítása

Elküldött feladatok videók feldolgozásához, amikor meg kell mondanunk a Media Services, hogy hol található a bemeneti videó. Az egyik lehetőség, hogy HTTPS URL-címet adja meg a feladat bemenetére, ebben a példában látható módon.

Futtatásakor `az ams job start`, a feladat kimenetének állíthat be egy címkét. Ezután használhatja a címke azonosíthatja a kimeneti adategység Mi az a.

- A címkét rendel egy értéket, ha "--kimeneti-objektumok," assetname = label ".
- Állítsa a felirat nem rendel hozzá egy értéket, ha "--kimeneti-objektumok," assetname = ".

  Figyelje meg, hogy adja hozzá a "=" való a `output-assets`.

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

A következőhöz hasonló választ kap:

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

Öt perc alatt a feladat állapotának ellenőrzéséhez. Érdemes lehet "befejeződött volna." Nem fejeződött be, ellenőrizze újra néhány perc múlva. Amikor elkészült, folytassa a következő lépéssel, és hozzon létre egy **Streamelési lokátor**.

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>A streamelési lokátorok létrehozásához és a egy elérési út lekérése

A kódolás befejezését követően a következő lépés az, hogy a videó a kimeneti adategység elérhető az ügyfelek számára a lejátszást. Ehhez először létre kell hoznia egy Streamelési lokátor. Ezután hozhat létre streamelési URL-címek, amellyel az ügyfelek.

### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

A következőhöz hasonló választ kap:

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

### <a name="get-streaming-locator-paths"></a>Első streamelési lokátor elérési utak

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

A következőhöz hasonló választ kap:

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

Másolás a HTTP live streaming (HLS) elérési útja. Ebben az esetben van `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)`.

## <a name="build-the-url"></a>Az URL-cím létrehozása 

### <a name="get-the-streaming-endpoint-host-name"></a>A streamelési végpontot gazdagép nevének lekérése

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```
Másolás a `hostName` értéket. Ebben az esetben van `amsaccount-usw22.streaming.media.azure.net`.

### <a name="assemble-the-url"></a>Állítsa össze az URL-cím

"https://" + &lt;hostName értékét&gt; + &lt;Hls elérési útjának értéke&gt;

Például:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Tesztelje az Azure Media Player használatával

> [!NOTE]
> Ha egy HTTPS-hely üzemelteti, ügyeljen arra, hogy indítsa el a "https" URL-CÍMÉT.

1. Nyisson meg egy webböngészőt, majd [ https://aka.ms/azuremediaplayer/ ](https://aka.ms/azuremediaplayer/).
2. Az a **URL-cím** mezőbe illessze be az előző szakaszban létrehozott URL-CÍMÉT. Beillesztheti az URL-címet a HLS, Dash vagy Smooth formátumban. Az Azure Media Player automatikusan használja, egy megfelelő streamelési protokoll az eszközre lejátszás céljából.
3. Válassza ki **Player frissítése**.

>[!NOTE]
>Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a az erőforrások az erőforráscsoportban, beleértve a Media Services és a storage-fiókok ebben a rövid útmutatóban létrehozott törölje az erőforráscsoportot.

Futtassa a CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Lásd még

Lásd: [hibakódok feladat](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

> [CLI-minták](cli-samples.md)
