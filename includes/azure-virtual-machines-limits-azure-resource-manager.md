---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334803"
---
| Erőforrás | Korlát |
| --- | --- |
| Virtuális gépek [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |25 000<sup>1</sup> régiónként. |
| Virtuálisgép-magok összesen, [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> régiónként. Lépjen kapcsolatba az ügyfélszolgálattal a korlát növeléséhez. |
| Az Azure Spot virtuális gép teljes magjai [előfizetésenként](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> régiónként. Lépjen kapcsolatba az ügyfélszolgálattal a korlát növeléséhez. |
| Virtuális gép sorozatonként, például Dv2 és F, [előfizetésenkénti](../articles/billing-buy-sign-up-azure-subscription.md) magok |20<sup>1</sup> régiónként. Lépjen kapcsolatba az ügyfélszolgálattal a korlát növeléséhez. |
| [Rendelkezésre állási csoportok](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) előfizetésenként |2000 régiónként. |
| Virtuális gépek rendelkezésre állási készletenként | 200 |
| Tanúsítványok előfizetésenként |Korlátlan<sup>2</sup> |

<sup>1 1</sup> Az alapértelmezett korlátok az ajánlatkategória típusától függően változnak, például az ingyenes próbaverzió és a kiosztó-kiosztó nóc, valamint sorozatok szerint, például Dv2, F és G. A nagyvállalati szerződéssel előfizetések alapértelmezett beállítása például 350.

<sup>2.</sup> Az Azure Resource Manager, tanúsítványok az Azure Key Vault tárolja. A tanúsítványok száma korlátlan egy előfizetéshez. A tanúsítványok egy központi telepítésre 1 MB-os korláttal rendelkeznek, amely egyetlen virtuális gépből vagy egy rendelkezésre állási csoportból áll.

> [!NOTE]
> A virtuálisgép-magok területi teljes korláttal rendelkeznek. A regionális méretsorozatok, például a Dv2 és az F sorozatok is korlátozva vannak. Ezek a korlátozások külön-külön vannak érvényben. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 A1-es virtuális gépet vagy 30 D1 virtuális gépet telepíthet, vagy a kettő kombinációját, hogy ne haladja meg az összesen 30 magot. Egy kombináció például 10 A1 virtuális gép és 20 D1 virtuális gép.  
> <!-- -->
> 
