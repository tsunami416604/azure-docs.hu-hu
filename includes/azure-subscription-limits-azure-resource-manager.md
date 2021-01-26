---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 01/25/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ad0c532c2ac80fd8a3bb3e68431ff7fc274d73e0
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792352"
---
| Erőforrás | Korlát |
| --- | --- |
| Előfizetések száma Azure Active Directory bérlőn | Korlátlan |
| A [rendszergazdák](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) /előfizetés |Korlátlan |
| [Erőforráscsoportok](../articles/azure-resource-manager/management/overview.md) /előfizetés |980 |
| Azure Resource Manager API-kérelem mérete |4 194 304 bájt |
| Címkék/előfizetés<sup>1</sup> |50 |
| Egyedi címkézési számítások az<sup>1</sup> . előfizetéshez | 80,000 |
| [Előfizetés szintű központi telepítések](../articles/azure-resource-manager/templates/deploy-to-subscription.md) régiónként | 800<sup>2</sup> |

<sup>1</sup> Akár 50 címkét is alkalmazhat közvetlenül egy előfizetésre. Az előfizetés azonban korlátlan számú címkét tartalmazhat, amelyek az előfizetésben lévő erőforráscsoportok és erőforrások esetében alkalmazhatók. Az erőforrás-vagy erőforráscsoport-címkék száma 50-ra van korlátozva. A Resource Manager csak akkor adja vissza az előfizetésben szereplő [egyedi címke nevét és értékeit](/rest/api/resources/tags) , ha a címkék száma 80 000 vagy kevesebb. Továbbra is megtalálhatja az erőforrást címke szerint, ha a szám meghaladja a 80 000 értéket.

<sup>2</sup> Ha eléri a 800-es üzemelő példányok korlátját, törölje az előzményekből már nem szükséges központi telepítéseket. Az előfizetési szintű központi telepítések törléséhez használja a [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az Deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete)parancsot.
