---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 897f2b728dc068b09849d4f48f899b8630a87a51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003998"
---
Nyissa meg a Azure Portal, és <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" hozzon létre egy új űrlap-felismerő erőforrást " target="_blank"> egy új űrlap-felismerő erőforrás létrehozásával <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . A **Létrehozás** ablaktáblán adja meg a következő adatokat:

|    |    |
|--|--|
| **Név** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyhez hozzáférés van megadva. |
| **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Tarifacsomag** | Az erőforrás díja a kiválasztott árképzési szinttől és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
| **Erőforráscsoport** | Az az [Azure-erőforráscsoport](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , amely az erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

> [!NOTE]
> Általában amikor a Azure Portal egy kognitív szolgáltatásbeli erőforrást hoz létre, lehetősége van egy több szolgáltatásból álló előfizetési kulcs (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcs (csak egy adott kognitív szolgáltatás használatával) létrehozására. A többszolgáltatásos előfizetés azonban jelenleg nem tartalmaz űrlap-felismerőt.

Ha az űrlap-felismerő erőforrás üzembe helyezése befejeződött, keresse meg és válassza ki a portál **minden erőforrás** listájából. A kulcs és a végpont az erőforrás kulcs és végpont lapján, az erőforrás-kezelés területen található. A továbbítás előtt mentse mindkettőt egy ideiglenes helyre.