---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: cedd302453e75c037f17cc47d8e00a06d3f3a8cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83996795"
---
Nyissa meg a Azure Portal, és <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" hozzon létre egy új űrlap-felismerő erőforrást " target="_blank"> egy új űrlap-felismerő erőforrás létrehozásával <span class="docon docon-navigate-external x-hidden-focus"></span> </a> . A **Létrehozás** ablaktáblán adja meg a következő adatokat:

|    |    |
|--|--|
| **Name (Név)** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyhez hozzáférés van megadva. |
| **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Díjszabási csomag** | Az erőforrás díja a kiválasztott árképzési szinttől és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
| **Erőforráscsoport** | Az az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , amely az erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

> [!NOTE]
> Általában amikor a Azure Portal egy kognitív szolgáltatásbeli erőforrást hoz létre, lehetősége van egy több szolgáltatásból álló előfizetési kulcs (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcs (csak egy adott kognitív szolgáltatás használatával) létrehozására. Mivel azonban az űrlap-felismerő egy előzetes kiadás, a többszolgáltatásos előfizetés nem tartalmazza, és nem hozhatja létre az egyszolgáltatásos előfizetést, kivéve, ha az üdvözlő e-mailben megadott hivatkozást használja.

Ha az űrlap-felismerő erőforrás üzembe helyezése befejeződött, keresse meg és válassza ki a portál **minden erőforrás** listájából. A kulcs és a végpont az erőforrás kulcs és végpont lapján, az erőforrás-kezelés területen található. A továbbítás előtt mentse mindkettőt egy ideiglenes helyre.
