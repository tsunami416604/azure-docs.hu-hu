---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334690"
---
| Erőforrás | Korlát |
| --- | --- |
| Erőforrások [erőforrás-csoportonként](../articles/azure-resource-manager/management/overview.md#resource-groups) | Az erőforrások nem korlátozódnak az erőforráscsoporthoz. Ehelyett egy erőforráscsoport erőforrástípus van korlátozva. Lásd a következő sort. |
| Erőforrások erőforrás-csoportonként, erőforrás-típus szerint |800 – egyes erőforrástípusok túllépik a 800 korlátot. Tekintse [meg az erőforrásokat, amelyek erőforráscsoport esetén nem korlátozódnak 800 példányra](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Üzemelő példányok erőforráscsoport szerint az üzembe helyezési előzményekben |800<sup>1</sup> |
| Erőforrások üzembe helyezése |800 |
| Felügyeleti zárolások egyedi hatókör szerint |20 |
| Címkék száma erőforrás vagy erőforráscsoport szerint |50 |
| Címke kulcsának hossza |512 |
| Címke értékének hossza |256 |

<sup>1</sup> Ha eléri a 800-es üzemelő példányok számát egy erőforráscsoport esetében, törölje a már nem szükséges előzményekből származó központi telepítéseket. Az üzembe helyezési előzményekből származó bejegyzések törlése nem befolyásolja az üzembe helyezett erőforrásokat. További információ: a [hiba elhárítása, ha a központi telepítés száma meghaladja a 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)-at.

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
