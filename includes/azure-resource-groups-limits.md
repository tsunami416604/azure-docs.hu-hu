---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334690"
---
| Erőforrás | Korlát |
| --- | --- |
| Erőforrások [erőforráscsoportonként](../articles/azure-resource-manager/management/overview.md#resource-groups) | Az erőforrásokat erőforráscsoport nem korlátozza. Ehelyett egy erőforráscsoport erőforrástípusa korlátozza őket. Lásd a következő sort. |
| Erőforrások erőforráscsoportonként, erőforrástípusonként |800 – Egyes erőforrástípusok meghaladhatják a 800-as korlátot. [Az Erőforrások nem korlátozódik 800 példányra erőforráscsoportonként](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Központi telepítések erőforráscsoportonként a telepítési előzményekben |800<sup>1</sup> |
| Erőforrások telepítésenként |800 |
| Felügyeleti zárolások egyedi hatókörenként |20 |
| Címkék száma erőforrásonként vagy erőforráscsoportonként |50 |
| Címkekulcs hossza |512 |
| Címke értékének hossza |256 |

<sup>1 1</sup> Ha eléri a 800 központi telepítések erőforráscsoportonként, törölje a központi telepítések az előzményekből, amelyek már nincs szükség. Egy bejegyzés törlése a telepítési előzmények ből nincs hatással az üzembe helyezett erőforrásokra. További információt a [Hiba feloldása 800-nál nagyobb üzemidő esetén](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)című témakörben talál.

#### <a name="template-limits"></a>Sablonkorlátok

| Érték | Korlát |
| --- | --- |
| Paraméterek |256 |
| Változók |256 |
| Erőforrások (beleértve a másolások számát) |800 |
| Kimenetek |64 |
| Sablonkifejezés |24 576 karakter |
| Exportált sablonok erőforrásai |200 |
| Sablon mérete |4 MB |
| Paraméterfájl mérete |64 KB |

Egyes sablonkorlátokat beágyazott sablon használatával túlléphet. További információt a [Csatolt sablonok használata Azure-erőforrások üzembe helyezésekor](../articles/azure-resource-manager/templates/linked-templates.md)című témakörben talál. A paraméterek, változók vagy kimenetek számának csökkentése érdekében több értéket egyesíthet egy objektumban. További információ: [Objects as parameters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
