---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78205821"
---
Nyissa meg a Azure Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=", és hozzon létre egy új" target="_blank">űrlap-felismerő erőforrást egy új <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>űrlap-felismerő erőforrás létrehozásával. A **Létrehozás** ablaktáblán adja meg a következő adatokat:

|    |    |
|--|--|
| **Név** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyhez hozzáférés van megadva. |
| **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Díjszabási csomag** | Az erőforrás díja a kiválasztott árképzési szinttől és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
| **Erőforráscsoport** | Az az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , amely az erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

> [!IMPORTANT]
> Általában amikor a Azure Portal egy kognitív szolgáltatásbeli erőforrást hoz létre, lehetősége van egy több szolgáltatásból álló előfizetési kulcs (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcs (csak egy adott kognitív szolgáltatás használatával) létrehozására. Mivel azonban az űrlap-felismerő egy előzetes kiadás, a többszolgáltatásos előfizetés nem tartalmazza, és nem hozhatja létre az egyszolgáltatásos előfizetést, kivéve, ha az üdvözlő e-mailben megadott hivatkozást használja.

Ha az űrlap-felismerő erőforrás üzembe helyezése befejeződött, keresse meg és válassza ki a portál **minden erőforrás** listájából. Ezután válassza a **gyors üzembe helyezés** lapot az előfizetési adatai megtekintéséhez. Mentse a **Key1** és a **végpont** értékeit egy ideiglenes helyre. Ezeket a következő lépésekben fogja használni.
