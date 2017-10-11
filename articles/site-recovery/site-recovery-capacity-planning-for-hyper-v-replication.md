---
title: A Hyper-V capacity Planner futtassa a Site Recovery |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan futtathat a Hyper-V capacity Planner az Azure Site Recovery
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: nisoneji
ms.openlocfilehash: 272b5abb5e6451164ca7900dda399b6aac65f986
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>A Hyper-V capacity Planner futtassa a Site Recovery

Az Azure Site Recovery üzembe helyezése részeként akkor ki kell deríteni a replikáció és a sávszélesség-követelményekkel. A Hyper-V capacity Planner a Site Recovery segítségével hajthatja végre, a Hyper-V Virtuálisgép-replikációt.

A cikkből megtudhatja, hogyan futtathat a Hyper-V capacity Planner. Ez az eszköz az adatokkal együtt használandó [a Site Recovery kapacitástervezési](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Előkészületek
Az eszközt futtatja az elsődleges hely Hyper-V kiszolgáló vagy fürt csomópontja. Az eszköz futtatásához a Hyper-V-kiszolgálóknak kell:

* Operációs rendszer: Windows Server 2012 vagy 2012 R2
* Memória: 20 MB (minimum)
* CPU: 5 százalékkal terhelés (minimum)
* Szabad lemezterület: 5 MB (minimum)

Az eszköz futtatása előtt elő kell készíteni az elsődleges hely. Ha közötti replikál két helyszíni hely és a sávszélesség ellenőrizni kívánja, elő kell készíteni a c kiszolgálót is.

## <a name="step-1-prepare-the-primary-site"></a>1. lépés: Felkészülés az elsődleges hely

1. Győződjön meg az elsődleges hely, a Hyper-V virtuális gépeket szeretne replikálni, és a Hyper-V gazdagépek/fürtök amelyen fontosságúak található összes listáját. Az eszköz több önálló gazdagépen vagy egy fürtön, de nem mindkettőt együtt is futtatható. Azt is meg minden egyes operációs rendszerhez, a Hyper-V kiszolgálók információt az alábbiak szerint kell gyűjteni függetlenül futtatni:

   * Windows Server 2012 önálló kiszolgálók
   * Windows Server 2012-fürtök
   * Windows Server 2012 R2 önálló kiszolgálók
   * Windows Server 2012 R2-fürt
2. A Hyper-V-gazdagépek és fürtök a WMI távoli hozzáférés engedélyezése. Futtassa a parancsot minden egyes kiszolgáló vagy fürt, győződjön meg arról, hogy a tűzfal-szabályokat és a felhasználói engedélyek vannak beállítva:

        netsh firewall set service RemoteAdmin enable
3. Engedélyezze a teljesítmény figyelése kiszolgálói és fürtjei a következőképpen:

   * Nyissa meg a Windows tűzfal a **biztonságú** beépülő modul, és ezután engedélyezése a következő bejövő szabályok: **COM + hálózati hozzáférés (DCOM-IN)** és az összes szabály az **Távoli Eseménynapló Felügyeleti csoport**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>2. lépés: Készítse elő a kiszolgálót (a helyszíni replikáció)
Ha az Azure-bA replikál ehhez nem kell.

Javasoljuk a helyreállítási kiszolgálónak, egy Hyper-V állomás beállítása, hogy egy üres virtuális Gépet replikálni lehet, hogy ellenőrizze a sávszélesség.  Kihagyhatja ezt, de nem lehet majd a sávszélesség mérését, kivéve, ha ezt a teheti.

1. Ha szeretné-e egy fürt csomópontja használatára, mert a replika a Hyper-V replikaszervező konfigurálása:

   * A **Kiszolgálókezelő**, nyissa meg **Feladatátvevőfürt-kezelő**.
   * Csatlakozzon a fürthöz, jelölje ki a fürt nevét, és kattintson a **műveletek** > **szerepkör konfigurálása** a magas rendelkezésre állás varázsló megnyitásához.
   * A **Szerepkörválasztás**, kattintson a **Hyper-V Replikaszervező**. A varázslóban adja meg a **NetBIOS-név** és a **IP-cím** , a szolgáltatáskapcsolódási pont a fürt (ügyfél-hozzáférési pont) használható. A **Hyper-V Replikaszervező** lesz konfigurálva, ami azt eredményezi, hogy egy ügyfél-hozzáférési pont nevét, vegye figyelembe.
   * Győződjön meg arról, hogy a Hyper-V Replikaszervező szerepkör sikeresen online módba kerül, és a fürt minden csomópontjáról képes átvenni. Ehhez kattintson a jobb gombbal a szerepkör, mutasson a **áthelyezése**, és kattintson a **csomópont kiválasztása**. Válasszon ki egy csomópontot > **OK**.
   * Ha Tanúsítványalapú hitelesítés használata esetén győződjön meg arról, egyes fürtcsomópontok és az ügyfél-hozzáférési pontja minden telepített tanúsítvánnyal rendelkeznek.
2. Engedélyezze a replikakiszolgálón:

   * Fürt nyissa meg a hiba kezelőt, csatlakozzon a fürthöz, és kattintson a **szerepkörök** > Válassza szerepkör > **replikációs beállítások** > **replikaként fürt engedélyezése kiszolgáló**. A replika egy fürt használja, ha szüksége van a Hyper-V Replikaszervező szerepkör rendelkezik a fürtön, valamint az elsődleges helyen.
   * Egy önálló kiszolgáló nyissa meg a Hyper-V kezelőjét. Az a **műveletek** ablaktáblán kattintson a **Hyper-V beállítások** engedélyezni kívánja a kiszolgáló és a **replikációs konfiguráció** kattintson **engedélyezése ezen a számítógépen, mint egy Replikakiszolgáló**.
3. Hitelesítés beállítása:

   * A **hitelesítés és portok**, válassza ki, hogyan segítségével hitelesíti az elsődleges kiszolgáló és a hitelesítési portokat. Ha a tanúsítvány kattintson **tanúsítvány kiválasztása** kívánt fiók kiválasztásához. Kerberos használja, ha az elsődleges és a helyreállítási Hyper-V-gazdagépek ugyanabban a tartományban vagy megbízható tartományban. Tanúsítványokat használ a különböző tartományokhoz, vagy egy munkacsoport-telepítéshez.
   * A **engedélyezés és tárolás**, engedélyezése **bármely** hitelesített (elsődleges) kiszolgáló küldhet-e adatokat a replikakiszolgálónak.

     ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)
   * Futtatás **netsh http show servicestate**, ellenőrizze, hogy fut-e a figyelő a megadott protokoll/port:  
4. Állítsa be a tűzfalon. Hyper-V telepítése során a tűzfalszabályok forgalmat engedélyezi a az alapértelmezett port (HTTPS a 443-as, a 80-as Kerberos) jönnek létre. Engedélyezze a ezeket a szabályokat az alábbiak szerint:
  - Tanúsítványhitelesítés fürtön (443):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
  - Kerberos-hitelesítés (80-as) fürtön:``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
  - Tanúsítványhitelesítés önálló kiszolgálón:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
  - Kerberos-hitelesítés önálló kiszolgálón:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planner-tool"></a>3. lépés: A capacity Planner futtatása
Miután elő az elsődleges helyet, és állítsa be a helyreállítási kiszolgálónak, futtathatja az eszközt.

1. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39057) az eszközt a Microsoft Download Center.
2. Futtassa az eszközt az egyik elsődleges kiszolgálón (vagy az elsődleges fürt a csomópontok egy). Kattintson a jobb gombbal a .exe-fájl, és válassza a **Futtatás rendszergazdaként**.
3. A **megkezdése előtt**, adja meg, mennyi ideig adatainak gyűjtéséről. Azt javasoljuk, hogy az eszközt annak biztosításához, hogy adatokat képviselője éles órában futtatja. Csak próbál hálózati kapcsolatának ellenőrzése, ha csak egy percet hozhatja létre.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)
4. A **elsődleges hely adatai**, adja meg a kiszolgáló nevét vagy FQDN egy önálló gazdagép vagy fürt adhatja meg az ügyfél teljes Tartományneve pont fogadja el, a fürt nevét vagy a fürt bármely csomópontjára, és kattintson a **következő**. Az eszköz automatikusan észleli a futtató kiszolgáló nevét. Az eszköz szerzi be a megadott kiszolgálóknál figyelhető virtuális gépek.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)
5. A **replika helyadatok**, ha az Azure-bA replikál, vagy ha egy másodlagos adatközpontba replikál, és még nem állított be a replikakiszolgálón **hagyja ki a teszteket használata esetén a replika helyszínére**. Ha egy másodlagos adatközpontba replikálja, és beállította a replika típusa, adja meg az önálló kiszolgáló vagy az ügyfél-hozzáférési pont teljes Tartományneve a fürt **kiszolgáló nevét (vagy) Hyper-V replika Broker CAP**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)
6. A **kiterjesztett replika részletek**, engedélyezése **hagyja ki a teszteket, a kiterjesztett replika hely érintő**. Ezeket a teszteket a Site Recovery által nem támogatott.
7. A **válassza a virtuális gépeket replikálja**, az eszközök a kiszolgáló vagy fürt csatlakozik, és megjeleníti a virtuális gépek és az elsődleges kiszolgálón futó lemezek beállítások szerint fognak megadott a **elsődleges hely adatai** lap. Virtuális gépek, amelyek a replikáció már engedélyezve, vagy az, hogy nem fut, nem jelennek meg. Válassza ki a virtuális gépek, amelyekhez gyűjtéséhez. A virtuális merevlemezek kiválasztása automatikusan gyűjti az adatokat a virtuális gépek túl.
8. Ha egy adatbázisreplika-kiszolgáló vagy fürt, konfigurálta a **hálózati adatok**, adja meg a hozzávetőleges WAN sávszélességet úgy gondolja, hogy fogja használni az elsődleges és replika helyek között, és válassza ki a tanúsítványokat, ha konfigurálta az tanúsítványhitelesítés.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)
9. A **összegzés**, ellenőrizze a beállításokat, és kattintson a **következő** metrikák gyűjtésére. Eszköz folyamat- és állapot jelenik meg a **kiszámításához kapacitás** lap. Az eszköz futtatásának befejezése után kattintson **jelentés megtekintése** eredményének megtekintéséhez. Alapértelmezés szerint jelentések és a naplók vannak tárolva **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>4. lépés: Az eredmények értelmezését.

Az alábbiakban a fontos metrikák. Itt listán nem szereplő metrikák figyelmen kívül hagyhatja. Nem fontosságúak a Site Recovery szükséges.

### <a name="on-premises-to-on-premises-replication"></a>A helyszíni alkalmazások közötti replikáció

* Az elsődleges gazdagépével számítási, memória a replikáció hatása
* Az elsődleges, helyreállítási állomások tároló szabad lemezterület, IOPS replikációs hatása
* Teljes sávszélességre van szüksége változásreplikálás (MB/s)
* Megfigyelt hálózati sávszélesség között az elsődleges állomás és a helyreállítási gazdagépen (MB/s)
* A gazdagépek/fürtök közötti aktív párhuzamos átvitelek ideális számának javaslat

### <a name="on-premises-to-azure-replication"></a>A helyszíni Azure replikáció

* Az elsődleges gazdagépével számítási, memória a replikáció hatása
* A replikáció az elsődleges gazdagépével tárolási helyet a lemezen, IOPS hatása
* Teljes sávszélességre van szüksége változásreplikálás (MB/s)

## <a name="more-resources"></a>További erőforrások
* Az eszközzel kapcsolatos részletes információkért olvassa el a dokumentumot, amely az eszköz letölthető kerül.
* Tekintse meg az eszköz a Keith Mayer bemutatóért [TechNet blog](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
* [A eredményt](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) a teljesítmény, a helyszíni Hyper-V replikáció tesztelése

## <a name="next-steps"></a>Következő lépések

A kapacitástervezés befejezése után megkezdheti a Site Recovery telepítését:

* [VMM-felhőkben Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-vmm-to-azure.md)
* [(VMM nélkül) a Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-hyper-v-site-to-azure.md)
* [A VMM-helyek közti replikálásához a Hyper-V virtuális gépek](site-recovery-vmm-to-vmm.md)
