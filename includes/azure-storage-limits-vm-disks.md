---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "68669678"
---
Több adatlemezt is csatolhat egy Azure-beli virtuális géphez. A virtuális gépek adatlemezei skálázhatósági és teljesítményi céljai alapján meghatározhatja a teljesítményre és a kapacitásra vonatkozó követelmények teljesítéséhez szükséges lemez számát és típusát.

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozza a virtuális géphez csatlakoztatott, magas kihasználtságú lemezek számát a lehetséges szabályozás elkerülése érdekében. Ha a csatlakoztatott lemezek nem magas kihasználtsággal rendelkeznek, a virtuális gép nagyobb számú lemezt tud támogatni.

**Azure Managed Disks esetén:**

Az alábbi táblázat az erőforrások számának alapértelmezett és maximális korlátját mutatja régiónként/előfizetésben. A Managed Disks, a pillanatképek és a lemezképek száma nincs korlátozva erőforráscsoport esetén.  

> | Resource | Alapértelmezett korlát  | Felső korlát |
> | --- | --- | --- |
> | Standard szintű felügyelt lemezek | 50,000 | 50,000 |
> | Standard SSD típusú felügyelt lemezek | 50,000 | 50,000 |
> | Prémium szintű Managed Disks | 50,000 | 50,000 |
> | Standard_LRS-Pillanatképek | 50,000 | 50,000 |
> | Standard_ZRS-Pillanatképek | 50,000 | 50,000 |
> | Felügyelt rendszerkép | 50,000 | 50,000 |

* **Standard szintű Storage-fiókok esetén:** A standard szintű Storage-fiók maximális száma 20 000 IOPS. A standard Storage-fiókban lévő összes virtuálisgép-lemez teljes IOPS nem lépheti túl ezt a korlátot.
  
    Nagyjából kiszámíthatja a standard Storage-fiók által támogatott, magas kihasználtságú lemezek számát a kérések díjszabási korlátja alapján. Egy alapszintű virtuális gép esetében például a magas kihasználtságú lemezek maximális száma körülbelül 66, amely lemezenként 20 000/300 IOPS. A standard szintű virtuális gép számára a magas kihasználtságú lemezek maximális száma körülbelül 40, amely lemezenként 20000/500 IOPS. 

* **Premium Storage-fiókok esetén:** A prémium szintű Storage-fiók maximális teljes átviteli sebessége 50 GB/s. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

