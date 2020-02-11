---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 0a4faad8bfe92a5389e0ee440aa3ccc404535955
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118102"
---
Nyissa meg az Azure Portalt, és [hozzon létre egy új űrlap-felismerő erőforrást](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer). A **Létrehozás** ablaktáblán adja meg a következő adatokat:

|    |    |
|--|--|
| **Name (Név)** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyhez hozzáférés van megadva. |
| **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Tarifacsomag** | Az erőforrás díja a kiválasztott árképzési szinttől és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
| **Erőforráscsoport** | Az az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) , amely az erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

> [!IMPORTANT]
> Általában amikor a Azure Portal egy kognitív szolgáltatásbeli erőforrást hoz létre, lehetősége van egy több szolgáltatásból álló előfizetési kulcs (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcs (csak egy adott kognitív szolgáltatás használatával) létrehozására. Mivel azonban az űrlap-felismerő egy előzetes kiadás, a többszolgáltatásos előfizetés nem tartalmazza, és nem hozhatja létre az egyszolgáltatásos előfizetést, kivéve, ha az üdvözlő e-mailben megadott hivatkozást használja.

Ha az űrlap-felismerő erőforrás üzembe helyezése befejeződött, keresse meg és válassza ki a portál **minden erőforrás** listájából. Ezután válassza a **gyors üzembe helyezés** lapot az előfizetési adatai megtekintéséhez. Mentse a **Key1** és a **végpont** értékeit egy ideiglenes helyre. Ezeket a következő lépésekben fogja használni.