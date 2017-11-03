---
title: "Azure Redis Cache felügyelete |} Microsoft Docs"
description: "Útmutató az Azure Redis Cache felügyeleti feladatokhoz, mint az újraindítás és ütemezés frissítések végrehajtásához"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 07/05/2017
ms.author: sdanie
ms.openlocfilehash: 3352fec59d7dfbfab9b0416992a60f11d0ec2402
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-administer-azure-redis-cache"></a>Azure Redis Cache felügyelete
Ez a témakör ismerteti, hogyan hajthat végre, mint a felügyeleti feladatokat [újraindítás](#reboot) és [frissítések ütemezése](#schedule-updates) az Azure Redis Cache-példányok.

## <a name="reboot"></a>Újraindítás
A **újraindítás** panel lehetővé teszi, hogy a gyorsítótár egy vagy több csomópont újraindítását. A rendszer újraindítása funkció lehetővé teszi a rugalmasságot az alkalmazás tesztelése, ha sikertelen. a gyorsítótár-csomópont.

![Újraindítás](./media/cache-administration/redis-cache-administration-reboot.png)

Válassza ki a csomópontok újraindítását, és kattintson a **újraindítás**.

![Újraindítás](./media/cache-administration/redis-cache-reboot.png)

Ha a prémium szintű gyorsítótár fürtözési engedélyezve van, mely szilánkok újraindítását a gyorsítótár választhatja meg.

![Újraindítás](./media/cache-administration/redis-cache-reboot-cluster.png)

Indítsa újra a gyorsítótár egy vagy több csomópontot, válassza ki a kívánt csomópontokat, majd **újraindítás**. Ha a prémium szintű gyorsítótár fürtözési engedélyezve van, jelölje ki a újraindul, majd kattintson a kívánt szilánkok **újraindítás**. Néhány perc múlva a kiválasztott csomópontok újraindítás, és amelyeket biztonsági online néhány percen belül.

Ügyfélalkalmazások gyakorolt hatás csomópontok attól függ, hogy újraindítja.

* **Fő** – Ha a fő csomópont újraindított, az Azure Redis Cache sikertelen keresztül a replika csomópontra, és elősegíthető a fő. A feladatátvétel során lehet egy rövid időszak, amelyben a kapcsolatok sikertelenek lehetnek a gyorsítótárba.
* **Az alárendelt** – Ha az alárendelt csomópont újraindítása után, általában nincs hatással a gyorsítótárazási ügyfelek számára.
* **Fő és a kisegítő** -amikor gyorsítótár-csomópontok, minden adat elveszik a gyorsítótárban, mind a gyorsítótárba kapcsolatok nem indulnak el az elsődleges csomópont ismét online elérhető lesz. Ha már konfigurált [adatmegőrzés](cache-how-to-premium-persistence.md), a legutóbbi biztonsági másolat visszaállítása a gyorsítótár ismét online elérhető, de a gyorsítótár írt, amely a legutóbbi biztonsági mentés óta történt elvesznek.
* **A prémium csomópontja gyorsítótár fürtözési engedélyezve** – Ha egy vagy több csomópont egy prémium szintű gyorsítótár fürtözési engedélyezve van, indítsa újra a kijelölt csomópontok a rendszer nem ugyanaz, mint amikor indítsa újra a megfelelő csomópont vagy a csomópontok nem fürtözött gyorsítótár.

> [!IMPORTANT]
> Minden tarifacsomagok Újraindítás most érhető el.
> 
> 

## <a name="reboot-faq"></a>Indítsa újra a gyakori kérdések
* [Melyik csomópontján kell indítania az alkalmazás teszteléséhez?](#which-node-should-i-reboot-to-test-my-application)
* [Újraindítás a gyorsítótár törli az ügyfélkapcsolatokat is?](#can-i-reboot-the-cache-to-clear-client-connections)
* [I adat elvész a gyorsítótárból, ha a számítógép újraindítása a teendő?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Újraindíthatja a gyorsítótár, a PowerShell, a parancssori felületen vagy a más felügyeleti eszközökhöz?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Milyen árképzési tiers az újraindítás funkciót használhatja a?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Melyik csomópontján kell indítania az alkalmazás teszteléséhez?
A rugalmasság, a gyorsítótár az elsődleges csomópont meghibásodása elleni az alkalmazás teszteléséhez indítsa újra a **fő** csomópont. A rugalmasság, a másodlagos csomópont meghibásodása elleni az alkalmazás teszteléséhez indítsa újra a **alárendelt** csomópont. A rugalmasság, a gyorsítótár teljes leállása ellen az alkalmazás teszteléséhez indítsa újra a **mindkét** csomópontok.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Újraindítás a gyorsítótár törli az ügyfélkapcsolatokat is?
Igen, ha újraindítja a gyorsítótár törlődik minden ügyfél-kommunikációhoz. Rendszer újraindítása lehet hasznos, abban az esetben, ha minden ügyfél-kommunikációhoz használt egy logikai hiba vagy az ügyfélalkalmazás hibája miatt. Minden tarifacsomag különböző rendelkezik [ügyfél kapcsolati határt](cache-configure.md#default-redis-server-configuration) a különböző méretű, és egyszer a működés felső korlátjának érhető el, nincs további kapcsolatok is elfogadhatóak. A gyorsítótár újraindítás biztosítja az jelet minden ügyfél-kommunikációhoz.

> [!IMPORTANT]
> Ha újraindítja a gyorsítótár törli az ügyfélkapcsolatokat, StackExchange.Redis automatikusan újracsatlakozik után a Redis-csomópont újra online állapotba kerül. Az alapul szolgáló hiba nem szűnik meg, ha az ügyfélkapcsolatokat továbbra is fel lehet használni.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>I adat elvész a gyorsítótárból, ha a számítógép újraindítása a teendő?
Ha mindkét újraindítja a **fő** és **alárendelt** csomópontok, minden adatot a gyorsítótárban (vagy adott shard használata egy prémium szintű gyorsítótár fürtözési engedélyezve) elvész. Ha már konfigurált [adatmegőrzés](cache-how-to-premium-persistence.md), a legutóbbi biztonsági mentés vissza lesznek állítva a gyorsítótár ismét online elérhető, de bármilyen gyorsítótár írás után a biztonsági másolat készült előfordult elvesznek.

Ha csak egy csomópont újraindul, legyen általában adatvesztés, de továbbra is lehet. Ha például a fő csomópont újraindítása után, és a gyorsítótár-írási van folyamatban, a gyorsítótár-írási adatait érték elveszett. Egy másik forgatókönyvet az adatvesztés lenne, ha egy csomópont újraindítása, és a többi csomópont történik egyszerre egy hiba miatt leáll. További információ a adatvesztés lehetséges okai: [Mi történt a Redis-adataimhoz?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md)

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Újraindíthatja a gyorsítótár, a PowerShell, a parancssori felületen vagy a más felügyeleti eszközökhöz?
Igen, a PowerShell útmutatásért lásd: [újraindítását a Redis gyorsítótár](cache-howto-manage-redis-cache-powershell.md#to-reboot-a-redis-cache).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Milyen árképzési tiers az újraindítás funkciót használhatja a?
Újraindítás összes tarifacsomagok érhető el.

## <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezése** panel lehetővé teszi, hogy a karbantartási időszak a Premium szint gyorsítótárhoz. A karbantartási időszak megadása esetén a Redis-kiszolgáló változásainak végrehajtott ezen időszak alatt történjen. 

> [!NOTE] 
> A karbantartási időszak csak a kiszolgálói frissítések Redis vonatkozik, és nem az összes Azure-bA frissít, vagy frissíti az operációs rendszer, a gyorsítótár üzemeltető virtuális gépek.
> 
> 

![Frissítések ütemezése](./media/cache-administration/redis-schedule-updates.png)

Adja meg a karbantartási időszak, ellenőrizze a kívánt napok, és adja meg a karbantartási időszak kezdő időpontja minden nap, és kattintson **OK**. Vegye figyelembe, hogy a karbantartási ablak időpontja UTC szerint. 

> [!NOTE]
> Az alapértelmezett karbantartási idejében frissítések öt órát. Ez az érték nem konfigurálható az Azure-portálon, de a PowerShell használatával konfigurálhatja a `MaintenanceWindow` paramétere a [New-AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry) parancsmag. További információkért lásd: [kezelhetők a PowerShell, a parancssori felületen vagy a más felügyeleti eszközökhöz ütemezett frissítés?](#can-i-manage-scheduled-updates-using-powershell-cli-or-other-management-tools)
> 
> 

## <a name="schedule-updates-faq"></a>A frissítések ütemezése – Gyakori kérdések
* [Ha tegye frissítések fordulhat elő, ha az ütemezés frissítések szolgáltatás nem használom?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Milyen típusú frissítéseket az ütemezett karbantartási időszak alatt végrehajtott?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [A PowerShell, a parancssori felületen vagy a más felügyeleti eszközökhöz ütemezett frissítések lehet kezelni?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Milyen árképzési tiers az ütemezés frissítések funkciót használhatja a?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Ha tegye frissítések fordulhat elő, ha az ütemezés frissítések szolgáltatás nem használom?
Ha nem adja meg a karbantartási időszak, bármikor frissítéseket is végezhető.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Milyen típusú frissítéseket az ütemezett karbantartási időszak alatt végrehajtott?
Csak Redis server frissítések válnak, az ütemezett karbantartási időszak alatt. A karbantartási időszak nem érvényes Azure-hírt vagy a virtuális gép operációs rendszert.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>A PowerShell, a parancssori felületen vagy a más felügyeleti eszközökhöz felügyelt ütemezett frissítések lehetőségeket?
Igen, az ütemezett frissítések a következő PowerShell-parancsmagok használatával kezelheti:

* [Get-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/get-azurermrediscachepatchschedule)
* [Új AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/new-azurermrediscachepatchschedule)
* [Új AzureRmRedisCacheScheduleEntry](/powershell/module/azurerm.rediscache/new-azurermrediscachescheduleentry)
* [Remove-AzureRmRedisCachePatchSchedule](/powershell/module/azurerm.rediscache/remove-azurermrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Milyen árképzési tiers az ütemezés frissítések funkciót használhatja a?
A **frissítések ütemezése** szolgáltatás csak akkor áll rendelkezésre a prémium tarifacsomag.

## <a name="next-steps"></a>Következő lépések
* Fedezze fel több [Azure Redis Cache prémium csomagban](cache-premium-tier-intro.md) szolgáltatásokat.

