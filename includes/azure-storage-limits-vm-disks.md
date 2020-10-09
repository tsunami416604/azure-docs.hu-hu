---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828939"
---
Több adatlemezt is csatolhat egy Azure-beli virtuális géphez. A virtuális gépek adatlemezei skálázhatósági és teljesítményi céljai alapján meghatározhatja a teljesítményre és a kapacitásra vonatkozó követelmények teljesítéséhez szükséges lemez számát és típusát.

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozza a virtuális géphez csatlakoztatott, magas kihasználtságú lemezek számát a lehetséges szabályozás elkerülése érdekében. Ha a csatlakoztatott lemezek nem magas kihasználtsággal rendelkeznek, a virtuális gép nagyobb számú lemezt tud támogatni.

**Azure Managed Disks esetén:**

Az alábbi táblázat az erőforrások számának alapértelmezett és maximális korlátját mutatja régiónként/előfizetésben. A Managed Disks, a pillanatképek és a lemezképek száma nincs korlátozva erőforráscsoport esetén.  

> | Erőforrás | Korlát |
> | --- | --- |
> | Standard szintű felügyelt lemezek | 50,000 |
> | Felügyelt lemezek standard SSD | 50,000 |
> | Prémium szintű Managed Disks | 50,000 |
> | Pillanatképek Standard_LRS | 50,000 |
> | Pillanatképek Standard_ZRS | 50,000 |
> | Felügyelt rendszerkép | 50,000 |

**Standard szintű Storage-fiókok esetén:** A standard szintű Storage-fiók maximális száma 20 000 IOPS. A standard Storage-fiókban lévő összes virtuálisgép-lemez teljes IOPS nem lépheti túl ezt a korlátot.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Premium Storage-fiókok esetén:** A prémium szintű Storage-fiók maximális teljes átviteli sebessége 50 GB/s. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

