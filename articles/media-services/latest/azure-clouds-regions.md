---
title: Felhők és régiók, ahol az Azure Media Services v3 elérhető
description: Ez a cikk az Azure-felhőkről és azokról a régiókról szól, amelyekben az Azure Media Services v3 érhető el.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 1257bf4dfb0d5b2c4995cac760290f97293a0c0f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382970"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Felhők és régiók, amelyekben az Azure Media Services v3 létezik

Az Azure Media Services v3 az Azure Resource Manager manifesztén keresztül érhető el a globális Azure, az Azure Government, az Azure Germany, az Azure China 21Vianet területén. Azonban nem minden Media Services-funkció érhető el az összes Azure-felhőben. Ez a dokumentum ismerteti a Media Services v3 fő összetevőinek elérhetőségét.

## <a name="feature-availability-in-azure-clouds"></a>A szolgáltatás elérhetősége az Azure-felhőkben

| Szolgáltatás|Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [StandardEncoderPreset](encoding-concept.md) | Elérhető | Elérhető | Elérhető | Elérhető |
| [Élő események](live-streaming-overview.md) | Elérhető | Elérhető | Elérhető | Elérhető |
| [Streamelésvégpontok](streaming-endpoint-concept.md) | Elérhető | Elérhető | Elérhető | Elérhető |

## <a name="regionsgeographieslocations"></a>Régiók/földrajzi területek/helyszínek

[Azok a régiók, amelyekben az Azure Media Services szolgáltatás telepítve van](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Régiókód neve

Ha meg kell adnia a **hely** paramétert, meg kell adnia a régiókód nevét **helyértékként.** Annak a régiónak a kódnevének lekérnie, amelyben a fiók található, és amelyhez a hívást át kell irányítani, futtathatja a következő sort az [Azure CLI-ben](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```azurecli-interactive
az account list-locations
```

Miután futtatta a fenti sort, megjelenik az összes Azure-régió listája. Keresse meg az Azure-régióban, amely rendelkezik a *displayName,* amely a keresett, és használja a *név* értékét a **hely** paraméter.

Például az Azure régió ban USA 2 nyugati régiója (alább látható), a **helyparaméterhez** a "westus2" lesz.

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

A következő végpontok fontos tudni, ha csatlakozik a Media Services-fiókok különböző nemzeti Azure-felhők.

### <a name="global-azure"></a>Globális Azure

|Végpontok||
| --- | --- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Hitelesítés | `https://login.microsoftonline.com/` |
| Token közönség | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Végpontok||
| --- | --- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Hitelesítés | `https://login.microsoftonline.us/` |
| Token közönség | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| Végpontok ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Hitelesítés | `https://login.microsoftonline.de/` |
| Token közönség | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Végpontok||
| --- | --- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Hitelesítés | `https://login.chinacloudapi.cn/` |
| Token közönség |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Lásd még

* [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure földrajzi területek](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Az Azure-beli helyszínek](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>További lépések

[A Media Services 3-as v3-as ügyének áttekintése](media-services-overview.md)
