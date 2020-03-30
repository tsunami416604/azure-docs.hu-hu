---
title: Vészhelyreállítás beállítása az SQL Server hez az Azure Site Recovery szolgáltatással
description: Ez a cikk ismerteti, hogyan állíthatja be a vészhelyreállítást az SQL Server az SQL Server és az Azure Site Recovery használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084741"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Vészhelyreállítás beállítása az SQL Server hez

Ez a cikk az alkalmazások SQL Server-háttérrendszerének védelmét ismerteti. Ezt az SQL Server üzletmenet-folytonossági és vész-helyreállítási (BCDR) technológiáinak és az [Azure Site Recovery-nek](site-recovery-overview.md)a kombinációjával teszi meg.

Mielőtt elkezdené, győződjön meg arról, hogy tisztában van az SQL Server vész-helyreállítási képességekkel. Ilyen képességek:

* Feladatátvételi fürtszolgáltatás
* Mindig elérhető késedelmi csoportok
* Adatbázis-tükrözés
* Naplóküldés
* Aktív georeplikáció
* Automatikus feladatátvételi csoportok

## <a name="combining-bcdr-technologies-with-site-recovery"></a>BcDR technológiák és a site recovery kombinálása

Az SQL Server-példányok helyreállításához bcdr-technológiát választott a helyreállítási idő célkitűzés (RTO) és a helyreállítási pont célkitűzés (RPO) igényei alapján kell, hogy az alábbi táblázatban leírtak szerint. Kombinálja a Site Recovery-t a kiválasztott technológia feladatátvételi műveletével a teljes alkalmazás helyreállításának koordinálásához.

Telepítés típusa | BCDR technológia | Az SQL Server várt rto-ja | Várt RPO az SQL Server-hez |
--- | --- | --- | ---
SQL Server egy Azure-infrastruktúra szolgáltatás (IaaS) virtuális gép (VM) vagy a helyszíni.| [Always On rendelkezésre állási csoport](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | A másodlagos replika elsődlegesként való átalakítása szükséges idő. | Mivel a másodlagos replika replikációja aszinkron, van némi adatvesztés.
SQL Server egy Azure IaaS virtuális gép vagy a helyszínen.| [Feladatátvevő fürtözés (mindig bekapcsolva FCI)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | A csomópontok közötti feladatátvételhez szükséges idő. | Mivel a Mindig bekapcsolva FCI megosztott tárolót használ, a tárolópéldány ugyanazon nézete érhető el feladatátvételkor.
SQL Server egy Azure IaaS virtuális gép vagy a helyszínen.| [Adatbázis-tükrözés (nagy teljesítményű mód)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | A szolgáltatás kikényszerítéséhez szükséges idő, amely a tükörkiszolgálót meleg készenléti kiszolgálóként használja. | A replikáció aszinkron. A tüköradatbázis némileg elmarad a fő adatbázistól. A lag általában kicsi. De ez válhat nagy, ha a fő vagy tükör szerver rendszere alatt nagy terhelés.<br/><br/>A naplószállítás az adatbázis-tükrözés kiegészítése lehet. Ez egy kedvező alternatívája az aszinkron adatbázis tükrözés.
SQL mint szolgáltatás (PaaS) az Azure-ban.<br/><br/>Ez a központi telepítési típus rugalmas készleteket és Azure SQL Database-kiszolgálókat tartalmaz. | Aktív georeplikáció | 30 másodperccel a feladatátvétel után.<br/><br/>Ha a feladatátvétel a másodlagos adatbázisok egyikéhez aktiválódik, az összes többi másodlagos automatikusan kapcsolódik az új elsődleges. | RPO öt másodperc.<br/><br/>Az aktív georeplikáció az SQL Server Always On technológiáját használja. Aszinkron módon replikálja az elsődleges adatbázisban lekötött tranzakciókat egy másodlagos adatbázisba pillanatkép-elkülönítés használatával.<br/><br/>A másodlagos adatok garantáltan soha nem rendelkeznek részleges tranzakciókkal.
SQL-t, mint az Azure-beli aktív georeplikációval konfigurált PaaS-t.<br/><br/>Ez a központi telepítési típus tartalmaz egy SQL Database felügyelt példányt, rugalmas készleteket és SQL Database-kiszolgálókat. | Automatikus feladatátvételi csoportok | Egy órás rto. | RPO öt másodperc.<br/><br/>Az automatikus feladatátvételi csoportok biztosítják a csoport szemantikáját az aktív georeplikáció felett. De ugyanazt az aszinkron replikációs mechanizmust használják.
SQL Server egy Azure IaaS virtuális gép vagy a helyszínen.| Replikáció az Azure site recovery szolgáltatással | RTO általában kevesebb, mint 15 perc. További információért olvassa el a [Site Recovery által biztosított RTO SLA-t.](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) | Egy óra az alkalmazás konzisztenciája és öt perc az összeomlás konzisztenciája. Ha alacsonyabb RPO-t keres, használjon más BCDR technológiákat.

> [!NOTE]
> Néhány fontos szempont, amikor a Site Recovery segítségével segít megvédeni az SQL-munkaterheléseket:
> * Site Recovery alkalmazás agnosztikus. A Site Recovery segítségével megvédheti az SQL Server támogatott operációs rendszerre telepített bármely verzióját. További információ: a replikált gépek [helyreállításának támogatási mátrixa.](vmware-physical-azure-support-matrix.md#replicated-machines)
> * Választhat, hogy a Site Recovery-t az Azure, a Hyper-V, a VMware vagy a fizikai infrastruktúra bármely üzembe helyezéséhez használja. Kövesse a cikk végén található útmutatást az [SQL Server-fürt site](#how-to-help-protect-a-sql-server-cluster) recovery szolgáltatással történő védelméről.
> * Győződjön meg arról, hogy a számítógépen megfigyelt adatváltozási sebesség a [Site Recovery határain](vmware-physical-azure-support-matrix.md#churn-limits)belül van. A változási sebességet írási bájt/másodpercben mérik. Windows rendszert futtató gépek esetén ezt a változási sebességet a Feladatkezelő **Teljesítmény** lapján tekintheti meg. Figyelje meg az egyes lemezek írási sebességét.
> * A Site Recovery támogatja a feladatátvevő fürtpéldányok replikációját a közvetlen tárolóhelyeken. További információ: [A közvetlen tárolóhelyek replikációjának engedélyezése.](azure-to-azure-how-to-enable-replication-s2d-vms.md)

## <a name="disaster-recovery-of-an-application"></a>Egy alkalmazás vészhelyreállítása

A Site Recovery a teljes alkalmazás feladatátvételét és feladatátvételét vezényli a helyreállítási tervek segítségével.

Vannak olyan előfeltételek, amelyek biztosítják, hogy a helyreállítási terv teljes mértékben testre szabott az Ön igényeinek megfelelően. Az SQL Server bármely központi telepítéséhez általában Active Directory-telepítésre van szükség. Az alkalmazáscsomaghoz is szüksége van kapcsolatra.

### <a name="step-1-set-up-active-directory"></a>1. lépés: Az Active Directory beállítása

Állítsa be az Active Directoryt a másodlagos helyreállítási helyen az SQL Server megfelelő futtatásához.

* **Kisvállalat**: Kisszámú alkalmazással és egyetlen tartományvezérlővel rendelkezik a helyszíni helyhez. Ha az egész helyet át szeretné venni, használja a Site Recovery replikációt. Ez a szolgáltatás replikálja a tartományvezérlőt a másodlagos adatközpontba vagy az Azure-ba.
* **Közepes és nagyvállalat :** Előfordulhat, hogy további tartományvezérlőket kell beállítania.
  - Ha nagy számú alkalmazással rendelkezik, rendelkezik egy Active Directory-erdővel, és alkalmazás vagy munkaterhelés szerint szeretné átvenni a feladatokat, állítson be egy másik tartományvezérlőt a másodlagos adatközpontban vagy az Azure-ban.
  -  Ha az Always On rendelkezésre állási csoportokat használja a távoli helyre való helyreállításhoz, állítson be egy másik tartományvezérlőt a másodlagos helyen vagy az Azure-ban. Ez a tartományvezérlő a helyreállított SQL Server-példányhoz használatos.

A cikkben található utasítások feltételezik, hogy a tartományvezérlő elérhető a másodlagos helyen. További információ: az [Active Directory site recovery szolgáltatással való védelmének elősegítésére](site-recovery-active-directory.md)szolgáló eljárások.

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>2. lépés: Kapcsolat biztosítása más rétegekkel

Miután az adatbázis-szint fut a cél Azure-régióban, győződjön meg arról, hogy az alkalmazás és a webes rétegek kapcsolat. A szükséges lépéseket előre a teszt feladatátvételsel való kapcsolat érvényesítéséhez.

Ha meg szeretné tudni, hogyan tervezhet alkalmazásokat kapcsolódási szempontokat, tekintse meg az alábbi példákat:

* [Felhőbeli vész-helyreállítási alkalmazás tervezése](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Rugalmas készlet vész-helyreállítási stratégiák](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>3. lépés: Az Mindig bekapcsolt, az aktív georeplikációs és az automatikus feladatátvételi csoportokkal való együttműködést

BCDR-technológiák mindig be van kapcsolva, az aktív georeplikáció és az automatikus feladatátvételi csoportok másodlagos replikái az SQL Server fut a cél Azure-régióban. Az alkalmazás feladatátvételelső lépése, hogy ezt a replika elsődlegesként. Ez a lépés feltételezi, hogy már rendelkezik egy tartományvezérlővel a másodlagos. A lépés nem feltétlenül szükséges, ha úgy dönt, hogy egy automatikus feladatátvétel. Csak az adatbázis feladatátvétele után adja át a webes és alkalmazásszinteket.

> [!NOTE]
> Ha a Site Recovery segítségével segített az SQL-gépek védelmében, csak létre kell hoznia ezeknek a gépeknek a helyreállítási csoportját, és hozzá kell adnia a feladatátvételt a helyreállítási tervhez.

[Hozzon létre egy helyreállítási tervet](site-recovery-create-recovery-plans.md) alkalmazás- és webes rétegbeli virtuális gépekkel. A következő lépések bemutatják, hogyan adhat hozzá feladatátvételt az adatbázisréteghez:

1. Importálja a parancsfájlokat az SQL availability csoport feladatátvételéhez mind [az Erőforrás-kezelő virtuális gépben,](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) mind a [klasszikus virtuális gépen.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1) Importálja a parancsfájlokat az Azure Automation-fiókba.

    [!["Üzembe helyezés az Azure-ba" embléma képe](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adja hozzá az ASR-SQL-FailoverAG parancsfájlt a helyreállítási terv első csoportjának előműveleteként.

1. Kövesse a parancsfájlban elérhető utasításokat egy automatizálási változó létrehozásához. Ez a változó a rendelkezésre állási csoportok nevét adja meg.

### <a name="step-4-conduct-a-test-failover"></a>4. lépés: Tesztfeladat-átvétel

Egyes BCDR-technológiák, például az SQL Always On nem támogatja natívmódon a tesztfeladat-átvételt. Az alábbi megközelítést *csak az ilyen technológiák használata esetén*javasoljuk.

1. Állítsa be az [Azure Backup](../backup/backup-azure-arm-vms.md) a virtuális gép, amely az Azure-ban a rendelkezésre állási csoport replika.

1. A helyreállítási terv tesztfeladat-átvétele előtt a virtuális gép helyreállítása az előző lépésben végrehajtott biztonsági másolatból.

    ![Képernyőkép a konfiguráció Azure Backup ból történő visszaállításához](./media/site-recovery-sql/restore-from-backup.png)

1. [Kvórum kényszerítése](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) a virtuális gépben, amely biztonsági mentésből lett visszaállítva.

1. Frissítse a figyelő IP-címét, hogy a teszt feladatátvételi hálózatban elérhető cím legyen.

    ![Képernyőkép a szabályok ablak- és IP-címtulajdonságaipárbeszédpanelről](./media/site-recovery-sql/update-listener-ip.png)

1. Hozza online a hallgatót.

    ![Képernyőkép a kiszolgálóneveket és állapotokat megjelenítő Content_AG feliratú ablakról](./media/site-recovery-sql/bring-listener-online.png)

1. Győződjön meg arról, hogy a feladatátvevő hálózat terheléselosztó ja egy IP-címmel rendelkezik, az egyes rendelkezésre állási csoport figyelőjének megfelelő előtér-IP-címkészletből és az SQL Server virtuális gépa háttérkészletben lévő előtér-IP-címkészletből.

     !["SQL-AlwaysOn-LB - Előtér-IP-készlet" című ablak képernyőképe](./media/site-recovery-sql/create-load-balancer1.png)

    !["SQL-AlwaysOn-LB - HáttérIP-készlet" című ablak képernyőképe](./media/site-recovery-sql/create-load-balancer2.png)

1. A későbbi helyreállítási csoportokban adja hozzá az alkalmazásszint feladatátvételét, majd a helyreállítási terv webes rétegét.

1. Végezze el a helyreállítási terv tesztfeladat-átvétele az alkalmazás végpontok között feladatátvétel teszteléséhez.

## <a name="steps-to-do-a-failover"></a>A feladatátvétel lépései

Miután hozzáadja a parancsfájlt a 3.

Az alkalmazás- és webszintek feladatátvételi lépéseinek azonosnak kell lenniük mind a feladatátvételi, mind a feladatátvételi helyreállítási tervekben.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Sql Server-fürt védelme

SQL Server Standard edition vagy SQL Server 2008 R2 rendszert futtató fürt esetén javasoljuk, hogy site recovery replikációt használjon az SQL Server védelme érdekében.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Az Azure-t az Azure-ba és a helyszíni azure-ba

A Site Recovery nem nyújt vendégfürt-támogatást, ha egy Azure-régióba replikál. Az SQL Server Standard edition nem nyújt alacsony költségű vész-helyreállítási megoldást sem. Ebben az esetben azt javasoljuk, hogy az SQL Server-fürtöt az elsődleges helyen egy önálló SQL Server-példánynak védje, és a másodlagos helyen helyreállítsa azt.

1. Konfiguráljon egy további önálló SQL Server-példányt az elsődleges Azure-régióban vagy a helyszíni helyen.

1. Konfigurálja úgy a példányt, hogy az a védeni kívánt adatbázisok tükrözéseként szolgáljon. A tükrözés konfigurálása nagy biztonságú módban.

1. Konfigurálja a site recovery konfigurálását az elsődleges helyen az [Azure,](azure-to-azure-tutorial-enable-replication.md) [a Hyper-V](site-recovery-hyper-v-site-to-azure.md)vagy a [VMware virtuális gépek hez és a fizikai kiszolgálókhoz.](site-recovery-vmware-to-azure-classic.md)

1. A Site Recovery replikációhasználatával replikálja az új SQL Server-példányt a másodlagos helyre. Mivel ez egy nagy biztonságú tükörpéldány, szinkronizálni fogja az elsődleges fürttel, de replikálja a Site Recovery replikációhasználatával.

   ![Egy szabványos fürt képe, amely az elsődleges hely, a Site Recovery és az Azure közötti kapcsolatot és folyamatot mutatja](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Feladat-visszavételi szempontok

SQL Server Standard fürtök esetén a nem tervezett feladatátvétel utáni feladat-visszavétel sql server biztonsági mentést és visszaállítást igényel. Ez a művelet a tükörpéldánytól az eredeti fürtig történik a tükör újbóli létrehozásával.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Hogyan kap licencet az SQL Server a Site Recovery szolgáltatással együtt?

Az SQL Server hely-helyreállítási replikációját a Frissítési Garancia vész-helyreállítási előnye fedezi. Ez a lefedettség az összes Site Recovery forgatókönyvre vonatkozik: a helyszíni Azure vész-helyreállítási és régióközi Azure IaaS vész-helyreállítási. További információkért tekintse meg az [Azure Site Recovery díjszabását.](https://azure.microsoft.com/pricing/details/site-recovery/)

### <a name="will-site-recovery-support-my-sql-server-version"></a>Támogatja a Site Recovery az SQL Server verzióját?

Site Recovery alkalmazás agnosztikus. A Site Recovery segítségével megvédheti az SQL Server támogatott operációs rendszerre telepített bármely verzióját. További információkért tekintse meg a támogató mátrix a replikált gépek [helyreállítását.](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="next-steps"></a>További lépések

* További információ a [Webhely-helyreállítási architektúráról](site-recovery-components.md).
* Az Azure-beli SQL Server ről további információ a másodlagos Azure-régióban a helyreállításhoz [szükséges magas rendelkezésre állású megoldásokról.](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions)
* Az SQL Database,további információ az [üzletmenet folytonosságáról](../sql-database/sql-database-business-continuity.md) és a [magas rendelkezésre állási](../sql-database/sql-database-high-availability.md) lehetőségek a helyreállítás egy másodlagos Azure-régióban.
* A helyszíni SQL Server-gépekről további információ az Azure virtuális gépek helyreállítási [magas rendelkezésre állási lehetőségeiről.](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions)
