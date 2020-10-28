---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 10599b47d55a0b4ea1eb79cda3c31a2479efb7c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755751"
---
Több adatlemezt is csatolhat egy Azure-beli virtuális géphez. A virtuális gépek adatlemezei skálázhatósági és teljesítményi céljai alapján meghatározhatja a teljesítményre és a kapacitásra vonatkozó követelmények teljesítéséhez szükséges lemez számát és típusát.

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozza a virtuális géphez csatlakoztatott, magas kihasználtságú lemezek számát a lehetséges szabályozás elkerülése érdekében. Ha a csatlakoztatott lemezek nem magas kihasználtsággal rendelkeznek, a virtuális gép nagyobb számú lemezt tud támogatni.

**Azure Managed Disks esetén:**

Az alábbi táblázat az erőforrások számának alapértelmezett és maximális korlátját mutatja régiónként/előfizetésben. A korlátok a platform által felügyelt kulcsokkal vagy az ügyfél által felügyelt kulcsokkal titkosított lemezektől függetlenül változatlanok maradnak. A Managed Disks, a pillanatképek és a lemezképek száma nincs korlátozva erőforráscsoport esetén.  

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

