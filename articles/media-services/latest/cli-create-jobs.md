---
title: Azure CLI-példaszkript – Feladat létrehozása és elküldése | Microsoft Docs
description: A jelen témakörben lévő Azure CLI-szkript bemutatja, hogyan tud feladatot küldeni egy egyszerű kódoló Átalakításnak HTTPS URL-cím használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: e4f2d4f0e7dd9380ba708d84dfe7588043fd4a68
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236701"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI-példa: Egy feladat létrehozása és elküldése

A Media Services v3-ban a videófeldolgozási feladatok indításakor meg kell adnia a Media Services számára a bemeneti videó helyét. A beállításokat egyik HTTPS URL-címet adja meg a feladat bemeneti (ahogyan az ebben a cikkben látható). 

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Példaszkript

Futtatásakor `az ams job start`, a feladat kimenetének állíthat be egy címkét. A címke később azonosíthatja, hogy a kimeneti adategység Mi az a használható. 

- A címkét rendel egy értéket, ha "--kimeneti-objektumok," assetname = label "
- A címke nem rendel egy értéket, ha "--kimeneti-objektumok," assetname = ".
  Figyelje meg, hogy, adja hozzá a "=" való a `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
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

## <a name="next-steps"></a>További lépések

[Media Services áttekintése](media-services-overview.md)