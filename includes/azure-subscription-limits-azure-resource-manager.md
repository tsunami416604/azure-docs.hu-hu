---
title: fájl belefoglalása
description: fájl belefoglalása
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: eb2d663a56084fc625c95f1f1a6236385880180d
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796017"
---
| Erőforrás | Alapértelmezett korlát | Felső korlát |
| --- | --- | --- |
| Virtuális gépek [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> régiónként. |25 000 régiónként. |
| Virtuálisgép-magok összesen, [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |régiónként 20<sup>1</sup> . | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| Azure spot VM-alapú teljes mag/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) |régiónként 20<sup>1</sup> . | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| VM/sorozat, például Dv2 és F, mag/ [előfizetés](../articles/billing-buy-sign-up-azure-subscription.md) |régiónként 20<sup>1</sup> . | Vegye fel a kapcsolatot az ügyfélszolgálattal. |
| A [rendszergazdák](../articles/billing-add-change-azure-subscription-administrator.md) /előfizetés |Korlátlan. |Korlátlan. |
| [Storage-fiókok](../articles/storage/common/storage-quickstart-create-account.md) régiónként/előfizetéssel |250 |250 |
| [Erőforráscsoportok](../articles/azure-resource-manager/resource-group-overview.md) /előfizetés |980 |980 |
| [Rendelkezésre állási](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) csoportok/előfizetés |2 000 régiónként. |2 000 régiónként. |
| Azure Resource Manager API-kérelem mérete |4 194 304 bájt. |4 194 304 bájt. |
| Címkék/előfizetés<sup>2</sup> |Korlátlan. |Korlátlan. |
| Egyedi címkézési számítások/előfizetés<sup>2</sup> | 10,000 | 10,000 |
| [Felhőszolgáltatások](../articles/cloud-services/cloud-services-choose-me.md) előfizetésenként |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Affinitáscsoportok](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) előfizetésenként |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [Előfizetés szintű központi telepítések](../articles/azure-resource-manager/deploy-to-subscription.md) régiónként | 800<sup>4</sup> | 800 |

<sup>1</sup> Az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverziós és az utólagos elszámolású, valamint az adatsorozatok, például a Dv2, az F és a G. A Nagyvállalati Szerződés-előfizetések alapértelmezett értéke például 350.

<sup>2</sup> Az előfizetések korlátlan számú címkét igényelhetnek. Az erőforrás-vagy erőforráscsoport-címkék száma 50-ra van korlátozva. A Resource Manager csak akkor adja vissza az előfizetésben szereplő [egyedi címke nevét és értékeit](/rest/api/resources/tags) , ha a címkék száma 10 000 vagy kevesebb. Továbbra is megtalálhatja az erőforrást címke szerint, ha a szám meghaladja a 10 000 értéket.  

<sup>3</sup> Ezek a funkciók már nem szükségesek az Azure-erőforráscsoportok és a Resource Manager használatához.

<sup>4</sup> Ha eléri a 800-es üzemelő példányok korlátját, törölje a már nem szükséges előzményekből származó központi telepítéseket. Az előfizetési szintű központi telepítések törléséhez használja a [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) vagy [az az Deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)parancsot.

> [!NOTE]
> A virtuális gépek magjai a regionális teljes korláttal rendelkeznek. Emellett a régión belüli méretre (például Dv2 és F) is korlátozva van. Ezeket a korlátokat külön kényszeríti a rendszer. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 a1-es virtuális gépet vagy 30 D1 virtuális gépet telepíthet, vagy a kettő kombinációja nem haladhatja meg az összes 30 magot. Egy kombináció például 10 a1 virtuális gép és 20 D1 virtuális gép.  
> <!-- -->
> 
> 

