---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334969"
---
Az Azure virtuális gépekhez számos adatlemez csatolható. A virtuális gép adatlemezeinek méretezhetősége és teljesítménycéljai alapján meghatározhatja a teljesítmény- és kapacitáskövetelmények teljesítéséhez szükséges lemez számát és típusát.

> [!IMPORTANT]
> Az optimális teljesítmény érdekében korlátozza a virtuális géphez csatlakoztatott nagy mértékben kihasznált lemezek számát, hogy elkerülje a szabályozást. Ha az összes csatlakoztatott lemez nem nagy mértékben kihasznált egy időben, a virtuális gép támogatja a nagyobb számú lemezek.

**Azure által kezelt lemezek esetén:**

Az alábbi táblázat bemutatja az erőforrások alapértelmezett és maximális korlátját régiónként előfizetésenként. A felügyelt lemezek, pillanatképek és képek erőforráscsoportonkénti száma nincs korlátozva.  

> | Erőforrás | Korlát |
> | --- | --- |
> | Szabványos felügyelt lemezek | 50 000 |
> | Szabványos SSD-kezelt lemezek | 50 000 |
> | Prémium szintű felügyelt lemezek | 50 000 |
> | Standard_LRS pillanatképek | 50 000 |
> | Standard_ZRS pillanatképek | 50 000 |
> | Felügyelt kép | 50 000 |

* **Standard tárfiókok esetén:** A standard tárfiók maximális teljes kérelem aránya 20 000 IOPS. A standard szintű tárfiókban lévő összes virtuálisgép-lemez teljes IOPS-értéke nem haladhatja meg ezt a korlátot.
  
    Nagyjából kiszámíthatja a nagy mértékben kihasznált lemezek száma által támogatott egyetlen standard tárfiók alapján a kérelem sebességkorlát. Például egy alapszintű virtuális gép, a nagy mértékben kihasznált lemezek maximális száma körülbelül 66, amely 20 000/300 IOPS lemezenként. A standard szintű virtuális gépek nagy mértékben kihasznált lemezek maximális száma körülbelül 40, amely lemezenként 20 000/500 IOPS. 

* **Prémium szintű tárfiókok esetén:** A prémium szintű tárfiók maximális teljes átviteli sebessége 50 Gb/s. Az összes virtuálisgép-lemezen lévő teljes átvitel nem lépheti túl ezt a korlátot.

