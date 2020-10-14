---
title: A kapacitás-készletezési problémák elhárítása Azure NetApp Files esetén | Microsoft Docs
description: Ismerteti a kapacitási készletek kezelése során esetlegesen felmerülő esetleges problémákat, és megoldásokat biztosít a problémákhoz.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/13/2020
ms.author: b-juche
ms.openlocfilehash: 54e6f4abd5ca6d15a4cc5a7bc9015abb005296a0
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013644"
---
# <a name="troubleshoot-capacity-pool-issues"></a>A kapacitási készlettel kapcsolatos problémák elhárítása

Ez a cikk azokat a problémákat ismerteti, amelyekkel a kapacitási készletek kezelése során előfordulhat, hogy a készlet módosítási műveletét is beleértve. 

## <a name="issues-managing-a-capacity-pool"></a>Kapacitási készletet kezelő problémák 

|     Hibafeltétel    |     Feloldás    |
|-|-|
| A kapacitási készlet létrehozásával kapcsolatos problémák |  Győződjön meg arról, hogy a kapacitási készlet száma nem haladja meg a korlátot. Lásd: [Azure NetApp Files erőforrás-korlátai](azure-netapp-files-resource-limits.md).  Ha a szám kisebb, mint a korlát, és továbbra is problémákat tapasztal, egy támogatási jegyet is benyújthat, és megadhatja a kapacitási készlet nevét. |
| Kapacitási készlet törlésével kapcsolatos problémák  |  Győződjön meg arról, hogy az előfizetésben lévő összes Azure NetApp Files kötetet és pillanatképet eltávolítja, ahol törölni szeretné a kapacitási készletet. <br> Ha már eltávolította az összes kötetet és pillanatképet, és továbbra sem tudja törölni a kapacitás-készletet, az erőforrásokra mutató hivatkozások továbbra is előfordulhatnak a portálon való megjelenítés nélkül. Ebben az esetben egy támogatási jegyet kell megadnia, és meg kell adnia, hogy végrehajtotta a fenti javasolt lépéseket. |
| A kötet létrehozása vagy módosítása a `Requested throughput not available` következő hibával meghiúsul: | A kötetek rendelkezésre álló átviteli sebességét a kapacitási készlet mérete és a szolgáltatási szint határozza meg. Ha nem rendelkezik elegendő átviteli sebességgel, növelje a készlet méretét, vagy módosítsa a meglévő kötet átviteli sebességét. | 

## <a name="issues-moving-a-capacity-pool"></a>Kapacitási készlet áthelyezésével kapcsolatos problémák 
|     Hibafeltétel    |     Feloldás    |
|-|-|
| A kötetek kapacitási készletének módosítása nem engedélyezett. | Lehet, hogy még nem engedélyezte ezt a funkciót. <br> A kötetek egy másik kapacitási készletbe való áthelyezésének funkciója jelenleg előzetes verzióban érhető el. Ha első alkalommal használja ezt a szolgáltatást, először regisztrálnia kell a funkciót, és be kell állítania a szolgáltatást `-FeatureName ANFTierChange` . Tekintse meg a [kötetek szolgáltatási szintjének dinamikus módosítása](dynamic-change-volume-service-level.md)című témakörben található regisztráció lépéseit. |
| A kapacitási készlet mérete túl kicsi a teljes kötet méretéhez. |  A hiba oka, hogy a célként megadott kapacitási készlet nem rendelkezik az áthelyezett kötet rendelkezésre álló kapacitásával.  <br> Növelje a cél készlet méretét, vagy válasszon egy nagyobb készletet.  Lásd: [Kapacitási készlet vagy kötet átméretezése](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
| A kötetek nem helyezhetők át, ha a célként megadott kapacitási készlet eltérő titkosítási típussal rendelkezik az eredeti kapacitási készletből.  Tegyük fel, hogy a kettős titkosításról egyetlen titkosításra helyez át, vagy fordítva.  | Válassza ki a cél kapacitási készletet ugyanazzal a titkosítási típussal, mint a forrás-kapacitási készlet.   |
|  A készlet módosítása nem hajtható végre, mert egy nevű kötet `'{source pool name}'` már létezik a célként megadott készletben. `'{target pool name}'` | Ez a hiba azért fordul elő, mert már létezik ilyen nevű kötet a cél kapacitási készletben.  Válasszon egy másik olyan kapacitási készletet, amely nem rendelkezik azonos nevű kötettel.   | 

## <a name="next-steps"></a>Következő lépések  

* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
* [Kötetek szolgáltatásszintjének dinamikus módosítása](dynamic-change-volume-service-level.md)
* [A kapacitáskészlet vagy kötet átméretezése](azure-netapp-files-resize-capacity-pools-or-volumes.md)
