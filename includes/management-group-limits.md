---
title: fájl belefoglalása
description: fájl belefoglalása
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334682"
---
| Erőforrás | Korlát |
| --- | --- |
| Felügyeleti csoportok könyvtáronként | 10,000 |
| Előfizetések felügyeleti csoportonként | Korlátlan. |
| A felügyeleticsoport-hierarchia szintjei | Gyökérszint plusz 6 szint<sup>1</sup> |
| Közvetlen szülőfelügyeleti csoport felügyeleti csoportonként | Eggyel |
| [Felügyeleti csoport szintű telepítések](../articles/azure-resource-manager/templates/deploy-to-management-group.md) helyenként | 800<sup>2</sup> |

<sup>1 1</sup> A 6 szint nem tartalmazza az előfizetési szintet.

<sup>2.</sup> Ha eléri a 800-ra vonatkozó korlátot, törölje a központi telepítéseket a már nem szükséges előzményekből. A felügyeleticsoport szintű telepítések törléséhez használja [az Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) vagy [az az deployment mg delete parancsot.](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)
