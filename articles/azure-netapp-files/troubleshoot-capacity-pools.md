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
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343320"
---
# <a name="troubleshoot-capacity-pool-issues"></a>A kapacitási készlettel kapcsolatos problémák elhárítása

Ez a cikk a kapacitási készletek kezelése során esetlegesen felmerülő problémák megoldásait ismerteti. 

## <a name="issues-creating-a-capacity-pool"></a>A kapacitási készlet létrehozásával kapcsolatos problémák

Győződjön meg arról, hogy a kapacitási készlet száma nem haladja meg a korlátot. Lásd: [Azure NetApp Files erőforrás-korlátai](azure-netapp-files-resource-limits.md).  Ha a szám kisebb, mint a korlát, és továbbra is problémákat tapasztal, egy támogatási jegyet is benyújthat, és megadhatja a kapacitási készlet nevét.

## <a name="issues-deleting-a-capacity-pool"></a>Kapacitási készlet törlésével kapcsolatos problémák

Győződjön meg arról, hogy eltávolította az összes olyan Azure NetApp Files kötetet és pillanatképet az előfizetésben, amelyben törölni szeretné a kapacitás-készletet.   

Ha már eltávolította az összes kötetet és pillanatképet, és továbbra sem tudja törölni a kapacitás-készletet, akkor előfordulhat, hogy az erőforrásokra mutató hivatkozások még a portálon való megjelenítés nélkül is létezhetnek. Ebben az esetben egy támogatási jegyet kell megadnia, és meg kell adnia, hogy végrehajtotta a fenti javasolt lépéseket. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>A kötet létrehozása vagy módosítása sikertelen a "kért átviteli sebesség nem érhető el" hibaüzenettel

A kötetek rendelkezésre álló átviteli sebességét a kapacitási készlet mérete és a szolgáltatási szint határozza meg. Ha nem rendelkezik elegendő átviteli sebességgel, növelje a készlet méretét, vagy módosítsa a meglévő kötet átviteli sebességét.


## <a name="next-steps"></a>Következő lépések  

* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Manuális QoS-kapacitás készletének kezelése](manage-manual-qos-capacity-pool.md)
