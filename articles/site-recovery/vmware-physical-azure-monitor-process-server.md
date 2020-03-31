---
title: Az Azure site recovery folyamatkiszolgálójának figyelése
description: Ez a cikk bemutatja, hogyan figyelheti az Azure Site Recovery folyamatkiszolgálóját a VMware VM/physical server vész-helyreállítási
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257068"
---
# <a name="monitor-the-process-server"></a>A folyamatkiszolgáló figyelése

Ez a cikk a [Site Recovery](site-recovery-overview.md) folyamatkiszolgálójának figyelését ismerteti.

- A folyamatkiszolgáló akkor használatos, amikor beállítja a helyszíni VMware virtuális gépek és fizikai kiszolgálók vészutáni helyreállítását az Azure-ba.
- Alapértelmezés szerint a folyamatkiszolgáló a konfigurációs kiszolgálón fut. A konfigurációs kiszolgáló telepítésekor alapértelmezés szerint telepítve van.
- A replikált gépek nagyobb számának és nagyobb mennyiségű replikációs forgalomnak a méretezéséhez és kezeléséhez további, kibővített folyamatkiszolgálókat is üzembe helyezhet.

[További információ](vmware-physical-azure-config-process-server-overview.md) a folyamatkiszolgálók szerepéről és központi telepítéséről.

## <a name="monitoring-overview"></a>Monitorozás áttekintése

Mivel a folyamatkiszolgáló olyan sok szerepkörrel rendelkezik, különösen a replikált adatgyorsítótárazás, tömörítés és az Azure-ba való átvitel során, fontos, hogy folyamatosan figyelje a folyamatkiszolgáló állapotát.

Számos olyan helyzet van, amely gyakran befolyásolja a folyamatkiszolgáló teljesítményét. A teljesítményt érintő problémák lépcsőzetes hatást gyakorolnak a virtuális gép állapotára, és végül kritikus állapotba kényszerítik a folyamatkiszolgálót és a replikált gépeket. A helyzetek a következők:

- A virtuális gépek nagy száma folyamatkiszolgálót használ, megközelítve vagy túllépve az ajánlott korlátozásokat.
- A folyamatkiszolgálót használó virtuális gépek magas lemorzsolódási aránnyal rendelkeznek.
- A virtuális gépek és a folyamatkiszolgáló közötti hálózati átviteli-kapcsolat nem elegendő a replikációs adatok feltöltéséhez a folyamatkiszolgálóra.
- A folyamatkiszolgáló és az Azure közötti hálózati átviteli nem elegendő a replikációs adatok feltöltéséhez a folyamatkiszolgálóról az Azure-ba.

Ezek a problémák hatással lehetnek a helyreállítási pont célja (RPO) a virtuális gépek. 

**Hogy miért?** Mivel egy virtuális gép helyreállítási pontjának létrehozása megköveteli, hogy a virtuális gép összes lemeze közös ponttal rendelkezik. Ha egy lemez nagy lemorzsolódási aránnyal rendelkezik, a replikáció lassú, vagy a folyamatkiszolgáló nem optimális, hatással van a helyreállítási pontok létrehozásának hatékony módjára.

## <a name="monitor-proactively"></a>Proaktív monitorozás

A folyamatkiszolgálóval kapcsolatos problémák elkerülése érdekében fontos, hogy:

- Ismerje meg a [kapacitást és méretezési útmutatást](site-recovery-plan-capacity-vmware.md#capacity-considerations)használó folyamatkiszolgálókra vonatkozó speciális követelményeket, és győződjön meg arról, hogy a folyamatkiszolgálók telepítése és futtatása a javaslatoknak megfelelően történt.
- A folyamatkiszolgálók hatékony működésének érdekében figyelheti a riasztásokat, és elháríthatja azok bekövetkezésének megfelelő enuszsát.


## <a name="process-server-alerts"></a>Kiszolgálói riasztások feldolgozása

A folyamatkiszolgáló számos állapotriasztást hoz létre, amelyeket az alábbi táblázat foglal össze.

**Riasztástípus** | **Részletek**
--- | ---
![Kifogástalan][green] | A folyamatkiszolgáló csatlakoztatva van és kifogástalan állapotú.
![Figyelmeztetés][yellow] | A CPU kihasználtsága az elmúlt 15 percben 80%->
![Figyelmeztetés][yellow] | A memóriahasználat 80%-kal > az elmúlt 15 percben
![Figyelmeztetés][yellow] | A gyorsítótármappa szabad területe az elmúlt 15 percben < 30%-os
![Figyelmeztetés][yellow] | A Site Recovery öt percenként figyeli a függőben lévő/kimenő adatokat, és úgy becsüli, hogy a folyamatkiszolgáló gyorsítótárában lévő adatok 30 percen belül nem tölthetők fel az Azure-ba.
![Figyelmeztetés][yellow] | A folyamatkiszolgáló-szolgáltatások nem futnak az elmúlt 15 percben
![Kritikus][red] | A CPU kihasználtsága > 95% az elmúlt 15 percben
![Kritikus][red] | A memóriahasználat 95%-> az elmúlt 15 percben
![Kritikus][red] | A gyorsítótármappa szabad területe az elmúlt 15 percben 25%-<
![Kritikus][red] | A Site Recovery öt percenként figyeli a függőben lévő/kimenő adatokat, és úgy becsüli, hogy a folyamatkiszolgáló gyorsítótárában lévő adatok 45 percen belül nem tölthetők fel az Azure-ba.
![Kritikus][red] | Nincs szívverés a folyamatkiszolgálótól 15 percig.

![Táblakulcs](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> A folyamatkiszolgáló általános állapota a generált legrosszabb riasztáson alapul.



## <a name="monitor-process-server-health"></a>Folyamatkiszolgáló állapotának figyelése

A folyamatkiszolgálók állapotát a következőképpen figyelheti: 

1. A replikált gép és a folyamatkiszolgáló replikációs állapotának és állapotának figyeléséhez a tárolóban > **replikált elemek**tárolójában kattintson a figyelni kívánt gépre.
2. A **replikáció állapota**, figyelheti a virtuális gép állapotát. Kattintson az állapotra a hiba részleteinek részletezéséhez.

    ![Kiszolgáló állapotának feldolgozása a virtuális gép irányítópultján](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. A **Folyamatkiszolgáló állapota**területen figyelheti a folyamatkiszolgáló állapotát. Részletezze a részleteket.

    ![Kiszolgáló részleteinek feldolgozása a virtuális gép irányítópultján](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Az állapot is figyelhető a virtuális gép oldalán található grafikus ábrázolással.
    - A horizontális felskálázási folyamatkiszolgáló narancssárga színnel lesz kiemelve, ha figyelmeztetések vannak hozzátársítva, és piros, ha kritikus problémák merülnek fel. 
    - Ha a folyamatkiszolgáló a konfigurációs kiszolgáló alapértelmezett központi telepítésében fut, akkor a konfigurációs kiszolgáló ennek megfelelően ki lesz emelve.
    - A részletezéshez kattintson a konfigurációs kiszolgálóra vagy a folyamatkiszolgálóra. Vegye figyelembe a problémákat és a javítási javaslatokat.

A **webhely-helyreállítási infrastruktúra**csoportban a tárolóban lévő folyamatkiszolgálókat is figyelheti. A **Hely-helyreállítási infrastruktúra kezelése**csoportban kattintson a **Konfigurációs kiszolgálók**elemre. Válassza ki a folyamatkiszolgálóhoz társított konfigurációs kiszolgálót, és részletezze a folyamatkiszolgáló részleteit.


## <a name="next-steps"></a>További lépések

- Ha bármilyen folyamatszerverrel kapcsolatos problémája van, kövesse [hibaelhárítási útmutatónkat](vmware-physical-azure-troubleshoot-process-server.md)
- Ha további segítségre van szüksége, tegye fel kérdését az [Azure Site Recovery fórumon.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
