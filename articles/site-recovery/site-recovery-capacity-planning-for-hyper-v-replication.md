---
title: "Hyper-V kapacitástervezési eszköz az Azure Site Recovery |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Site Recovery a Hyper-V kapacitástervező eszköz futtatása"
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
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: 80c3dcb65bd74bcfa3acc5f12dd5c45cd1c06455
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="hyper-v-capacity-planning-tool-for-site-recovery"></a>Hyper-V kapacitástervezési eszköz a Site Recovery

A továbbfejlesztett verziójának [Azure hely helyreállítási telepítési Planner for Hyper-V számára az Azure-telepítés](site-recovery-hyper-v-deployment-planner.md) érhető el. Lecseréli a régi eszköz. Az új eszköz használata a központi telepítésének megtervezése. Az eszközt biztosít a következő irányelveket:

* Virtuális gép jogosultsági assessment, a lemezek számát, lemez méretét, IOPS, forgalmának kezeléséhez, és néhány virtuális gép jellemzők alapján
* Hálózati sávszélesség és a helyreállítási Időkorlát assessment kell
* Az Azure-infrastruktúrával kapcsolatos követelmények
* A helyszíni infrastruktúrával kapcsolatos követelmények
* Kezdeti replikálás kötegelés útmutató
* Becsült teljes vész helyreállítási költségének az Azure-bA

Az Azure Site Recovery üzembe helyezése részeként akkor ki kell deríteni a replikáció és a sávszélesség-követelményekkel. A Hyper-V-kapacitástervezési eszköz a Site Recovery segítségével meghatározhatja, hogy ezek a követelmények, a Hyper-V Virtuálisgép-replikációt.

A cikkből megtudhatja, hogyan futtathat a Hyper-V kapacitástervezési eszköz. Ezt az eszközt az adatokkal együtt használja [Site Recovery Kapacitástervezését](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Előkészületek
Az eszközt futtatja az elsődleges hely Hyper-V kiszolgáló vagy fürt csomópontja. Az eszköz futtatásához, a Hyper-V-kiszolgálóknak kell:

* Operációs rendszer: Windows Server 2012 vagy 2012 R2
* Memória: 20 MB (minimum)
* CPU: 5 százalékkal terhelés (minimum)
* Szabad lemezterület: 5 MB (minimum)

Az eszköz futtatása előtt elő kell készíteni az elsődleges hely. Ha két helyszíni hely között replikálja, és a sávszélesség ellenőrizni kívánja, akkor készítse elő a kiszolgálót is.

## <a name="step-1-prepare-the-primary-site"></a>1. lépés: Felkészülés az elsődleges hely

1. Győződjön meg az elsődleges hely, a Hyper-V virtuális gépeinek replikálni kívánt listáját. A Hyper-V-gazdagépek vagy helyük fürtök listája Az eszköz több önálló gazdagépen vagy egy fürtön, de nem mindkettőt együtt is futtatható. Azt is meg külön futtatja az egyes operációs rendszerekhez. Gyűjtse össze a Hyper-V kiszolgálók a következő információkat:

   * Windows Server 2012 standalone servers
   * Windows Server 2012-fürtök
   * Windows Server 2012 R2 standalone servers
   * Windows Server 2012 R2-fürt

2. A Hyper-V-gazdagépek és fürtök Windows Management Instrumentation távoli hozzáférés engedélyezése. Futtassa ezt a parancsot minden olyan kiszolgáló vagy fürt győződjön meg arról, hogy a tűzfalszabályok, és a felhasználói engedélyek vannak beállítva:

        netsh firewall set service RemoteAdmin enable
3. Teljesítmény a figyelés bekapcsolható a kiszolgálók és fürtök az alábbiak szerint:

   a. Nyissa meg a Windows tűzfal a **biztonságú** beépülő modult. 
   
   b. Jelölje ki a bejövő szabályt **COM + hálózati hozzáférés (DCOM-IN)**. Válassza ki az összes szabály az **Távoli Eseménynapló kezelése** csoport.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>2. lépés: Készítse elő a kiszolgálót (a helyszíni replikáció)
Ha replikálása Azure-ba, nem kell tennie, hogy ezt a lépést.

Azt javasoljuk, hogy beállított egy Hyper-V állomás a helyreállítási kiszolgálónak, hogy egy üres virtuális Gépet replikálni lehet, hogy ellenőrizze a sávszélesség. Ezt a lépést kihagyhatja, de nem sávszélesség mérését, kivéve, ha ezt a teheti.

1. Ha egy fürtcsomóponton replikaként használni kívánt, adja meg a Hyper-V Replikaszervező:

   a. A **Kiszolgálókezelő**, nyissa meg **Feladatátvevőfürt-kezelő**.

   b. Csatlakozzon a fürthöz, és jelölje ki a fürt nevét. Válassza ki **műveletek** > **szerepkör konfigurálása** a magas rendelkezésre állás varázsló megnyitásához.

   c. A **Szerepkörválasztás**, jelölje be **Hyper-V Replikaszervező**. A varázslóban adja meg egy nevet a **NetBIOS-név**. Adjon meg egy címet a **IP-cím** , a szolgáltatáskapcsolódási pont a fürt (ügyfél-hozzáférési pont) használható. A **Hyper-V Replikaszervező** van konfigurálva, egy ügyfél-hozzáférési pont nevét, vegye figyelembe ennek eredményeképpen.

   d. Győződjön meg arról, hogy a Hyper-V Replikaszervező szerepkör sikeresen online módba kerül, és a fürt minden csomópontjáról képes átvenni. Kattintson a jobb gombbal a szerepkört, és válassza ki **áthelyezése** > **csomópont kiválasztása**. Válasszon ki egy csomópontot, majd válassza ki **OK**.

   e. Ha Tanúsítványalapú hitelesítés használata esetén győződjön meg arról, egyes fürtcsomópontok és az ügyfél-hozzáférési pontja minden telepített tanúsítvánnyal rendelkeznek.

2. Ahhoz, hogy a kiszolgálót, ezeket a lépéseket:

   a. Nyissa meg a fürt esetén **hiba kezelő** , és csatlakozzon a fürthöz. Válassza ki **szerepkörök**, és válassza ki a szerepkört. Válassza ki **replikációs beállítások** > **replikakiszolgálóként fürt engedélyezése**. A replika egy fürt használja, ha szüksége van a Hyper-V Replikaszervező szerepkör rendelkezik a fürtön, valamint az elsődleges helyen.

   b. Nyissa meg egy önálló kiszolgáló **Hyper-V kezelője**. Az a **műveletek** ablaktáblán válassza előbb **Hyper-V beállítások** engedélyezni kívánja a kiszolgáló. A **Replikációkonfiguráló**, jelölje be **replikakiszolgálóként számítógép engedélyezése**.

3. Hitelesítés beállítása az alábbi lépéseket kell végrehajtani:

   a. A **hitelesítés és portok**, válassza ki, hogyan segítségével hitelesíti az elsődleges kiszolgáló és a hitelesítési portokat. Ha tanúsítványt használ, válassza ki a **tanúsítvány kiválasztása** kívánt fiók kiválasztásához. Kerberos használja, ha az elsődleges és a helyreállítási Hyper-V-gazdagépek ugyanabban a tartományban vagy megbízható tartományban. Különböző tartományokhoz vagy munkacsoport-KözpontiTelepítés-tanúsítványokat használ.

   b. A **engedélyezés és tárolás**, engedélyezése **bármely** hitelesített (elsődleges) kiszolgáló küldhet-e adatokat a replikakiszolgálónak.

   ![A replikációs alapkonfiguráció](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

   c. Győződjön meg arról, hogy a figyelő a protokoll és port megadott fut, futtassa a **netsh http show servicestate**. 

4. Állítsa be a tűzfalon. Hyper-V telepítése során a tűzfalszabályok engedélyezik a forgalmat az alapértelmezett (a 443-as HTTPS) és a Kerberos a 80-as portot a jönnek létre. Engedélyezze a ezeket a szabályokat az alábbiak szerint:

    - Tanúsítványhitelesítés fürtön (443):``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
    - Kerberos-hitelesítés (80-as) fürtön:``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
    - Tanúsítványhitelesítés önálló kiszolgálón:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
    - Kerberos-hitelesítés önálló kiszolgálón:``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planning-tool"></a>3. lépés: A kapacitástervezés eszköz futtatása
Miután előkészítette a elsődleges helyhez, és állítsa be a helyreállítási kiszolgálónak, futtathatja az eszközt.

1. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39057) az eszközt a Microsoft Download Center.

2. Futtassa az eszközt az elsődleges kiszolgálón vagy az elsődleges fürt a csomópontok egyikét. Kattintson a jobb gombbal a .exe-fájl, és válassza a **Futtatás rendszergazdaként**.

3. A **előkészületek**, adja meg, mennyi ideig adatainak gyűjtéséről. Azt javasoljuk, hogy az eszköz időszakban éles annak biztosításához, hogy adatokat képviselőjével. Ha azt szeretné, csak a hálózati kapcsolat ellenőrzése, csak egy percet hozhatja létre.

    ![Előkészületek](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. A **elsődleges hely adatai**, adja meg a kiszolgáló nevét vagy FQDN önálló gazdagépként. Fürt esetén adja meg a fürtben az ügyfél-hozzáférési pont, a fürt nevét vagy a bármely csomópont teljes Tartománynevét. Kattintson a **Tovább** gombra. Az eszköz automatikusan észleli a futtató kiszolgáló nevét. Az eszköz szerzi be a megadott kiszolgálóknál figyelhető virtuális gépek.

    ![Elsődleges hely adatai](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. A **replika helyadatok**, ha replikálása Azure-ba, vagy ha replikálása másodlagos adatközpontba, és még nem állított be a replikakiszolgálón **hagyja ki a teszteket használata esetén a replika helyszínére**. Ha egy másodlagos adatközpontba replikálja, és beállította a replika típusa, adja meg az önálló kiszolgáló vagy az ügyfél-hozzáférési pont teljes Tartománynevét a fürt számára az **kiszolgáló nevét (vagy) Hyper-V replika Broker CAP**.

    ![A replika hely adatai](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. A **kiterjesztett replika részletek**, jelölje be **hagyja ki a teszteket, a kiterjesztett replika hely érintő**. Ezeket a teszteket a Site Recovery által nem támogatott.

7. A **válassza a virtuális gépeket replikálja**, az eszköz csatlakozik a kiszolgáló vagy fürt. Azt jeleníti meg a virtuális gépek és a beállítások alapján, az elsődleges kiszolgálón futó lemezek megadott a **elsődleges hely adatai** lap. Nem jeleníthető meg, hogy a replikáció már engedélyezve vagy nem futó virtuális gépek. Válassza ki a virtuális gépek, amelyekhez gyűjtéséhez. A virtuális merevlemezek kiválasztása automatikusan gyűjti az adatokat a virtuális gépek túl.

8. Ha konfigurálta a adatbázisreplika-kiszolgáló vagy fürt, a **hálózati adatok**, adja meg az elsődleges és replika helyek közötti használandó hozzávetőleges WAN sávszélességét. Ha a tanúsítványhitelesítés módszert állította be, válassza ki a tanúsítványokat.

    ![Hálózati adatai](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

9. A **összegzés**, ellenőrizze a beállításokat. Válassza ki **következő** metrikák gyűjtésére. Eszköz folyamat- és állapot jelenik meg a **kiszámításához kapacitás** lap. Ha az eszköz futásának, válassza ki a **jelentés megtekintése** eredményének megtekintéséhez. Alapértelmezés szerint jelentések és a naplók vannak tárolva **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![Kapacitásának kiszámításához](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>4. lépés: Az eredmények értelmezését.

Az alábbiakban a fontos metrikák. Itt nem szereplő metrikák figyelmen kívül hagyhatja. Nem fontosságúak a Site Recovery szükséges.

### <a name="on-premises-to-on-premises-replication"></a>A helyszíni alkalmazások közötti replikáció

* A replikáció az elsődleges gazdagépével számítási és memóriakapacitást hatása
* Az elsődleges állomás és a helyreállítási gazdagépen tárolási lemezterületet, és IOPS replikációs hatása
* Teljes sávszélességre van szüksége változásreplikálás (MB/s)
* Megfigyelt hálózati sávszélesség között az elsődleges állomás és a helyreállítási gazdagépen (MB/s)
* A két gazdagépekhez vagy fürtökhöz közötti aktív párhuzamos átvitelt ideális számának javaslat

### <a name="on-premises-to-azure-replication"></a>A helyszíni Azure replikáció

* A replikáció az elsődleges gazdagépével számítási és memóriakapacitást hatása
* Az elsődleges gazdagépével tárolási lemezterület és IOPS replikációs hatása
* Teljes sávszélességre van szüksége változásreplikálás (MB/s)

## <a name="more-resources"></a>További erőforrások

* Az eszközzel kapcsolatos további információkért olvassa el a dokumentumot, amely az eszköz letölthető kerül.
* Tekintse meg az eszköz a Keith Mayer bemutatóért [TechNet blog](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
* [A eredményt](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) teljesítményének tesztelése a helyszíni Hyper-V replikáció.

## <a name="next-steps"></a>További lépések

A kapacitástervezés befejezése után telepítheti a Site Recovery:
* [VMM-felhőkben Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-vmm-to-azure.md)
* [(VMM nélkül) a Hyper-V virtuális gépek replikálása Azure-bA](site-recovery-hyper-v-site-to-azure.md)
* [A VMM-helyek közti replikálásához a Hyper-V virtuális gépek](site-recovery-vmm-to-vmm.md)
