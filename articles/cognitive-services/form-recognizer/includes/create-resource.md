---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: d322aa27c7c9dcf4ad365ec22f8074aeacc85d3b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502842"
---
Használja az űrlap felismerő hozzáférést kapnak, amikor kap egy üdvözlő e-mailt, több hivatkozásokat és forrásanyagokat. Ebben az üzenetben az "Az Azure portal" hivatkozás segítségével nyissa meg az Azure Portalt, és hozzon létre egy űrlap felismerő erőforrást. Az a **létrehozás** panelen adja meg a következő információkat:

|    |    |
|--|--|
| **Name** | Az erőforrás egy leíró nevet. Azt javasoljuk egy leíró nevet, például *MyNameFormRecognizer*. |
| **Előfizetés** | Válassza ki az Azure-előfizetést, amelynek hozzáférési engedélyt kapott. |
| **Location** | A cognitive Services-példány helye. Különböző helyeken a késés bevezetni, de semmilyen hatást nem futásidejű rendelkezésre állását az erőforrást. |
| **Tarifacsomag** | Az erőforrás költségének, válassza ki a tarifacsomagot, és a használat függ. További információkért tekintse meg az API-t [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).
| **Erőforráscsoport** | A [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , amely tartalmazza az erőforrás. Hozzon létre egy új csoportot, vagy adja hozzá egy meglévő csoportot. |

> [!IMPORTANT]
> Általában a Cognitive Services-erőforrás létrehozásakor az Azure Portalon lehetősége van egy több szolgáltatásos előfizetési kulcsot (több, a cognitive services futásideje) vagy egy (csak egy adott cognitive szolgáltatáshoz használt) – olyan egyetlen szolgáltatást előfizetési kulcs létrehozása. Azonban mivel az űrlap felismerő előzetes kiadás, több szolgáltatásos az előfizetésben nem szerepel, és a egyszolgáltatásos előfizetés nem hozható létre, ha nem használ az üdvözlő e-mailt a megadott hivatkozás.

Befejeztével az űrlap felismerő erőforrás üzembe helyezéséhez keresse meg és válassza ki a **összes erőforrás** listájához a portálon. Válassza ki a **kulcsok** fülre kattintva megtekintheti az előfizetési kulcsok. Egyiket sem az alkalmazás hozzáférést biztosít az erőforráshoz. Másolja az értéket a **kulcs 1**. A következő szakaszban be fogja használni.