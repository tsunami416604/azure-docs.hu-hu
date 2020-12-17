---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 45c959bc3cb71a965f254867798ae32d86ba1e70
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97612646"
---
| Erőforrás | Korlát |
| --- | --- |
| Virtuális gépek [előfizetésenként](https://azure.microsoft.com/pricing/) |25 000<sup>1</sup> régiónként. |
| Virtuálisgép-magok összesen, [előfizetésenként](https://azure.microsoft.com/pricing/) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| Azure spot VM-alapú teljes mag/ [előfizetés](https://azure.microsoft.com/pricing/) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| VM/sorozat, például Dv2 és F, mag/ [előfizetés](https://azure.microsoft.com/pricing/) |régiónként 20<sup>1</sup> . A korlát növeléséhez forduljon az ügyfélszolgálathoz. |
| [Rendelkezésre állási](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) csoportok/előfizetés |2 500 régiónként. |
| Virtuális gépek rendelkezésre állási készlete | 200 |
| Tanúsítvány/rendelkezésre állási csoport | 199<sup>2</sup> |
| Tanúsítványok/előfizetés |Korlátlan<sup>3</sup> |

<sup>1</sup> az alapértelmezett korlátok az ajánlati kategória típusa szerint változnak, például az ingyenes próbaverziós és az utólagos elszámolású, valamint az adatsorozatok, például a Dv2, az F és a G. A Nagyvállalati Szerződés-előfizetések alapértelmezett értéke például 350.

<sup>2</sup> a tulajdonságok, például az SSH nyilvános kulcsok is tanúsítványként lesznek leküldve, és a korlátnak számítanak. A korlát megkerüléséhez használja a [Windows Azure Key Vault bővítményét](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows) vagy a Linux rendszerhez készült [Azure Key Vault-bővítményt](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux) a tanúsítványok telepítéséhez.

<sup>3</sup> Azure Resource Manager a tanúsítványokat a Azure Key Vault tárolja. Az előfizetéshez tartozó tanúsítványok száma korlátlan. Üzemelő példányok 1 MB-os korlátja, amely egyetlen virtuális gépről vagy rendelkezésre állási csoportból áll.



> [!NOTE]
> A virtuális gépek magjai a regionális teljes korláttal rendelkeznek. Emellett a régión belüli méretre (például Dv2 és F) is korlátozva van. Ezeket a korlátokat külön kényszeríti a rendszer. Például tegyük fel, hogy egy előfizetés az USA keleti régiójára vonatkozó teljes magkorlátja 30, az A sorozatú magkorlátja 30, és a D sorozatú magkorlátja is 30. Ez az előfizetés 30 a1-es virtuális gépet vagy 30 D1 virtuális gépet telepíthet, vagy a kettő kombinációja nem haladhatja meg az összes 30 magot. Egy kombináció például 10 a1 virtuális gép és 20 D1 virtuális gép.  
> <!-- -->
>
