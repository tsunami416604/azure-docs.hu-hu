---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b4338560d515289d3e9aedfa716ec7c3ace51131
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392265"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Erőforrások erőforrás [-csoportonként](../articles/azure-resource-manager/management/overview.md#resource-groups), erőforrás-típus szerint |800 |Egyes erőforrástípusok túllépik a 800 korlátot. Tekintse [meg az erőforrásokat, amelyek erőforráscsoport esetén nem korlátozódnak 800 példányra](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Üzemelő példányok erőforráscsoport szerint az üzembe helyezési előzményekben |800<sup>1</sup> |800 |
| Erőforrások üzembe helyezése |800 |800 |
| Felügyeleti zárolások egyedi hatókör szerint |20 |20 |
| Címkék száma erőforrás vagy erőforráscsoport szerint |50 |50 |
| Címke kulcsának hossza |512 |512 |
| Címke értékének hossza |256 |256 |

<sup>1</sup> Ha eléri a 800-es üzemelő példányok számát egy erőforráscsoport esetében, törölje a már nem szükséges előzményekből származó központi telepítéseket. Az üzembe helyezési előzményekből származó bejegyzések törlése nem befolyásolja az üzembe helyezett erőforrásokat. További információ: a [hiba elhárítása, ha a központi telepítés száma meghaladja a 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)-at.

#### <a name="template-limits"></a>Sablonok korlátai

| Value (Díj) | Alapértelmezett korlát | Felső korlát |
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
