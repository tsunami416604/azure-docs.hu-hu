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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334989"
---
| Erőforrás | Korlát |
| --- | --- |
| Előfizetések Azure Active Directory-bérlőnként | Korlátlan. |
| [Társadminisztrátorok](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) előfizetésenként |Korlátlan. |
| [Erőforráscsoportok](../articles/azure-resource-manager/management/overview.md) előfizetésenként |980 |
| Az Azure Resource Manager API-kérelem mérete |4 194 304 bájt. |
| Címkék előfizetésenként<sup>1</sup> |50 |
| Egyedi címkeszámítások előfizetésenként<sup>1</sup> | 10,000 |
| [Előfizetési szintű telepítések](../articles/azure-resource-manager/templates/deploy-to-subscription.md) helyenként | 800<sup>2</sup> |

<sup>1 1</sup> Legfeljebb 50 címkét alkalmazhat közvetlenül egy előfizetésre. Az előfizetés azonban korlátlan számú címkét tartalmazhat, amelyek az előfizetésen belüli erőforráscsoportokra és erőforrásokra vonatkoznak. Az erőforrásonkénti vagy erőforráscsoportonkénti címkék száma legfeljebb 50 lehet. Az Erőforrás-kezelő csak akkor adja vissza [az előfizetésben szereplő egyedi címkenevek és értékek listáját,](/rest/api/resources/tags) ha a címkék száma 10 000 vagy kevesebb. Továbbra is megtalálhatja az erőforrást címke szerint, ha a szám meghaladja a 10 000-et.  

<sup>2.</sup> Ha eléri a 800-ra vonatkozó korlátot, törölje a központi telepítéseket a már nem szükséges előzményekből. Az előfizetési szintű telepítések törléséhez használja [az Eltávolítás-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az az-telepítési rész törlését.](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete)
