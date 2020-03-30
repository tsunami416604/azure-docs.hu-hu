---
title: Az Azure Cache for Redis felügyelete
description: Ismerje meg, hogyan hajthat végre felügyeleti feladatokat, például az Azure Cache for Redis újraindítását és ütemezését
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
# <a name="how-to-administer-azure-cache-for-redis"></a>Az Azure Cache for Redis felügyelete
Ez a témakör ismerteti, hogyan felügyeleti feladatok, például [újraindítása](#reboot) és [ütemezése frissítések et](#schedule-updates) az Azure Cache redis példányok.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Újraindítás
Az **Újraindítás** panel lehetővé teszi a gyorsítótár egy vagy több csomók újraindítását. Ez az újraindítási képesség lehetővé teszi, hogy tesztelje az alkalmazást a rugalmasság, ha egy gyorsítótár-csomópont meghibásodása van.

![Újraindítás](./media/cache-administration/redis-cache-administration-reboot.png)

Jelölje ki az újraindításhoz szükséges csomópontokat, és kattintson az **Újraindítás gombra.**

![Újraindítás](./media/cache-administration/redis-cache-reboot.png)

Ha a fürtözés engedélyezve van a prémium szintű gyorsítótár, kiválaszthatja, hogy a gyorsítótár mely szegmenseit szeretné újraindítani.

![Újraindítás](./media/cache-administration/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomók újraindításához jelölje ki a kívánt csomópontokat, és kattintson az **Újraindítás gombra.** Ha a fürtözés engedélyezve van a prémium szintű gyorsítótárral, válassza ki az újraindítani kívánt szegmenseket, majd kattintson az **Újraindítás gombra.** Néhány perc múlva a kijelölt csomópontok újraindulnak, és néhány perccel később újra online állapotba kerülnek.

Az ügyfélalkalmazásokra gyakorolt hatás attól függ, hogy mely csomópontokat indítja újra.

* **Fő –** A fő csomópont újraindítása kor, az Azure Cache for Redis átadja a replikacsomópont, és elősegíti azt a master. A feladatátvétel során előfordulhat, hogy egy rövid idő közrei, amelyben a kapcsolatok nem a gyorsítótárba.
* **Slave** – A slave csomópont újraindításakor általában nincs hatással az ügyfelek gyorsítótára.
* **A fő és a slave** – Ha mindkét gyorsítótár-csomópont újraindul, minden adat elvész a gyorsítótárban, és a gyorsítótárhoz való kapcsolatok nem sikerül, amíg az elsődleges csomópont nem jön vissza online. Ha beállította az [adatmegőrzést](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés visszaáll, amikor a gyorsítótár újra online állapotba kerül, de a legutóbbi biztonsági mentés után bekövetkezett gyorsítótár-írások elvesznek.
* **A prémium szintű gyorsítótár fürtözési engedélyezésével rendelkező csomópontok** – Ha egy prémium szintű gyorsítótár egy vagy több csomópontját újraindítja fürtözéssel, a kijelölt csomópontok viselkedése megegyezik a nem fürtözött gyorsítótár megfelelő csomópontjának vagy csomópontjainak újraindításakor.

## <a name="reboot-faq"></a>Gyakori kérdések újraindítása
* [Melyik csomópontot indítsam újra az alkalmazás teszteléséhez?](#which-node-should-i-reboot-to-test-my-application)
* [Újraindíthatom a gyorsítótárat az ügyfélkapcsolatok törléséhez?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Elvesznek az adatok a gyorsítótárból, ha újraindítom?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Újraindíthatom a gyorsítótárat PowerShell, CLI vagy más felügyeleti eszközök használatával?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Melyik csomópontot indítsam újra az alkalmazás teszteléséhez?
Ha szeretné tesztelni az alkalmazás rugalmasságát a gyorsítótár elsődleges csomópontjának meghibásodása ellen, indítsa újra a **fő** csomópontot. Ha tesztelni szeretné az alkalmazás rugalmasságát a másodlagos csomópont meghibásodása ellen, indítsa újra a **Slave** csomópontot. Az alkalmazás rugalmasságának a gyorsítótár teljes meghibásodása elleni teszteléséhez indítsa újra **mindkét** csomópontot.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Újraindíthatom a gyorsítótárat az ügyfélkapcsolatok törléséhez?
Igen, ha újraindítja a gyorsítótárat, az ügyfélkapcsolatok törlődnek. Az újraindítás akkor lehet hasznos, ha az összes ügyfélkapcsolat logikai hiba vagy hiba miatt használatos az ügyfélalkalmazásban. Minden tarifacsomag különböző [ügyfélkapcsolati korlátokkal](cache-configure.md#default-redis-server-configuration) rendelkezik a különböző méretekhez, és ezek a korlátok elérése után nem fogad el több ügyfélkapcsolatot. A gyorsítótár újraindítása lehetővé teszi az összes ügyfélkapcsolat törlését.

> [!IMPORTANT]
> Ha újraindítja a gyorsítótárat az ügyfélkapcsolatok törléséhez, a StackExchange.Redis automatikusan újracsatlakozik, amint a Redis-csomópont újra online állapotba kerül. Ha az alapul szolgáló probléma nem oldódott meg, az ügyfélkapcsolatok továbbra is felhasználhatók.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Elvesznek az adatok a gyorsítótárból, ha újraindítom?
Ha újraindítja a **Master** és a **Slave** csomópontokat is, a gyorsítótárban lévő összes adat (vagy abban a szegmensben, ha fürtözési engedélyezve lévő prémium gyorsítótárat használ) elveszhet, de ez sem garantált. Ha beállította az [adatmegőrzést](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés visszaáll, amikor a gyorsítótár újra online állapotba kerül, de a biztonsági másolat készítése után beírt gyorsítótár-írások elvesznek.

Ha csak az egyik csomópontot indítja újra, az adatok általában nem vesznek el, de még mindig lehetnek. Ha például a fő csomópont újraindul, és a gyorsítótár írási folyamata folyamatban van, a gyorsítótár írási adatai elvesznek. Egy másik forgatókönyv az adatvesztés lenne, ha újraindít egy csomópontot, és a másik csomópont történik, hogy leáll egy hiba miatt ugyanabban az időben. Az adatvesztés lehetséges okairól a [Mi történt az adataimmal a Redis-ben című témakörben talál.](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Újraindíthatom a gyorsítótárat PowerShell, CLI vagy más felügyeleti eszközök használatával?
Igen, a PowerShell-utasításokhoz [lásd: Az Azure-gyorsítótár újraindítása a Redis.Yes,](cache-how-to-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis)for PowerShell instructions see To reboot an Azure Cache for Redis.

## <a name="schedule-updates"></a>Frissítések ütemezése
A **Frissítés ütemezése** panel lehetővé teszi, hogy kijelölje a karbantartási időszakot a gyorsítótár-példányhoz. Ha a karbantartási időszak meg van adva, a Redis-kiszolgáló frissítései ebben az időszakban történnek. 

> [!NOTE] 
> A karbantartási időszak csak a Redis-kiszolgáló frissítéseire vonatkozik, a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének egyetlen Azure-frissítésére és frissítéseire nem.
>

![Frissítések ütemezése](./media/cache-administration/redis-schedule-updates.png)

Karbantartási időszak megadásához ellenőrizze a kívánt napokat, és adja meg a karbantartási időszak kezdési óráját az egyes napokhoz, majd kattintson az **OK**gombra. Vegye figyelembe, hogy a karbantartási időszak ideje UTC.Note that the maintenance window time is in UTC. 

A frissítések alapértelmezett és minimális karbantartási időszaka öt óra. Ez az érték nem konfigurálható az Azure Portalon, de `MaintenanceWindow` konfigurálhatja a PowerShell ben a [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) parancsmag paraméterével. További információt az Ütemezett frissítések kezelése a PowerShell, a CLI vagy más felügyeleti eszközök használatával című témakörben talál?

## <a name="schedule-updates-faq"></a>Frissítések ütemezése – gyakori kérdések
* [Mikor történik a frissítés, ha nem használom az ütemezési frissítések funkciót?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Milyen típusú frissítések et hajtottak végre az ütemezett karbantartási időszak alatt?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Mikor történik a frissítés, ha nem használom az ütemezési frissítések funkciót?
Ha nem ad meg karbantartási időszakot, a frissítések bármikor elláthatók.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Milyen típusú frissítések et hajtottak végre az ütemezett karbantartási időszak alatt?
Csak a Redis-kiszolgáló frissítései lesznek az ütemezett karbantartási időszakban. A karbantartási időszak nem vonatkozik az Azure-frissítésekre vagy a virtuális gép operációs rendszer frissítéseire.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kezelhetem az ütemezett frissítéseket a PowerShell, a CLI vagy más felügyeleti eszközök használatával?
Igen, az ütemezett frissítéseket a következő PowerShell-parancsmagok segítségével kezelheti:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [Új-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [Új-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

## <a name="next-steps"></a>További lépések
* Fedezzen fel további [Azure-gyorsítótárat a Redis prémium szintű funkcióihoz.](cache-premium-tier-intro.md)

