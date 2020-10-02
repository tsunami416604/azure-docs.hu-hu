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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651054"
---
# <a name="troubleshoot-capacity-pool-issues"></a>A kapacitási készlettel kapcsolatos problémák elhárítása

Ez a cikk a kapacitási készletek kezelése során esetlegesen felmerülő problémák megoldásait ismerteti. 

## <a name="error-conditions-and-resolutions"></a>Hibákra vonatkozó feltételek és megoldások 

|     Hibafeltétel    |     Feloldás    |
|-|-|
| A kapacitási készlet létrehozásával kapcsolatos problémák |  Győződjön meg arról, hogy a kapacitási készlet száma nem haladja meg a korlátot. Lásd: [Azure NetApp Files erőforrás-korlátai](azure-netapp-files-resource-limits.md).  Ha a szám kisebb, mint a korlát, és továbbra is problémákat tapasztal, egy támogatási jegyet is benyújthat, és megadhatja a kapacitási készlet nevét. |
| Kapacitási készlet törlésével kapcsolatos problémák  |  Győződjön meg arról, hogy az előfizetésben lévő összes Azure NetApp Files kötetet és pillanatképet eltávolítja, ahol törölni szeretné a kapacitási készletet. <br> Ha már eltávolította az összes kötetet és pillanatképet, és továbbra sem tudja törölni a kapacitás-készletet, az erőforrásokra mutató hivatkozások továbbra is előfordulhatnak a portálon való megjelenítés nélkül. Ebben az esetben egy támogatási jegyet kell megadnia, és meg kell adnia, hogy végrehajtotta a fenti javasolt lépéseket. |
| A kötet létrehozása vagy módosítása a `Requested throughput not available` következő hibával meghiúsul: | A kötetek rendelkezésre álló átviteli sebességét a kapacitási készlet mérete és a szolgáltatási szint határozza meg. Ha nem rendelkezik elegendő átviteli sebességgel, növelje a készlet méretét, vagy módosítsa a meglévő kötet átviteli sebességét. | 

## <a name="next-steps"></a>Következő lépések  

* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Manuális QoS-kapacitáskészlet kezelése](manage-manual-qos-capacity-pool.md)
