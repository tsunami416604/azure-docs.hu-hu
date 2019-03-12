---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a528fad10144ec733a3db5340ef12dee5ce5411c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553545"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Erőforrások száma [erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), erőforrás-típusonként |800 |Erőforrás típusa szerint változó |
| Az üzembe helyezési előzmények erőforráscsoportonként központi telepítések |800 |800 |
| Erőforrások száma üzemelő példányonként |800 |800 |
| Felügyeleti zárolások egyedi hatókör szerint |20 |20 |
| Egy erőforrás vagy erőforráscsoport címkék száma |15 |15 |
| Címke kulcs hossza |512 |512 |
| Címke hossza |256 |256 |


#### <a name="template-limits"></a>Sablon korlátok

| Érték | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Paraméterek |256 |256 |
| Változók |256 |256 |
| Erőforrások, amely tartalmazza a példányszám |800 |800 |
| Kimenetek |64 |64 |
| Kifejezés |24,576 karakter |24,576 karakter |
| Az exportált sablonok erőforrások |200 |200 | 
| Sablon mérete |1 MB |1 MB |
| Fájl mérete paraméter |64 KB |64 KB |

Néhány sablon korlát beágyazott sablonok segítségével is lehet. További információkért lásd: [kapcsolt sablonok használata Azure-erőforrások központi telepítésekor](../articles/azure-resource-manager/resource-group-linked-templates.md). A paraméterek, a változók és a kimenetek számának csökkentése, több értéket is egyesítendő objektum. További információkért lásd: [paraméterekként objektumok](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).

Ha eléri a korlátot, az adott erőforráscsoport esetében 800 központi telepítések, törölje a központi telepítések az előzményekben tekintheti át, hogy már nincs rá szükség. Bejegyzések törölheti az előzményekből [az csoport központi telepítésének törlése](/cli/azure/group/deployment) az Azure CLI-hez. Használhatja még [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) a PowerShellben. Az üzembe helyezési előzmények bejegyzés törlése nem befolyásolja az üzembe helyezési erőforrásokkal. 