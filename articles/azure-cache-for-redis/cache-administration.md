---
title: Az Azure cache felügyelete a Redis-hez
description: Megtudhatja, hogyan hajthat végre olyan felügyeleti feladatokat, mint például a Redis készült Azure cache újraindítása és frissítéseinek ütemezett frissítése
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 69686cad20bc4ce70bff2a92a216c9430522c301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278843"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Az Azure cache felügyelete a Redis-hez
Ez a témakör azt ismerteti, hogyan hajtható végre olyan adminisztrációs feladatok, mint például a Redis-példányok Azure cache-re való [újraindítása](#reboot) és a [frissítések ütemezése](#schedule-updates) .

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Újraindítás
Az **Újraindítás** panel lehetővé teszi a gyorsítótár egy vagy több csomópontjának újraindítását. Ez az újraindítási funkció lehetővé teszi az alkalmazás rugalmasságának tesztelését, ha hiba történt a gyorsítótár-csomópontok meghibásodása esetén.

![Újraindítás](./media/cache-administration/redis-cache-administration-reboot.png)

Válassza ki az újraindítani kívánt csomópontokat, majd kattintson az **Újraindítás**gombra.

![Újraindítás](./media/cache-administration/redis-cache-reboot.png)

Ha olyan prémium szintű gyorsítótárral rendelkezik, amelyen engedélyezve van a fürtözés, kiválaszthatja, hogy a gyorsítótár mely szegmensei induljon újra.

![Újraindítás](./media/cache-administration/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomópontjának újraindításához válassza ki a kívánt csomópontokat, majd kattintson az **Újraindítás**gombra. Ha olyan prémium szintű gyorsítótárral rendelkezik, amelyen engedélyezve van a fürtözés, válassza ki a kívánt szegmenseket az újraindításhoz, majd kattintson az **Újraindítás**gombra. Néhány perc elteltével a kijelölt csomópontok újraindulnak, és néhány perc múlva újra online állapotba kerülnek.

Az ügyfélalkalmazások hatása attól függően változik, hogy melyik csomópontokat kívánja újraindítani.

* **Főkiszolgáló** – a főcsomópont újraindításakor a Redis-hez készült Azure cache feladatátvételt hajt végre a replika csomóponton, és a főkiszolgálót is támogatja. A feladatátvétel során előfordulhat, hogy egy rövid időszak, amelyben a kapcsolatok sikertelenek lesznek a gyorsítótárban.
* **Slave** – a Slave csomópont újraindításakor általában nincs hatással a gyorsítótár-ügyfelekre.
* **Mind a Master** , mind a Slave – ha a gyorsítótár-csomópontok is újraindulnak, az összes adat elvész a gyorsítótárban, és a gyorsítótárhoz való csatlakozás meghiúsul, amíg az elsődleges csomópont ismét online állapotba nem kerül. Ha beállította az [adatmegőrzést](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés visszakerül, ha a gyorsítótár online állapotba kerül, de a legutóbbi biztonsági mentést követően történt gyorsítótár-írások elvesznek.
* **Prémium szintű gyorsítótár csomópontja** , amelyen engedélyezve van a fürtözés. Ha egy prémium szintű gyorsítótár egy vagy több csomópontját a fürtözés engedélyezése után újraindítja, a kiválasztott csomópontok viselkedése megegyezik a nem fürtözött gyorsítótár megfelelő csomópontjának vagy csomópontjainak újraindításával.

## <a name="reboot-faq"></a>Újraindítással kapcsolatos gyakori kérdések
* [Melyik csomópontot kell újraindítani az alkalmazás teszteléséhez?](#which-node-should-i-reboot-to-test-my-application)
* [Újra lehet indítani a gyorsítótárat az ügyfélkapcsolatok törléséhez?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Elveszítem az adatok a gyorsítótárból, ha újraindítást végezek?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Újraindíthatom a gyorsítótárat a PowerShell, a CLI vagy más felügyeleti eszközök használatával?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Melyik csomópontot kell újraindítani az alkalmazás teszteléséhez?
Az alkalmazás rugalmasságának teszteléséhez a gyorsítótár elsődleges csomópontjának meghibásodása esetén indítsa újra a **fő** csomópontot. Ha tesztelni szeretné az alkalmazás rugalmasságát a másodlagos csomópont meghibásodása miatt, indítsa újra a **Slave** csomópontot. Ha tesztelni szeretné az alkalmazás rugalmasságát a gyorsítótár teljes meghibásodása ellen, indítsa újra **mindkét** csomópontot.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Újra lehet indítani a gyorsítótárat az ügyfélkapcsolatok törléséhez?
Igen, ha újraindítja a gyorsítótárat, az összes ügyfél-kapcsolat törlődik. Az újraindítás hasznos lehet abban az esetben, ha az összes ügyfélkapcsolatot a rendszer logikai hiba vagy az ügyfélalkalmazás hibája miatt használja fel. Az egyes díjszabási szintek eltérő [ügyfélkapcsolati korlátokkal](cache-configure.md#default-redis-server-configuration) rendelkeznek a különböző méretekhez, és a határértékek elérésekor a rendszer nem fogad el több ügyfélkapcsolatot. A gyorsítótár újraindítása lehetővé teszi az összes ügyfélkapcsolat törlését.

> [!IMPORTANT]
> Ha újraindítja a gyorsítótárat az ügyfélkapcsolatok törléséhez, a StackExchange. Redis automatikusan újracsatlakozik, ha a Redis csomópont ismét online állapotba kerül. Ha az alapul szolgáló probléma nem oldható fel, az ügyfélkapcsolatok továbbra is használhatók.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Elveszítem az adatok a gyorsítótárból, ha újraindítást végezek?
Ha a **fő** -és a **alárendelt** csomópontok is újraindulnak, akkor a gyorsítótárban lévő összes adat (vagy abban a szegmensben, ha a fürtön engedélyezve van a prémium szintű gyorsítótár használata) elvesznek, ez azonban nem garantált. Ha beállította az [adatmegőrzést](cache-how-to-premium-persistence.md), a rendszer visszaállítja a legutóbbi biztonsági mentést, ha a gyorsítótár online állapotba kerül, de a biztonsági mentés után történt gyorsítótár-írások elvesznek.

Ha csak az egyik csomópontot újraindítja, a rendszer általában nem veszíti el az adatvesztést, de az is lehetséges. Ha például a főcsomópontot újraindították, és a gyorsítótár írása folyamatban van, a gyorsítótárból származó adatok elvesznek. Az adatvesztés egy másik forgatókönyve az egyik csomópont újraindítása, a másik csomópont pedig egy hiba miatt leáll. Az adatvesztés lehetséges okaival kapcsolatos további információkért lásd: [Mi történt az Redis-beli adatokkal?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Újraindíthatom a gyorsítótárat a PowerShell, a CLI vagy más felügyeleti eszközök használatával?
Igen, a PowerShell-utasításokért lásd: az [Azure cache újraindítása a Redis](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

## <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezett frissítése** panelen megadhatja a gyorsítótár-példány karbantartási időszakát. Ha a karbantartási időszak meg van adva, a rendszer minden Redis-kiszolgáló frissítést végez ebben az ablakban. 

> [!NOTE] 
> A karbantartási időszak csak a Redis-kiszolgáló frissítéseire vonatkozik, és nem a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének összes Azure-frissítésére vagy frissítésére.
>

![Frissítések ütemezése](./media/cache-administration/redis-schedule-updates.png)

A karbantartási időszak megadásához tekintse meg a kívánt napokat, és minden nap esetében határozza meg a karbantartási időszak kezdő óráját, majd kattintson **az OK**gombra. Vegye figyelembe, hogy a karbantartási időszak időpontja UTC. 

A frissítések alapértelmezett és minimális karbantartási időszaka öt óra. Ez az érték nem konfigurálható a Azure Portalból, de a `MaintenanceWindow` [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) parancsmag paraméterének használatával konfigurálhatja a PowerShellben. További információ: kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?

## <a name="schedule-updates-faq"></a>Frissítések ütemezett frissítései – gyakori kérdések
* [Mikor történik a frissítések használata, ha nem használom a frissítések ütemezett szolgáltatását?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Milyen típusú frissítések történnek az ütemezett karbantartási időszakban?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Mikor történik a frissítések használata, ha nem használom a frissítések ütemezett szolgáltatását?
Ha nem ad meg karbantartási időszakot, a frissítés bármikor elvégezhető.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Milyen típusú frissítések történnek az ütemezett karbantartási időszakban?
Az ütemezett karbantartási időszakban csak a Redis-kiszolgáló frissítései lesznek elérhetők. A karbantartási időszak nem vonatkozik az Azure frissítéseire vagy a virtuális gép operációs rendszerének frissítéseire.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?
Igen, a következő PowerShell-parancsmagokkal kezelheti az ütemezett frissítéseket:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [Új – AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Új – AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>További lépések
* Ismerje [meg az Azure cache-t a prémium szintű Redis](cache-premium-tier-intro.md) -funkciókhoz.

