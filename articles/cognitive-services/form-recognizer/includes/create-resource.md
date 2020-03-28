---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: 33624ab800bd1155b52efbc05f317122a99bb479
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205821"
---
Nyissa meg az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Azure Portalt, és" target="_blank">hozzon létre egy <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>új űrlapfelismerő erőforrást, és hozzon létre egy új űrlapfelismerő erőforrást. A **Létrehozás** ablaktáblában adja meg a következő információkat:

|    |    |
|--|--|
| **Név** | Az erőforrás leíró neve. Javasoljuk, hogy használjon leíró nevet, például *A MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki a hozzáférést kapott Azure-előfizetést. |
| **Helyen** | A kognitív szolgáltatáspéldány helye. A különböző helyek késést okozhatnak, de nincsenek hatással az erőforrás futásidejű rendelkezésre állására. |
| **Tarifacsomag** | Az erőforrás költsége a választott tarifacsomagtól és a használattól függ. További információt az API [díjszabásának részleteicímű témakörben talál.](https://azure.microsoft.com/pricing/details/cognitive-services/)
| **Erőforráscsoport** | Az [Azure erőforráscsoport,](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) amely tartalmazza az erőforrást. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz. |

> [!IMPORTANT]
> Általában, ha létrehoz egy Cognitive Service-erőforrást az Azure Portalon, lehetősége van egy többszolgáltatásos előfizetési kulcsot (több kognitív szolgáltatásban használt) vagy egy egyszolgáltatásos előfizetési kulcsot (csak egy adott kognitív szolgáltatással használható). Mivel azonban a Form Recognizer egy előzetes verziójú kiadás, nem szerepel a többszolgáltatásos előfizetésben, és nem hozhatja létre az egyszolgáltatásos előfizetést, hacsak nem használja az üdvözlő e-mailben megadott hivatkozást.

Amikor az űrlapfelismerő erőforrás telepítése befejeződik, keresse meg és válassza ki a portál **Minden erőforrás** listájából. Ezután válassza a **Rövid kezdés** lapot az előfizetési adatok megtekintéséhez. Mentse a **Key1** és a **Végpont** értékeit egy ideiglenes helyre. Ezeket a következő lépésekben fogja használni.
