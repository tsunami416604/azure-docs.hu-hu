---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c1a52e1bc2dde74289cb270fcae832be24de5a06
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592423"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Erőforrások [erőforrás-csoportonként](../articles/azure-resource-manager/management/overview.md#resource-groups) | N/A | Az erőforrások nem korlátozódnak az erőforráscsoporthoz. Ehelyett egy erőforráscsoport erőforrástípus van korlátozva. Lásd a következő sort. | 
| Erőforrások erőforrás-csoportonként, erőforrás-típus szerint |800 |Egyes erőforrástípusok túllépik a 800 korlátot. Tekintse [meg az erőforrásokat, amelyek erőforráscsoport esetén nem korlátozódnak 800 példányra](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Üzemelő példányok erőforráscsoport szerint az üzembe helyezési előzményekben |800<sup>1</sup> |800 |
| Erőforrások üzembe helyezése |800 |800 |
| Felügyeleti zárolások egyedi hatókör szerint |20 |20 |
| Címkék száma erőforrás vagy erőforráscsoport szerint |50 |50 |
| Címke kulcsának hossza |512 |512 |
| Címke értékének hossza |256 |256 |

<sup>1</sup> Ha eléri a 800-es üzemelő példányok számát egy erőforráscsoport esetében, törölje a már nem szükséges előzményekből származó központi telepítéseket. Az üzembe helyezési előzményekből származó bejegyzések törlése nem befolyásolja az üzembe helyezett erőforrásokat. További információ: a [hiba elhárítása, ha a központi telepítés száma meghaladja a 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)-at.

#### <a name="template-limits"></a>Sablonok korlátai

| Érték | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Paraméterek |256 |256 |
| Változók |256 |256 |
| Erőforrások (beleértve a példányszámot is) |800 |800 |
| Kimenetek |64 |64 |
| Sablon kifejezése |24 576 karakter |24 576 karakter |
| Exportált sablonok erőforrásai |200 |200 | 
| Sablon mérete |4 MB |4 MB |
| Paraméter fájlmérete |64 KB |64 KB |

Az egyes sablonokra vonatkozó korlátokat egy beágyazott sablon használatával lehet meghaladni. További információ: a [csatolt sablonok használata az Azure-erőforrások telepítésekor](../articles/azure-resource-manager/templates/linked-templates.md). A paraméterek, változók és kimenetek számának csökkentése érdekében több értéket is egyesítheti egy objektumban. További információ: [objektumok paraméterként](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
