---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133718"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| [Erőforráscsoportok](../articles/azure-resource-manager/management/overview.md) /előfizetés |980 |980 |
| Azure Resource Manager API-kérelem mérete |4 194 304 bájt. |4 194 304 bájt. |
| Címkék/előfizetés<sup>1</sup> |Korlátlan. |Korlátlan. |
| Egyedi címkézési számítások az<sup>1</sup> . előfizetéshez | 10,000 | 10,000 |
| [Előfizetés szintű központi telepítések](../articles/azure-resource-manager/templates/deploy-to-subscription.md) régiónként | 800<sup>2</sup> | 800 |
| Előfizetések száma Azure Active Directory bérlőn | Korlátlan. | Korlátlan. |
| A [rendszergazdák](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) /előfizetés |Korlátlan. |Korlátlan. |

<sup>1</sup> Az előfizetések korlátlan számú címkét igényelhetnek. Az erőforrás-vagy erőforráscsoport-címkék száma 50-ra van korlátozva. A Resource Manager csak akkor adja vissza az előfizetésben szereplő [egyedi címke nevét és értékeit](/rest/api/resources/tags) , ha a címkék száma 10 000 vagy kevesebb. Továbbra is megtalálhatja az erőforrást címke szerint, ha a szám meghaladja a 10 000 értéket.  

<sup>2</sup> Ha eléri a 800-es üzemelő példányok korlátját, törölje a már nem szükséges előzményekből származó központi telepítéseket. Az előfizetési szintű központi telepítések törléséhez használja a [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az az Deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)parancsot.
