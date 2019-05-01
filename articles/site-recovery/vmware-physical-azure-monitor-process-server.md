---
title: Az Azure Site Recovery folyamatkiszolgáló figyelése
description: Ez a cikk ismerteti az Azure Site Recovery folyamatkiszolgáló figyelése.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/24/2019
ms.author: rayne
ms.openlocfilehash: 5fac369f15edb3ef0be31d3dc7d7434104c18dfe
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928167"
---
# <a name="monitor-the-process-server"></a>A folyamatkiszolgáló figyelése

Ez a cikk bemutatja, hogyan figyelheti a [Site Recovery](site-recovery-overview.md) folyamatkiszolgáló.

- A folyamatkiszolgáló szolgál a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA vész-helyreállítási beállításakor.
- Alapértelmezés szerint a folyamatkiszolgáló a konfigurációs kiszolgálón fut. Telepíti a rendszer alapértelmezés szerint használt konfigurációs kiszolgáló telepítése.
- Igény szerint méretezhetőség és leíró számtalan replikált gépek és a replikációs forgalom nagyobb mennyiségű telepíthet további, a horizontális felskálázási folyamatkiszolgáló.

[További](vmware-physical-azure-config-process-server-overview.md) a szerepkör és a folyamat kiszolgálók üzembe helyezésével kapcsolatos.

## <a name="monitoring-overview"></a>Monitorozás áttekintése

Mivel a folyamatkiszolgáló sok szerepkörök, különösen a replikált adatok gyorsítótárazását, tömörítését és átvitele az Azure-bA fontos folyamat kiszolgáló állapotának folyamatos figyelése.

Létezik néhány gyakran folyamat kiszolgáló teljesítményét befolyásoló helyzetben. Teljesítményt érintő problémákat egymásra épülő hatással lesz a virtuális gép állapota, végül a folyamat és a replikált gépek küld kritikus állapotba. Esetek a következők:

- Virtuális gépek nagy mennyiségű folyamatkiszolgáló, hamarosan eléri vagy meghaladja az ajánlott korlátozások használja.
- A folyamatkiszolgáló virtuális gépek a nagy forgalom sebessége rendelkezik.
- Virtuális gépek és a folyamatkiszolgáló közötti hálózati átviteli sebessége nem elegendő a replikációs adatok feltöltése a folyamatkiszolgálónak.
- A folyamatkiszolgáló és az Azure közötti hálózati átviteli sebessége nem elegendő feltölteni a replikációs adatok a folyamatkiszolgálóról az Azure-bA.

Minden, a problémák hatással lehetnek a helyreállításipont-célkitűzés (RPO) virtuális gépek. 

**miért?** Mivel a virtuális gépen, hogy egy közös pont az összes lemez egy helyreállítási pont létrehozása egy virtuális géphez van szükség. Ha egyik lemezhez tartozik egy nagy forgalom sebessége, replikálás lassú, vagy a folyamatkiszolgáló nem optimális, a következőkre hat hatékonyan hogyan jönnek létre helyreállítási pontokat.

## <a name="monitor-proactively"></a>Proaktív monitorozás

A folyamatkiszolgáló problémák elkerülése érdekében fontos:

- Folyamatkiszolgálók segítségével konkrét követelmények megértéséhez [kapacitás és a méretezési útmutató](site-recovery-plan-capacity-vmware.md#capacity-considerations), és ellenőrizze, hogy a folyamat-kiszolgálókat azért helyezik üzembe, és futó javaslatainak megfelelő lépéseket.
- Riasztások figyelése és hibáinak elhárítása a bekövetkezésük, hatékony működését folyamatkiszolgálók tartani.


## <a name="process-server-alerts"></a>Folyamatkiszolgálói riasztások

A folyamatkiszolgáló állapotriasztások, az alábbi táblázatban összefoglalt számos állít elő.

**Riasztás típusa** | **Részletek**
--- | ---
![Kifogástalan][green] | A folyamatkiszolgáló csatlakoztatva és működik megfelelően.
![Figyelmeztetés][yellow] | Processzor kihasználtsága > 80 % az elmúlt 15 percben
![Figyelmeztetés][yellow] | Memória használati > 80 % az elmúlt 15 percben
![Figyelmeztetés][yellow] | Gyorsítótár mappa szabad terület < 30 % az elmúlt 15 percben
![Figyelmeztetés][yellow] | Folyamatkiszolgáló szolgáltatásai az elmúlt 15 percben nem fut
![Kritikus][red] | Processzor kihasználtsága > 95 % az elmúlt 15 percben
![Kritikus][red] | Memória használati > 95 % az elmúlt 15 percben
![Kritikus][red] | Gyorsítótár mappa szabad terület < 25 %-os az elmúlt 15 percben
![Kritikus][red] | Nem érkezett szívverés a folyamatkiszolgálóról 15 percig.

![tábla kulcsa](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> A folyamatkiszolgáló általános állapotának a legrosszabb generált riasztások alapján történik.



## <a name="monitor-process-server-health"></a>A figyelő folyamat kiszolgáló állapota

A folyamatkiszolgálók állapotát módon figyelheti: 

1. A replikációs állapotot és a replikált gépről, és a folyamatkiszolgáló, a tárolóban állapotának monitorozásához > **replikált elemek**, kattintson a figyelni kívánt gépen.
2. A **replikálás állapotának**, nyomon követheti a virtuális gép állapota. Kattintson az állapot részletes elemzést, a hiba részletei.

    ![A virtuális gép irányítópult folyamat kiszolgáló állapota](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. A **folyamat kiszolgáló állapota**, figyelheti a folyamatkiszolgáló állapotát. Részletezés részleteiről.

    ![Folyamatkiszolgáló adatait a virtuális gép irányítópult](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Egészségügyi is használatával figyelhetők a grafikus ábrázolását a virtuális gép oldalon.
    - Horizontális felskálázási folyamatkiszolgáló lesz kiemelve a narancssárga, ha vannak társítva figyelmeztetéseket, és piros, ha bármely kritikus fontosságú problémáit. 
    - Ha az alapértelmezett üzembe helyezés a folyamatkiszolgáló fut a konfigurációs kiszolgálón, majd a konfigurációs kiszolgáló kiemeli.
    - Szeretne lehatolni, kattintson a konfigurációs kiszolgálón vagy folyamatkiszolgálón. Vegye figyelembe az esetleges problémákat, és szervizelési javaslatokkal szolgálni.

Is figyelemmel kísérheti a tárban lévő kiszolgálók feldolgozni **Site Recovery-infrastruktúra**. A **a Site Recovery-infrastruktúra kezelése**, kattintson a **konfigurációs kiszolgálók**. Válassza ki a konfigurációs kiszolgáló társítva a folyamatkiszolgáló és a részletezés le be a folyamatkiszolgáló adatait.


## <a name="next-steps"></a>További lépések

- Ha rendelkezik ilyennel feldolgozni a kiszolgálók problémák, hajtsa végre a [hibaelhárítási útmutató](vmware-physical-azure-troubleshoot-process-server.md)
- Ha további segítségre van szüksége, tegye közzé a kérdését a [Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
