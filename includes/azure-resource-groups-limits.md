---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: f0ab7c2efc499c43245680e56a7e5ca1b5261397
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748779"
---
| Erőforrás | Korlát |
| --- | --- |
| Erőforrások [erőforrás-csoportonként](../articles/azure-resource-manager/management/overview.md#resource-groups) | Az erőforrások nem korlátozódnak az erőforráscsoporthoz. Ehelyett egy erőforráscsoport erőforrástípus van korlátozva. Lásd a következő sort. |
| Erőforrások erőforrás-csoportonként, erőforrás-típus szerint |800 – egyes erőforrástípusok túllépik a 800 korlátot. Tekintse [meg az erőforrásokat, amelyek erőforráscsoport esetén nem korlátozódnak 800 példányra](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Üzemelő példányok erőforráscsoport szerint az üzembe helyezési előzményekben |800<sup>1</sup> |
| Erőforrások üzembe helyezése |800 |
| Felügyeleti zárolások egyedi [hatókör](../articles/azure-resource-manager/management/overview.md#understand-scope) szerint  |20 |
| Címkék száma erőforrás vagy erőforráscsoport szerint |50 |
| Címke kulcsának hossza |512 |
| Címke értékének hossza |256 |

<sup>1</sup> A 2020. júniustól kezdődően az üzembe helyezéseket a rendszer automatikusan törli az előzményekből a korlát közelében. Az üzembe helyezési előzményekből származó bejegyzések törlése nem befolyásolja az üzembe helyezett erőforrásokat. További információ: [automatikus törlések az üzembe helyezési előzményekből](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Sablonok korlátai

| Érték | Korlát |
| --- | --- |
| Paraméterek |256 |
| Változók |256 |
| Erőforrások (beleértve a példányszámot is) |800 |
| Kimenetek |64 |
| Sablon kifejezése |24 576 karakter |
| Exportált sablonok erőforrásai |200 |
| Sablon mérete |4 MB |
| Paraméter fájlmérete |64 KB |

Az egyes sablonokra vonatkozó korlátokat egy beágyazott sablon használatával lehet meghaladni. További információ: a [csatolt sablonok használata az Azure-erőforrások telepítésekor](../articles/azure-resource-manager/templates/linked-templates.md). A paraméterek, változók és kimenetek számának csökkentése érdekében több értéket is egyesítheti egy objektumban. További információ: [objektumok paraméterként](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
