---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334989"
---
| Erőforrás | Korlát |
| --- | --- |
| Előfizetések száma Azure Active Directory bérlőn | Korlátlan. |
| A [rendszergazdák](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) /előfizetés |Korlátlan. |
| [Erőforráscsoportok](../articles/azure-resource-manager/management/overview.md) /előfizetés |980 |
| Azure Resource Manager API-kérelem mérete |4 194 304 bájt. |
| Címkék/előfizetés<sup>1</sup> |50 |
| Egyedi címkézési számítások az<sup>1</sup> . előfizetéshez | 10,000 |
| [Előfizetés szintű központi telepítések](../articles/azure-resource-manager/templates/deploy-to-subscription.md) régiónként | 800<sup>2</sup> |

<sup>1</sup> Akár 50 címkét is alkalmazhat közvetlenül egy előfizetésre. Az előfizetés azonban korlátlan számú címkét tartalmazhat, amelyek az előfizetésben lévő erőforráscsoportok és erőforrások esetében alkalmazhatók. Az erőforrás-vagy erőforráscsoport-címkék száma 50-ra van korlátozva. A Resource Manager csak akkor adja vissza az előfizetésben szereplő [egyedi címke nevét és értékeit](/rest/api/resources/tags) , ha a címkék száma 10 000 vagy kevesebb. Továbbra is megtalálhatja az erőforrást címke szerint, ha a szám meghaladja a 10 000 értéket.  

<sup>2</sup> Ha eléri a 800-es üzemelő példányok korlátját, törölje a már nem szükséges előzményekből származó központi telepítéseket. Az előfizetési szintű központi telepítések törléséhez használja a [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az Deployment sub delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete)parancsot.
