---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: dce9b4ffa05b48fc1789859328b221ac1a0f3a27
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594436"
---
Ha hozzáférést kap a használati űrlap-felismerőhöz, a rendszer egy üdvözlő e-mailt fog kapni, amely több hivatkozást és erőforrást is tartalmaz. Az üzenetben található "Azure Portal" hivatkozásra kattintva nyissa meg a Azure Portal, és hozzon létre egy űrlap-felismerő erőforrást. A **Létrehozás** ablaktáblán adja meg a következő adatokat:

|    |    |
|--|--|
| **Name** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki azt az Azure-előfizetést, amelyhez hozzáférés van megadva. |
| **Location** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
| **Tarifacsomag** | Az erőforrás díja a kiválasztott árképzési szinttől és a használattól függ. További információt az API díjszabását ismertető [](https://azure.microsoft.com/pricing/details/cognitive-services/)témakörben talál.
| **Erőforráscsoport** | Az az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , amely az erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

> [!IMPORTANT]
> Általában amikor a Azure Portal egy kognitív szolgáltatásbeli erőforrást hoz létre, lehetősége van egy több szolgáltatásból álló előfizetési kulcs (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcs (csak egy adott kognitív szolgáltatás használatával) létrehozására. Mivel azonban az űrlap-felismerő egy előzetes kiadás, a többszolgáltatásos előfizetés nem tartalmazza, és nem hozhatja létre az egyszolgáltatásos előfizetést, kivéve, ha az üdvözlő e-mailben megadott hivatkozást használja.

Ha az űrlap-felismerő erőforrás üzembe helyezése befejeződött, keresse meg és válassza ki a portál **minden erőforrás** listájából. Ezután válassza a **kulcsok** fület az előfizetési kulcsok megtekintéséhez. Bármelyik kulcs megadja az alkalmazásnak az erőforráshoz való hozzáférést. Másolja az 1. **kulcs**értékét.