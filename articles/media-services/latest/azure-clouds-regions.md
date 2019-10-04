---
title: Magánfelhők és régióban, mely az Azure Media Services v3 elérhető |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure-felhők régiókban mely az Azure Media Services v3 érhető el.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7b2691f543cf38a56eefb1e8521169aeccbf3221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409287"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>Magánfelhők és régiók, mely az Azure Media Services v3 létezik

Az Azure Media Services v3-as globális Azure-ban, Azure Government, Azure Germany, az Azure China 21Vianet az Azure Resource Manager-jegyzékfájl keresztül érhető el. Azonban nem minden Media Services-funkciók érhetők el az összes az Azure-felhőben. Ez a dokumentum lehetőségek a Media Services v3 fő összetevőit vázolja fel.

## <a name="feature-availability-in-azure-clouds"></a>A szolgáltatás rendelkezésre állása az Azure-felhők

| Funkció|Globális Azure-régiók | Azure Government|Azure Germany|Azure China 21Vianet|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  Elérhető | Nem érhető el | Nem érhető el | Nem érhető el |
| [StandardEncoderPreset](encoding-concept.md) | Elérhető | Elérhető | Elérhető | Elérhető |
| [LiveEvents](live-streaming-overview.md) | Elérhető | Elérhető | Elérhető | Elérhető |
| [Streamvégpontok](streaming-endpoint-concept.md) | Elérhető | Elérhető | Elérhető | Elérhető |

## <a name="regionsgeographieslocations"></a>Régiók és földrajzi területeken/helyek

[Régió, amelyben az Azure Media Services-szolgáltatás üzembe helyezése](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>Régió neve 

Amikor meg kell adnia a **hely** paramétert meg kell adnia a régió neve, a **hely** érték. A régió, amely a fiók, és hogy a hívás kell átirányítani a kód nevének futtassa a következő sort [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

```bash
az account list-locations
```

Egyszer futtatja a sor a fent látható az összes Azure-régiók listájának beolvasása. Keresse meg az Azure-régióban, amely rendelkezik a *displayName* keres, és használja a *neve* értékét a **hely** paraméter.

Ha például az Azure-régiót (alább látható) 2. nyugati RÉGIÓJA, az használandó "westus2" a **hely** paraméter.

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

Az alábbi végpontokat is fontos tudni, hogy különböző országos Azure-felhők a Media Services-fiókok való csatlakozáskor.

### <a name="global-azure"></a>Globális Azure

|Végpontok ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| Hitelesítés | `https://login.microsoftonline.com/` | 
| Token célrendszere | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|Végpontok||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Hitelesítés | `https://login.microsoftonline.us/` | 
| Token célrendszere | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| Végpontok ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Hitelesítés | `https://login.microsoftonline.de/` |
| Token célrendszere | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure China 21Vianet

|Végpontok||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Hitelesítés | `https://login.chinacloudapi.cn/` |
| Token célrendszere |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Lásd még

* [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure-régiócsoportok](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-helyek](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>További lépések

[Media Services v3 áttekintése](media-services-overview.md)
