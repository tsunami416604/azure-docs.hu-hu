---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: 8bd16378e9c82a011309c12cf241b59d03405a77
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60012523"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Erőforrások száma [erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), erőforrás-típusonként |800 |Erőforrás típusa szerint változó |
| Az üzembe helyezési előzmények erőforráscsoportonként központi telepítések |800<sup>1</sup> |800 |
| Erőforrások száma üzemelő példányonként |800 |800 |
| Felügyeleti zárolások egyedi hatókör szerint |20 |20 |
| Egy erőforrás vagy erőforráscsoport címkék száma |15 |15 |
| Címke kulcs hossza |512 |512 |
| Címke hossza |256 |256 |

<sup>1</sup>eléri a korlátot, az adott erőforráscsoport esetében 800 központi telepítések, ha törli a központi telepítések az előzményekben tekintheti át, hogy már nincs rá szükség. Az üzembe helyezési előzmények bejegyzés törlése nem befolyásolja az üzembe helyezett erőforrásokat. Bejegyzések törölheti az előzményekből [az csoport központi telepítésének törlése](/cli/azure/group/deployment) Azure CLI-hez, vagy [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) a PowerShellben.  Egy PowerShell-parancsfájlt, amely automatizálja a folyamatos integráció és folyamatos készregyártás (CI/CD) a forgatókönyvben szereplő telepítések törlése lásd [remove-deployments.ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f).

#### <a name="template-limits"></a>Sablon korlátok

| Value | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Paraméterek |256 |256 |
| Változók |256 |256 |
| Erőforrások (beleértve a másolási száma) |800 |800 |
| Kimenetek |64 |64 |
| Kifejezés |24,576 karakter |24,576 karakter |
| Az exportált sablonok erőforrások |200 |200 | 
| Sablon mérete |1 MB |1 MB |
| Fájl mérete paraméter |64 KB |64 KB |

Néhány sablon korlát beágyazott sablonok segítségével is lehet. További információkért lásd: [kapcsolt sablonok használata Azure-erőforrások központi telepítésekor](../articles/azure-resource-manager/resource-group-linked-templates.md). A paraméterek, a változók és a kimenetek számának csökkentése, több értéket is egyesítendő objektum. További információkért lásd: [paraméterekként objektumok](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
