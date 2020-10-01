---
title: Felhők és régiók, amelyekben Azure Media Services v3 elérhető
description: Ez a cikk olyan Azure-felhőket és-régiókat mutat be, amelyekben Azure Media Services v3 elérhető.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 9c54482dc188141c4fc697bd43628b81e0ddc29f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91597119"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Felhők és régiók, amelyekben Azure Media Services v3 létezik

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services v3 a globális Azure-ban, a Azure Government, az Azure Germany és az Azure China 21Vianet Azure Resource Manager-jegyzékfájlján keresztül érhető el. Azonban nem minden Media Services funkció érhető el az összes Azure-felhőben. Ez a dokumentum a fő Media Services v3-összetevők elérhetőségeit ismerteti.

## <a name="feature-availability-in-azure-clouds"></a>Funkciók elérhetősége az Azure-felhőkben

| Funkció|Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [StandardEncoderPreset](encoding-concept.md) | Elérhető | Elérhető | Elérhető | Elérhető |
| [LiveEvents](live-streaming-overview.md) | Elérhető | Elérhető | Elérhető | Elérhető |
| [StreamingEndpoints](streaming-endpoint-concept.md) | Elérhető | Elérhető | Elérhető | Elérhető |

## <a name="feature-availability-in-preview"></a>A szolgáltatás rendelkezésre állása előzetes verzióban

A [LiveTranscription](live-transcription.md) a következő régiókban érhető el:

- Délkelet-Ázsia
- Nyugat-Európa
- Észak-Európa
- USA keleti régiója
- Az USA középső régiója
- USA déli középső régiója
- USA 2. nyugati régiója
- Dél-Brazília

## <a name="regionsgeographieslocations"></a>Régiók/földrajzi területek/helyek

[A Azure Media Services szolgáltatást üzembe helyező régiók](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Régiókód neve

Ha meg kell adnia a **Location** paramétert, meg kell adnia a területi kód nevét a **hely** értékeként. Ha szeretné lekérni annak a régiónak a nevét, amelyben a fiókja található, és a hívást át kell irányítani a szolgáltatásba, a következő sort futtathatja az [Azure CLI](/cli/azure/?view=azure-cli-latest) -ben.

```azurecli-interactive
az account list-locations
```

Miután futtatta a fenti sort, megjelenik az összes Azure-régió listája. Navigáljon ahhoz az Azure-régióhoz, amelynek a *DisplayName* a nevét keresi, és használja a *Name* értékét a **Location** paraméterhez.

Az USA 2. nyugati régiójában (az alábbi ábrán látható) például az "westus2" kifejezést fogja használni a **Location** paraméterhez.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Végpontok  

A következő végpontokat fontos tudni, hogy mikor csatlakozhat Media Services-fiókokhoz a különböző nemzeti Azure-felhőkből.

### <a name="global-azure"></a>Globális Azure

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Hitelesítés | `https://login.microsoftonline.com/` |
| Jogkivonat célközönsége | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Hitelesítés | `https://login.microsoftonline.us/` |
| Jogkivonat célközönsége | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Germany

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Hitelesítés | `https://login.microsoftonline.de/` |
| Jogkivonat célközönsége | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

| Szolgáltatás | Végpont |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Hitelesítés | `https://login.chinacloudapi.cn/` |
| Jogkivonat célközönsége |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>További információ

* [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure földrajzi területek](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-beli helyszínek](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>További lépések

[Media Services v3 – áttekintés](media-services-overview.md)
