---
title: Vészhelyreállítás beállítása az SQL Server az SQL Server és az Azure Site Recovery |} A Microsoft Docs
description: Ez a cikk ismerteti a vész-helyreállítási beállítása az SQL Server, SQL Server és az Azure Site Recovery használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/30/2019
ms.author: sutalasi
ms.openlocfilehash: 1c44b10b54a5f58dff1aecf36c3633cc8ffbd8f0
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491776"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Vészhelyreállítás beállítása az SQL Server

Ez a cikk azt ismerteti, hogyan védheti meg az SQL Server háttéralkalmazását az SQL Server üzletmenet-folytonossági és vészhelyreállítási (BCDR) helyreállítási technológiák kombinációját használó alkalmazások és [Azure Site Recovery](site-recovery-overview.md).

Mielőtt elkezdené, győződjön meg arról, megismerte az SQL Server vész-helyreállítási lehetőségei, beleértve a Feladatátvételi fürtszolgáltatás, Always On rendelkezésre állási csoportokkal, az adatbázis-tükrözés, szállítási, aktív georeplikációval és automatikus feladatátvételi csoportok naplózása.

## <a name="dr-recommendation-for-integration-of-sql-server-bcdr-technologies-with-site-recovery"></a>Az SQL Server BCDR-technológiákkal a Site Recovery-integráció DR-javaslat

Az RTO és RPO igényeinek megfelelően többféle helyreállítási SQL Server-kiszolgálók egy BCDR technológiára kell alapulnia az alábbi táblázatban. Tegyük a választást számukra történik, ha a Site Recovery integrálható legyen az adott technológia előkészíthető a teljes alkalmazás helyreállítása a feladatátvételi műveletet.

**Központi telepítési típus** | **BCDR-technológia** | **Az SQL várt RTO** | **Az SQL várt RPO** |
--- | --- | --- | ---
Az Azure IaaS virtuális gépen vagy egy helyszíni SQL Server| **[Always On rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)** | Az idő, hogy a másodlagos másodpéldány az elsődleges megfelelő megoldások | Replikációs aszinkron a másodlagos replikára, így nincs adatvesztés.
Az Azure IaaS virtuális gépen vagy egy helyszíni SQL Server| **[A Feladatátvételi fürtszolgáltatás (mindig az FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017)** | Az idő a csomópontok közötti feladatátvételt megfelelő megoldások | Megosztott tárolót használ, ezért a storage-példány ugyanabban a nézetben érhető el a feladatátvételkor.
Az Azure IaaS virtuális gépen vagy egy helyszíni SQL Server| **[Az adatbázis-tükrözés (nagy teljesítményű mód)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017)** | Egyenértékű a szolgáltatást, amely a tükrözött kiszolgálót használja, mint Tartalékkiszolgáló újraindítás szükséges idő. | Replikáció az aszinkron. Késés jelentkezhet az elsődleges adatbázis mögött valamelyest a tüköradatbázis. A gap mérete általában kicsi, howvever, jelentős mértékben használják, ha az egyszerű vagy tükrözött kiszolgáló rendszer nagy terhelés alatt válhat.<br></br>Naplóküldés lehet az adatbázis-tükrözés kiegészítése és egy kedvező alternatívája lehet aszinkron adatbázis-tükrözés
Az SQL Azure PaaS-ként<br></br>(Az SQL database-kiszolgálók, rugalmas készletek) | **Aktív georeplikáció** | 30 másodperc után aktiválódik<br></br>Feladatátvétel a másodlagos adatbázisok egyik aktiválásakor összes másodlagos a rendszer automatikusan összekapcsolja az új elsődleges. | 5 másodperces Időkorlát<br></br>Aktív georeplikációt használja az AlwaysOn technológia az SQL Server replikálása aszinkron módon véglegesített tranzakciók száma, az elsődleges adatbázison egy másodlagos adatbázis-pillanatkép-elkülönítés használatával. <br></br>A másodlagos adatok garantáltan soha többé nem kell a részleges tranzakciókat.
Aktív georeplikáció az Azure-ban konfigurált PaaS-ként SQL<br></br>(SQL Database felügyelt példányába, rugalmas készletek az SQL database-kiszolgálók) | **Automatikus feladatátvételi csoportok** | RTO 1 óra | 5 másodperces Időkorlát<br></br>Automatikus feladatátvételi csoportok a csoport szemantikát felett aktív georeplikációt biztosít, de az azonos aszinkrón replikációs mechanizmus használatos.
Az Azure IaaS virtuális gépen vagy egy helyszíni SQL Server| **Az Azure Site Recovery replikációs** | Általában kevesebb mint 15 perc alatt. [További információ](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) további információ az Azure Site Recovery által biztosított RTO SLA. | az alkalmazáskonzisztencia és összeomlási konzisztenciát 5 percenként 1 óra. 

> [!NOTE]
> Néhány fontos szempontjaival SQL számítási feladatok az Azure Site Recovery védelmét:
> * Az Azure Site Recovery alkalmazás független, és ezért az SQL server egy támogatott operációs rendszert telepített bármely verzióját is védhetők az Azure Site Recovery. [További információk](vmware-physical-azure-support-matrix.md#replicated-machines).
> * Kiválaszthatja a Site Recovery szolgáltatást minden olyan Azure-ban, a Hyper-V, VMware vagy fizikai infrastruktúra központi telepítésben használja. Kérjük, kövesse a [útmutatást](site-recovery-sql.md#how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2) hogyan védheti meg az SQL Server-fürt az Azure Site Recovery a dokumentum végén található.
> * Győződjön meg arról, hogy az adatváltozási sebessége (írt bájtok másodpercenkénti száma) észlelt a gépen a rendszer belül [Site Recovery-korlátok](vmware-physical-azure-support-matrix.md#churn-limits). Windows rendszerű gépek a Feladatkezelő Teljesítmény lapján megtekintheti a. Figyelje meg az írási sebessége az egyes lemezek.
> * Az Azure Site Recovery támogatja a replikációt a Feladatátvevőfürt-példányok közvetlen tárolóhelyeken. [További információk](azure-to-azure-how-to-enable-replication-s2d-vms.md).
 

## <a name="disaster-recovery-of-application"></a>Alkalmazások vészhelyreállítása

**Az Azure Site Recovery koordinálja a feladatátvételi teszt és a helyreállítási tervek segítségével. a teljes alkalmazás feladatátvétele.** 

Vannak bizonyos Előfeltételek annak érdekében, hogy a helyreállítási terv teljes mértékben testreszabható az igényeknek megfelelően. Minden olyan SQL Server-telepítés általában egy Active Directory van szüksége. Kapcsolat az alkalmazás szintjén is szükséges.

### <a name="step-1-set-up-active-directory"></a>1\. lépés: Active Directory beállítása

Állítsa be az Active Directory, a másodlagos helyreállítási hely az SQL Server megfelelően fusson.

* **Kis vállalati**– az alkalmazások és a helyszíni helyhez tartozó egyetlen tartományvezérlőt kis számú, ha átadja a feladatokat a teljes helyre szeretné azt javasoljuk, használja a Site Recovery replikációs replikálása a másodlagos tartományvezérlő Datacenter, vagy az Azure-bA.
* **Közepes és nagyvállalati**– Ha nagy számú alkalmazások, az Active Directory-erdőben van, és szeretne végezni az irányítást az alkalmazást vagy munkaterhelést futtatják, javasoljuk, hogy beállította egy további tartományvezérlőt a másodlagos adatközponthoz, vagy az Azure-ban. Ha AlwaysOn rendelkezésre állási csoportok egy távoli helyen való helyreállításához használja, javasoljuk a helyreállított SQL Server-példány használata a másodlagos helyen vagy az Azure-ban, egy másik további tartományvezérlő beállítása.

A jelen cikkben lévő utasítások feltételezik, hogy a tartományvezérlő a másodlagos helyen érhető el. [További információ](site-recovery-active-directory.md) Active Directory a Site Recovery védelméről.

### <a name="step-2-ensure-connectivity-with-other-application-tiers-and-web-tier"></a>2\. lépés: Győződjön meg, hogy a kapcsolat más alkalmazás tier(s) és a webes szint

Győződjön meg arról, hogy az adatbázisszint működik-e a cél Azure-régióban, miután, hogy rendelkezik az alkalmazással és a webes szint. Szükséges lépéseket előre kell venni a feladatátvételi teszt a kapcsolat ellenőrzéséhez.

Megismerheti, hogyan is tervezhet alkalmazásokat következzen két példa kapcsolat megfontolások:
* [Felhőalapú vész-helyreállítási alkalmazások tervezése](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Rugalmas készletek vészhelyreállítási stratégiái](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-integrate-with-always-on-active-geo-replication-or-auto-failover-groups-for-application-failover"></a>3\. lépés: Integráció a mindig bekapcsolva, aktív georeplikációval vagy alkalmazás feladatátvételhez automatikus feladatátvételi csoportok

Always On BCDR-technológiákkal, aktív-földrajzi replikálási és automatikus feladatátvételi csoportok rendelkezik az SQL server rendszert futtató Azure-célrégiót a másodlagos replikákon. Ezért az első lépés az alkalmazás feladatátvételre, hogy ez a másodpéldány az elsődleges (feltéve, hogy már rendelkezik egy olyan tartományvezérlő másodlagos). Ez a lépés nem lehet szükség, ha úgy dönt, hogy az automatikus feladatátvételt hajt végre. Csak az adatbázis-feladatátvétel befejezése után meg kell feladatátvételt webhely vagy alkalmazás szinten.

> [!NOTE] 
> Ha az SQL-gépek az Azure Site Recovery védelemmel, egyszerűen hozzon létre egy helyreállítási csoportot az ezeken a gépeken, és adja hozzá a feladatátvételi a helyreállítási tervben szereplő.

[Hozzon létre egy helyreállítási terv](site-recovery-create-recovery-plans.md) alkalmazással és a webes szintű virtuális gépeken. Kövesse az az alábbi lépéseket az adatbázisszint feladatátvételi hozzáadása:

1. Parancsfájlok importálja az Azure Automation-fiók. Ez tartalmazza a feladatátvétel SQL rendelkezésre állási csoport szkripteket a egy [Resource Manager virtuális gépének](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) és a egy [klasszikus virtuális gép](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1).

    [![Üzembe helyezés az Azure-ban](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)


1. Adja hozzá az ASR-SQL-FailoverAG előzetes műveletként az első csoport a helyreállítási terv.

1. Kövesse az utasításokat a parancsprogram elérhető a nevét, a rendelkezésre állási csoportok egy automatizálási változó létrehozása.

### <a name="step-4-conduct-a-test-failover"></a>4\. lépés: Feladatátvételi teszt elvégzésével

Néhány BCDR-technológiákkal, például SQL Always On nem támogatják natív módon feladatátvételi tesztet. Ezért azt javasoljuk, hogy a következő módon **csak akkor, ha ilyen technológiák integrálásáról**:

1. Állítsa be a [Azure Backup](../backup/backup-azure-arm-vms.md) replikát a rendelkezésre állási csoportot az Azure-beli virtuális gépen.

1. Mielőtt elindítaná a helyreállítási terv teszt feladatátvétele, a virtuális gép helyreállítása az előző lépésben biztonsági.

    ![Az Azure biztonsági másolat visszaállítása](./media/site-recovery-sql/restore-from-backup.png)

1. [A kvórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) a virtuális gép biztonsági mentésből.

1. Frissítési IP-címét a figyelő egy IP-cím elérhető a feladatátvételi teszt hálózatában.

    ![Frissítse a figyelő IP](./media/site-recovery-sql/update-listener-ip.png)

1. Figyelő online állapotba.

    ![Figyelő Online állapotba helyezés](./media/site-recovery-sql/bring-listener-online.png)

1. Load balancer létrehozása egy előtérbeli IP-címkészlet minden rendelkezésre állási csoport figyelőjének megfelelő alatt létrehozott IP és a háttérkészlet hozzáadása SQL virtuális géppel.

     ![Load Balancer - előtérbeli IP-készlet létrehozása](./media/site-recovery-sql/create-load-balancer1.png)

    ![Load Balancer létrehozása – háttérkészlet](./media/site-recovery-sql/create-load-balancer2.png)

1. Adja hozzá az alkalmazásrétegben webes réteg követ a későbbi helyreállítás csoportokba a helyreállítási terv feladatátvételét. 
1. Hajtsa végre az alkalmazás végpontok közötti feladatátvétel tesztelése a helyreállítási terv feladatátvételi tesztet.

## <a name="steps-to-do-a-failover"></a>Ehhez a feladatátvétel lépések

Miután hozzáadta a parancsfájl a 3. lépésben a helyreállítási tervben szereplő és ellenőrizte, hogy egy feladatátvételi tesztet egy specializált módszert használja, a 4. lépés végrehajtásával, a 3. lépésben létrehozott helyreállítási terv feladatátvételi teheti meg.

Vegye figyelembe, hogy az alkalmazás és a webes szint feladatátvételi lépései egyeznie kell a feladatátvételi teszt és a feladatátvétel helyreállítási tervek.

## <a name="how-to-protect-a-sql-server-cluster-standard-editionsql-server-2008-r2"></a>Hogyan védheti meg az SQL Server-fürt (standard edition vagy SQL Server 2008 R2)

SQL Server Standard edition vagy SQL Server 2008 R2 rendszert futtató fürtre javasoljuk a Site Recovery replikációs használatával az SQL Server védelme.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Az Azure és az Azure-bA helyszíni Azure

A Site Recovery nem biztosít a Vendég támogatási fürt, amikor egy Azure-régióban való replikálásához. Az SQL Server is nem biztosít egy alacsony költségű vész-helyreállítási megoldást a Standard kiadása esetén. Ebben a forgatókönyvben javasoljuk, hogy egy önálló elsődleges hely az SQL Server az SQL Server-fürt védelmére, és azt a másodlagos helyre.

1. Elsődleges Azure-régió vagy a helyszíni helyen, konfigurálja a további önálló SQL Server-példány.
1. A védeni kívánt adatbázisokat a tükör egyikükön példányát konfigurálja. Állítsa be a magas biztonsági üzemmódú tükrözés.
1. Az elsődleges helyen konfigurálni a Site Recovery ([Azure](azure-to-azure-tutorial-enable-replication.md), [Hyper-V](site-recovery-hyper-v-site-to-azure.md) vagy [VMware virtuális gépek/fizikai kiszolgálók)](site-recovery-vmware-to-azure-classic.md).
1. A Site Recovery replikációs segítségével az új SQL Server-példány replikálása másodlagos helyre. Tükrözött magas biztonsági másolatot, mert az elsődleges fürt szinkronizálható, de a Site Recovery replikációs replikálandó.


![Standard fürt](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Feladat-visszavétel kapcsolatos szempontok

Az SQL Server Standard fürtök esetén a feladat-visszavétel nem tervezett feladatátvétel után szükséges egy SQL server biztonsági másolat és helyreállítás az eredeti fürthöz, a tükör reestablishment a tükör példányból.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-sql-get-licensed-when-protected-with-azure-site-recovery"></a>Hogyan nem SQL első licencelése az Azure Site Recovery védett?
Az SQL Server esetében Azure Site Recovery-replikációra érvényes a Frissítési Garancia és a Vészhelyreállítási ajánlat minden Azure Site Recovery-alkalmazási helyzetben (helyszínről az Azure-ba történő vészhelyreállítás, vagy pedig régiók közötti Azure-beli IaaS-vészhelyreállítás). [További információ](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-azure-site-recovery-support-my-sql-version"></a>Az Azure Site Recovery támogatja az SQL verziója?
Az Azure Site Recovery szolgáltatás a alkalmazás független. Az SQL server egy támogatott operációs rendszert telepített bármely verzióját, ezért az Azure Site Recovery védhetők. [További információ](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>További lépések
* [További](site-recovery-components.md) kapcsolatos Site Recovery architektúrájáról.
* Az Azure-beli SQL-kiszolgálók, tudjon meg többet [magas rendelkezésre állású megoldások](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) másodlagos Azure-régióban a helyreállításhoz.
* Az SQL Database az Azure-ban, tudjon meg többet a [üzletmenet-folytonossági](../sql-database/sql-database-business-continuity.md) és [magas rendelkezésre állású](../sql-database/sql-database-high-availability.md) másodlagos Azure-régióban található helyreállítási lehetőségei.
* Az SQL server-gépek, a helyszínen [további](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) a magas rendelkezésre állású lehetőségekről, a helyreállítás az Azure Virtual machines gépeken.
