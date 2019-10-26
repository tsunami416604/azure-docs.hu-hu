---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 14740e780f6143f19f843891c16b25df761d0b4f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931253"
---
Ha hozzáférést kap a használati űrlap-felismerőhöz, a rendszer egy üdvözlő e-mailt fog kapni, amely több hivatkozást és erőforrást is tartalmaz. Az üzenetben található "Azure Portal" hivatkozásra kattintva nyissa meg a Azure Portal, és hozzon létre egy űrlap-felismerő erőforrást. A **Létrehozás** ablaktáblán adja meg a következő adatokat:

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