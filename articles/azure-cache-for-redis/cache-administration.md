---
title: Felügyelete az Azure Cache redis |} A Microsoft Docs
description: Ismerje meg, hogyan hajthat végre felügyeleti feladatokat, például a frissítések újraindítást és ütemezése az Azure Cache redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: b75a2faa2030fc343cbabb17eb37b63c9ea34f70
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232381"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>A Redis Azure Cache felügyelete
Ez a témakör ismerteti, hogyan lehet felügyeleti feladatok végrehajtásához [újraindítása](#reboot) és [frissítések ütemezése](#schedule-updates) az Azure Cache Redis-példány számára.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Újraindítás
A **újraindítás** panel lehetővé teszi, hogy a gyorsítótár egy vagy több csomópont újraindítását. Az újraindítás funkció lehetővé teszi, hogy tesztelje az alkalmazást a rugalmassághoz, ha sikertelen. a gyorsítótár-csomópont.

![Újraindítás](./media/cache-administration/redis-cache-administration-reboot.png)

Válassza ki a csomópontokat, indítsa újra, és kattintson az **újraindítás**.

![Újraindítás](./media/cache-administration/redis-cache-reboot.png)

Ha egy prémium szintű gyorsítótár-fürtözés engedélyezve van, kiválaszthatja, melyik szegmensben indítani a gyorsítótár.

![Újraindítás](./media/cache-administration/redis-cache-reboot-cluster.png)

Indítsa újra a gyorsítótár egy vagy több csomópontot, válassza ki a kívánt csomópontokat, és kattintson a **újraindítás**. Ha egy prémium szintű gyorsítótár a fürtözés engedélyezve van, kattintson az újraindítás, majd kattintson a kívánt szegmensek **újraindítás**. Néhány perc múlva a kiválasztott csomópontok újraindítását, és a rendszer ismét elérhető néhány perc múlva.

Az ügyfélalkalmazások gyakorolt melyik csomópontokon függően változik, hogy újraindítja.

* **Fő** – Ha a fő csomópontot újraindítják, Azure Cache redis átadja a feladatokat a replika csomópontot, és elősegíti a fő. A feladatátvétel során valószínűleg egy rövid időszak, amelyben a kapcsolatok sikertelen lehet a gyorsítótárba.
* **Az alárendelt** – Ha az alárendelt csomópont újraindítása után, általában ez nincs hatással a gyorsítótárazási ügyfelek számára.
* **Fő és a kisegítő** – Ha gyorsítótár-csomópontok indulnak újra, a gyorsítótárban lévő összes adat elveszik, mind a gyorsítótárhoz kapcsolatok sikertelen lesz, amíg az elsődleges csomópont visszatér online állapotba. Ha konfigurálta a [adatmegőrzés](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés visszaállítása a gyorsítótár ismét online elérhető, de a legutóbbi biztonsági mentés óta bekövetkezett gyorsítótár írási elvesznek.
* **Csomópontok egy prémium szintű gyorsítótár engedélyezve fürtözési** – Ha egy prémium szintű gyorsítótár legalább egy csomópontján fürtözési engedélyezve van, indítsa újra a viselkedés a kiválasztott csomópontok pedig ugyanaz, mint amikor, indítsa újra a megfelelő csomópont vagy egy nem fürtözött Cache.

> [!IMPORTANT]
> Újraindítás most már az összes tarifacsomag érhető el.
> 
> 

## <a name="reboot-faq"></a>Újraindítás – gyakori kérdések
* [Melyik csomópont kell újraindítani az alkalmazást tesztelheti?](#which-node-should-i-reboot-to-test-my-application)
* [A gyorsítótár törlése ügyfélkapcsolatok indíthatja újra?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Elveszítek adatokat a gyorsítótárból újraindítás ehhez?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [A PowerShell, a parancssori felület vagy a más felügyeleti eszközök a gyorsítótár indíthatja újra?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Milyen díjszabási szint esetében, használhatja, hogy az újraindítás funkciót?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Melyik csomópont kell újraindítani az alkalmazást tesztelheti?
Ha tesztelni szeretné a gyorsítótár az elsődleges csomópont meghibásodása elleni az alkalmazás rugalmasságát, indítsa újra a **fő** csomópont. Ha tesztelni szeretné a másodlagos csomópont meghibásodása elleni az alkalmazás rugalmasságát, indítsa újra a **alárendelt** csomópont. Ha tesztelni szeretné a gyorsítótár teljes leállása ellen az alkalmazás rugalmasságát, újraindítás **mindkét** csomópontok.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>A gyorsítótár törlése ügyfélkapcsolatok indíthatja újra?
Igen, ha újraindítja a gyorsítótár minden olyan ügyfélkapcsolatot törlődnek. Rendszer újraindítása lehet hasznos, abban az esetben, ahol minden olyan ügyfélkapcsolatot el is használta egy logikai hiba vagy az ügyfélalkalmazás hibája miatt. Minden tarifacsomag különböző rendelkezik [ügyfél kapcsolat korlátai](cache-configure.md#default-redis-server-configuration) számára a különböző méretű, és egyszer ezek a korlátok elérésekor a rendszer elfogadja az ügyfél több kapcsolat nem. A gyorsítótár újraindítása lehetővé teszi minden olyan ügyfélkapcsolatot törölje.

> [!IMPORTANT]
> Ha újraindítja a gyorsítótár ügyfélkapcsolatok jelet, StackExchange.Redis automatikusan újracsatlakozik követően a Redis-csomópont újra online állapotba kerül. Ha a mögöttes probléma továbbra is fennáll, az ügyfélkapcsolatok továbbra is fel lehet használni.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Elveszítek adatokat a gyorsítótárból újraindítás ehhez?
Ha mindkét újraindítja a **fő** és **alárendelt** csomópontok, minden adatot a gyorsítótárban (vagy használatakor a prémium szintű gyorsítótár fürtözés engedélyezve van a szegmensben lévő) elvész. Ha konfigurálta a [adatmegőrzés](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés helyre fog állni a gyorsítótár ismét online elérhető, de a gyorsítótár, amely után a biztonsági másolat készült történt írási elvesznek.

Ha csak az egyik csomópont valamelyik indítsa újra, általában adatvesztés nem történik, de továbbra is lehet. A például, ha a fő csomópont újraindul, és a egy gyorsítótár-írás folyamatban van, a gyorsítótár-írás származó adatok elvesznek. Az adatvesztést egy másik eset lenne, ha egy csomópont újraindítása, és a többi csomópont történik, egyszerre egy hiba miatt leáll. Ennek lehetséges okai az adatvesztést kapcsolatos további információkért lásd: [Mi történt a redis adataimat?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>A PowerShell, a parancssori felület vagy a más felügyeleti eszközök a gyorsítótár indíthatja újra?
Igen, PowerShell-Útmutató: [újraindítását az Azure Cache redis](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Milyen díjszabási szint esetében, használhatja, hogy az újraindítás funkciót?
Újraindítás minden árképzési szint esetében érhető el.

## <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezése** panel lehetővé teszi, hogy a prémium szintű gyorsítótár egy karbantartási időszakot. Ha a karbantartási időszak van megadva, a Redis-kiszolgáló frissítéseit ezen időszak alatt készülnek el. 

> [!NOTE] 
> A karbantartási időszak csak azokra a Redis-kiszolgáló frissítéseit, és nem segítségével bármely Azure-frissítések, vagy frissíti a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének.
> 
> 

![Frissítések ütemezése](./media/cache-administration/redis-schedule-updates.png)

Adja meg a karbantartási időszak, ellenőrizze a kívánt napok és adja meg a karbantartási időszak kezdő órája minden nap, és kattintson **OK**. Vegye figyelembe, hogy a karbantartási ablak időpontja (UTC). 

A frissítések az alapértelmezett és minimális karbantartási időszak 5 óra. Ez az érték nem konfigurálható az Azure Portalról, de a PowerShell használatával konfigurálhatja a `MaintenanceWindow` paraméterében a [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) parancsmagot. További információkért lásd: [felügyelhető a PowerShell, a parancssori felület vagy a más felügyeleti eszközök ütemezett frissítések?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)


## <a name="schedule-updates-faq"></a>Frissítések ütemezése – Gyakori kérdések
* [Ha ehhez frissítések fordulhat elő, ha az ütemezés frissítések szolgáltatás nem használom?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Milyen típusú frissítéseket az ütemezett karbantartási időszakban menjenek?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [A PowerShell, a parancssori felület vagy a más felügyeleti eszközök ütemezett frissítések lehet kezelni?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Milyen díjszabás szint esetében, használhatja, hogy az ütemezés frissítések funkciót?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Ha ehhez frissítések fordulhat elő, ha az ütemezés frissítések szolgáltatás nem használom?
Ha nem adja meg a karbantartási időszak, frissítések lehet kapcsolódni a tetszőleges időpontban.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Milyen típusú frissítéseket az ütemezett karbantartási időszakban menjenek?
Csak a Redis-kiszolgáló frissítéseket az ütemezett karbantartási időszakban menjenek végbe. A karbantartási időszak nem vonatkozik az Azure vagy a virtuális gép operációsrendszer-frissítéseket.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>A PowerShell, a parancssori felület vagy a más felügyeleti eszközök felügyelt ütemezett frissítések lehetőségeket?
Igen, az ütemezett frissítések a következő PowerShell-parancsmagok használata kezelheti:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Milyen díjszabás szint esetében, használhatja, hogy az ütemezés frissítések funkciót?
A **frissítések ütemezése** szolgáltatás csak a prémium tarifacsomagban érhető el.

## <a name="next-steps"></a>További lépések
* További részletek [Azure Cache Redis prémium szint](cache-premium-tier-intro.md) funkciókat.

