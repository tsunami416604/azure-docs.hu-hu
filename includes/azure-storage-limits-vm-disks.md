---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334969"
---
Több adatlemezt is csatolhat egy Azure-beli virtuális géphez. A virtuális gépek adatlemezei skálázhatósági és teljesítményi céljai alapján meghatározhatja a teljesítményre és a kapacitásra vonatkozó követelmények teljesítéséhez szükséges lemez számát és típusát.

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozza a virtuális géphez csatlakoztatott, magas kihasználtságú lemezek számát a lehetséges szabályozás elkerülése érdekében. Ha a csatlakoztatott lemezek nem magas kihasználtsággal rendelkeznek, a virtuális gép nagyobb számú lemezt tud támogatni.

**Azure Managed Disks esetén:**

Az alábbi táblázat az erőforrások számának alapértelmezett és maximális korlátját mutatja régiónként/előfizetésben. A Managed Disks, a pillanatképek és a lemezképek száma nincs korlátozva erőforráscsoport esetén.  

> | Erőforrás | Korlát |
> | --- | --- |
> | Standard szintű felügyelt lemezek | 50 000 |
> | Felügyelt lemezek standard SSD | 50 000 |
> | Prémium szintű Managed Disks | 50 000 |
> | Pillanatképek Standard_LRS | 50 000 |
> | Pillanatképek Standard_ZRS | 50 000 |
> | Felügyelt rendszerkép | 50 000 |

* **Standard szintű Storage-fiókok esetén:** A standard szintű Storage-fiók maximális száma 20 000 IOPS. A standard Storage-fiókban lévő összes virtuálisgép-lemez teljes IOPS nem lépheti túl ezt a korlátot.
  
    Nagyjából kiszámíthatja a standard Storage-fiók által támogatott, magas kihasználtságú lemezek számát a kérések díjszabási korlátja alapján. Egy alapszintű virtuális gép esetében például a magas kihasználtságú lemezek maximális száma körülbelül 66, amely lemezenként 20 000/300 IOPS. A standard szintű virtuális gép számára a magas kihasználtságú lemezek maximális száma körülbelül 40, amely lemezenként 20000/500 IOPS. 

* **Premium Storage-fiókok esetén:** A prémium szintű Storage-fiók maximális teljes átviteli sebessége 50 GB/s. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

