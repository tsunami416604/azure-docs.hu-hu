---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58261483"
---
Egy több adatlemez csatlakoztathat egy Azure virtuális gépen. A virtuális gép adatlemezek méretezhetőségi és Teljesítménycélok alapján, meghatározhatja a száma és típusa, amely csak szüksége lehet a teljesítményre és kapacitásra vonatkozó lemez.

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozhatja a lehetséges szabályozás elkerülése érdekében a virtuális géphez csatlakoztatott nagy kihasználtságú lemezek számát. Csatlakoztatott lemezek kihasználtságú nem egy időben, ha a virtuális gép nagyobb számú lemezeket is támogatja.

**Az Azure-ban felügyelt lemezek:**

Az alábbi táblázat mutatja be, az alapértelmezett és korlátozásainak megfelelő régiónként és előfizetésenként erőforrások száma

> | Erőforrás | Alapértelmezett korlát  | Felső korlát |
> | --- | --- | --- |
> | Standard szintű felügyelt lemezekre | 25,000 | 50,000 |
> | Standard SSD típusú felügyelt lemezek | 25,000 | 50,000 |
> | Prémium szintű Managed Disks | 25,000 | 50,000 |
> | Standard_LRS pillanatképek | 25,000 | 50,000 |
> | Standard_ZRS pillanatképek | 25,000 | 50,000 |
> | Felügyelt rendszerkép | 25,000 | 50,000 |

* **Standard szintű tárfiókok esetén:** A standard szintű tárfiók a kérelmek maximális száma összesen 20 000 IOPS aránya rendelkezik. Az összes virtuálisgép-lemezek, a standard szintű storage-fiókban a teljes IOPS nem haladhatja meg ezt a korlátot.
  
    Nagyjából kiszámíthatja a kérelemarány korlátja alapján egyetlen standard szintű tárfiók által támogatott nagy kihasználtságú lemezek számát. Például egy alapszintű VM, a nagy kihasználtságú lemezek maximális száma érték körülbelül 66, amely 20 000/300 IOPS lemezenként. A Standard szintű virtuális gépek nagy kihasználtságú lemezek maximális száma érték körülbelül 40, amely 20 000/500 IOPS lemezenként. 

* **Prémium szintű tárfiókok esetén:** Premium storage-fiók rendelkezik 50 gbps maximális teljes átviteli sebességet. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

