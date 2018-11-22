---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: aa701ada917811382351fee9469a5cfa9a7599b8
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279683"
---
Az Azure virtuális gépek több adatlemez csatolását támogatták. Ez a cikk ismerteti a virtuális gép adatlemezek méretezhetőségi és teljesítménycéljai. Ezeken a célokon használatával segít eldönteni, hogy száma és a teljesítmény és kapacitás követelmények teljesítéséhez szükséges lemez típusa. 

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozhatja a lehetséges szabályozás elkerülése érdekében a virtuális géphez csatlakoztatott nagy kihasználtságú lemezek számát. Az összes csatlakoztatott lemez magas nem használhatók egyszerre, ha a virtuális gép nagyobb számú lemezeket is támogatja.

* **Az Azure Managed Disks:** 

> | Erőforrás | Alapértelmezett korlát | Felső korlát |
> | --- | --- | --- |
> | Standard Managed Disks | 10,000 | 50,000 |
> | Standard SSD Managed Disks | 10,000 | 50,000 |
> | Premium Managed Disks | 10,000 | 50,000 |
> | Standard_LRS pillanatképek | 10,000 | 50,000 |
> | Standard_ZRS pillanatképek | 10,000 | 50,000 |
> | Felügyelt rendszerkép | 10,000 | 50,000 |

* **Standard szintű tárfiókok esetén:** A Standard szintű tárfiókok összesen legfeljebb 20 000 IOPS kérelemaránnyal rendelkeznek. A Standard szintű tárfiókokon az összes virtuálisgép-lemezen lévő teljes IOPS nem léphet túl ezen a korláton.
  
    Nagyjából kiszámíthatja az egyetlen Standard szintű tárfiók által támogatott nagy kihasználtságú lemezek számát a kérelemarány korlátja alapján. Ha például egy alapszintű Tarifacsomagot Virtuálisgép, nagy kihasználtságú lemezek maximális száma körülbelül 66 (20 000/300 IOPS lemezenként), és a egy Standard szintű virtuális gép van, pedig körülbelül 40 (20 000/500 IOPS lemezenként). 

* **Prémium szintű tárfiókok esetén:** A prémium szintű tárfiókok összesen legfeljebb 50 Gbps átviteli sebességgel rendelkeznek. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

