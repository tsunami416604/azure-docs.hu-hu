---
title: 'Gyakori kérdések: Az Azure Site Recoveryvel Azure – Azure vészhelyreállítási |} A Microsoft Docs'
description: Ez a cikk gyakori kérdésekre foglalja össze, üzembe helyezésekor meg az Azure virtuális gépek vészhelyreállítása egy másik Azure-régióba az Azure Site Recovery használatával
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 03/18/2019
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 2c1890570f153de68d187c37dc0a7bca156c2d47
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312053"
---
# <a name="common-questions-azure-to-azure-replication"></a>Gyakori kérdések: Azure – Azure replikálás

Ez a cikk a vészhelyreállítás (DR) Azure-beli virtuális üzembe helyezése egy másik Azure-régióba az Azure Site Recovery használatával kapcsolatos gyakori kérdésekre adott válaszokat ismerteti. Ha kérdése van a cikk elolvasása után, el őket az a [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan van a Site Recovery díjszabása?
Felülvizsgálat [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) részleteit.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Mit jelent az Azure Site Recovery szolgáltatás esetében az ingyenes szint?
Az Azure Site Recovery szolgáltatás által védett példányok a védelem első 31 napja során díjmentesek. A 32. naptól kezdődően a példányokra vonatkozó védelem díjának felszámítása a fenti díjszabás alapján történik.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 nap során kell valamilyen más Azure-díjat fizetni?
Igen. Bár az Azure Site Recovery szolgáltatás a védett példányok tekintetében díjmentes az első 31 nap során, az Azure Storage szolgáltatás, a tárolási tranzakciók és az adatforgalom díjkötelesek lehetnek. Továbbá a helyreállított virtuális gépekre is vonatkozhatnak Azure-díjak a számítási idő alapján. Kész részletek a díjszabási [Itt](https://azure.microsoft.com/pricing/details/site-recovery)

### <a name="what-are-the-best-practices-for-configuring-site-recovery-on-azure-vms"></a>Mik azok a Site Recovery konfigurálása az Azure virtuális gépekhez ajánlott eljárásai?
1. [Azure – Azure architektúrájának ismertetése](azure-to-azure-architecture.md)
2. [Tekintse át a támogatott és nem támogatott konfigurációk](azure-to-azure-support-matrix.md)
3. [Vészhelyreállítás beállítása az Azure virtuális gépek](azure-to-azure-how-to-enable-replication.md)
4. [Feladatátvételi teszt futtatása](azure-to-azure-tutorial-dr-drill.md)
5. [Feladatátadás és feladatátvétel az elsődleges régióba](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Az Azure disk encryption által engedélyezett virtuális gépeket lehet replikálni?
Igen, replikálhatja azokat. Tekintse meg a cikket [replikálása az Azure disk encryption engedélyezve van a virtuális gépeket egy másik Azure-régióba](azure-to-azure-how-to-enable-replication-ade-vms.md). Jelenleg az Azure Site Recovery támogatja csak az Azure virtuális gépek, amelyek a Windows operációs rendszer fut, és engedélyezve titkosítás az Azure Active Directory (Azure AD-) alkalmazások.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Replikálhatok virtuális gépeket egy másik előfizetéshez?
Igen, az Azure virtuális gépeket replikálhatja az Azure AD-bérlőhöz belül egy másik előfizetésbe.
Vészhelyreállítás konfigurálása [előfizetésekben](https://azure.microsoft.com/blog/cross-subscription-dr) egyszerű. Kiválaszthat egy másik előfizetést, a replikáció során.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Replikálhatok. zóna – rögzíthetők az Azure virtuális gépeket egy másik régióban?
Igen, akkor is [. zóna – rögzíthetők a virtuális gépek replikálása](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) egy másik régióba.

### <a name="can-i-exclude-disks"></a>Kizárhatok egyes lemezek?

Igen, olyan lemezeket zárhat a védelem idején PowerShell használatával. További információkért tekintse meg [cikk](azure-to-azure-exclude-disks.md)

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?
Replikációs akkor folyamatos, ha az Azure virtuális gépeket replikál egy másik Azure-régióba. További információkért lásd: a [Azure – Azure replikációs architektúra](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Egy adott régión belül a virtuális gépeket lehet replikálni? Ezt a virtuális gépek áttelepítése van szükségem.
Egy adott régión belül a virtuális gépek replikálása az Azure – Azure Vészhelyreállítási megoldás nem használható.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Replikálhatok virtuális gépeket minden Azure-régióban?
A Site Recovery replikálja, és a virtuális gépek helyreállítása ugyanazon a földrajzi fürtön belül bármely két régió között. Földrajzi fürtök adatkésleltetést és szem előtt az van megadva. További információkért lásd: a Site Recovery [régió támogatási mátrix](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>A Site Recovery igényel internetkapcsolatot?

A Site Recovery nem, nem szükséges az internetkapcsolat. Hozzáférést igényelnek a Site Recovery URL-címek és IP-tartományok, említetteknek megfelelően, de [Ez a cikk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges).

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Replikálhatok külön szinteket külön erőforráscsoportot kellene az alkalmazást? 
Igen, replikálja az alkalmazást, és ne a vész-helyreállítási konfiguráció külön erőforráscsoportot túl.
Például egy alkalmazást a minden alkalmazás, db, és külön erőforráscsoportot a webes szint esetében, akkor a elemre kell kattintania, a [replikálás varázsló](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) háromszor való védelme minden szinten. Az ASR replikálja ezeket mindhárom szintet három másik erőforráscsoportban található.

## <a name="replication-policy"></a>Replikációs szabályzat

### <a name="what-is-a-replication-policy"></a>Mi a replikációs szabályzat?
Azt határozza meg a helyreállítási pontok megőrzési előzményeit és az alkalmazáskonzisztens pillanatképek gyakorisága beállításait. Alapértelmezés szerint az Azure Site Recovery létrehoz egy új replikációs házirendet az alapértelmezett beállításokkal:

* 24 órát, hogy a helyreállítási pontok megőrzési előzményekhez.
* az alkalmazáskonzisztens pillanatképek gyakorisága 60 perc alatt.

[További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?
Összeomlás-konzisztens helyreállítási pont a lemezen lévő adatok jelöli, mint ha a virtuális gép leállt, vagy a tápkábel felhasználásával lett létrehozva a kiszolgálóról a pillanatkép időpontjában. Nem tartalmazza, amelyeket volt, a memória, ha a pillanatkép. 

Napjainkban a legtöbb alkalmazás helyreállíthatja jól összeomlás-konzisztens pillanatképekkel. Összeomlás-konzisztens helyreállítási pont elég általában a nem adatbázis-operációs rendszerek és alkalmazások, például fájlkiszolgálókat, a DHCP-kiszolgálók és a nyomtatókiszolgálók.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Mi az az összeomlás-konzisztens helyreállítási pont létrehozásának gyakorisága?
A Site Recovery 5 percenként összeomlás-konzisztens helyreállítási pont létrehozása.

### <a name="what-is-an-application-consistent-recovery-point"></a>Mi az az alkalmazáskonzisztens helyreállítási pontot? 
Alkalmazáskonzisztens helyreállítási pontok az alkalmazáskonzisztens pillanatképek jönnek létre. Alkalmazáskonzisztens helyreállítási pontok rögzítése összeomlás-konzisztens pillanatképekkel, a memóriában lévő összes adatot, és minden folyamatban lévő tranzakciót is ugyanazokat az adatokat. 

Miatt további tartalmak a alkalmazáskonzisztens pillanatképek a legtöbb metódusa vesz, és tegye meg a legrégebb végrehajtásához. Azt javasoljuk, hogy az adatbázis operációs rendszerek és alkalmazások, például az SQL Server alkalmazáskonzisztens helyreállítási pontjait.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Mit jelent alkalmazásteljesítmény alkalmazáskonzisztens helyreállítási pontokat a hatását?
Alkalmazáskonzisztens helyreállítási pontok összes adatot a memória és a folyamat rögzíti a mérlegeli igényel a keretrendszert, például VSS letiltásától az alkalmazás windows rendszeren. Ha nagyon gyakran meg lehet a teljesítményt, ha a számítási feladat már nagyon elfoglalt. Általában javasolt nem 1 óra használata kevésbé gyakori alkalmazáskonzisztens helyreállítási pontok adatbázis-számítási feladatokhoz és adatbázis-munkaterhelés esetén is elegendő. 

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mi az a minimális gyakoriságot a alkalmazáskonzisztens helyreállítási pont létrehozásakor?
A Site Recovery segítségével hoz létre egy alkalmazáskonzisztens helyreállítási pont a minimális gyakoriság (1 óra).

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan helyreállítási pontok létrehozott és mentett?
Szeretné megtudni, hogyan hoz létre a Site Recovery helyreállítási pontok, vessünk egy példát egy replikációs szabályzatot, amely a helyreállítási pontok adatmegőrzési időtartam 24 óra és a egy alkalmazás-konzisztens gyakoriság pillanatkép 1 óra.

A Site Recovery 5 percenként összeomlás-konzisztens helyreállítási pont létrehozása. A felhasználó nem módosíthatja a gyakorisága. Az elmúlt 1 óra, a felhasználónak kell 12 összeomlás-konzisztens, pontokat és 1 alkalmazáskonzisztens közül választhat mutat. Az idő előrehaladtával összes a helyreállítási pontok túl az elmúlt 1 óra, és menti a csak 1 helyreállítási pont óránként, a Site Recovery szilva.

Az alábbi képernyőfelvételen látható a példa szemlélteti. A képernyőképen látható:

1. Ideje kevesebb, mint az elmúlt 1 óra vannak a helyreállítási pontok, 5 perces gyakorisággal.
2. Ideje meghaladja az elmúlt 1 óra a Site Recovery biztosítja, hogy csak 1 helyreállítási pont.

   ![A létrehozott helyreállítási pontok listája](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Milyen biztonsági állíthatja helyre?
A legrégebbi helyreállítási pont használható érték 72 óra.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mi történik, ha az egy replikációs házirendet, 24 órában és a egy probléma megakadályozza, hogy a Site Recovery létrehozni helyreállítási pontokból 24 óránál tovább áll? Elvesznek az előző helyreállítási pontokhoz?
Nem, a Site Recovery fogja megőrizni a korábbi helyreállítási pontjait. Függően a helyreállítási pontok megőrzési időtartamát, 24 órában ebben az esetben a Site Recovery váltja fel legrégebbi pont csak akkor, ha van egy új pontok generációja. Ebben az esetben minden olyan új helyreállítási pont jön létre, néhány probléma miatt nem lesz, mint a régi pontok csoportengedély változatlan marad eljut a adatmegőrzési időszak után.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Miután egy virtuális gépen engedélyezve van a replikáció, hogyan módosíthatom a replikációs szabályzat? 
Lépjen a **Site Recovery-tároló** > **Site Recovery-infrastruktúra** > **replikációs házirendek**. Válassza ki a házirendet, amelyet szerkeszteni és menteni a módosításokat. Minden olyan változás túl a meglévő replikálását alkalmazza. 

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>A helyreállítási pontok teljes másolat, a virtuális gép vagy különbségi vannak?
Az első helyreállítási pontot, amely akkor jön létre a teljes példánnyal rendelkezik. Egymást követő helyreállítási pontot kell a változásokat.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>A helyreállítási pontok megőrzési időszakának megnöveli a tárolási költségek?
Igen. Ha növeli a megőrzési időtartam 24 óra, 72 óra, a Site Recovery menti a helyreállítási pontok egy további 48 órán át. A hozzáadott idő tárolási díjakat számítunk fel. Például ha egyetlen helyreállítási pont rendelkezik az új módosításokkal, 10 GB-os és a GB-onkénti költség 0,16 $ / hó, a további díjakat lenne $ 1.6-os * 48 havonta.

## <a name="multi-vm-consistency"></a>Több virtuális gépre kiterjedő konzisztencia 

### <a name="what-is-multi-vm-consistency"></a>Mi az virtuális gépre kiterjedő konzisztencia?
Ez azt jelenti, hogy így arról, hogy a helyreállítási pont konzisztens az összes a replikált virtuális gépek között.
A Site Recovery "Virtuális gépre kiterjedő konzisztencia,", amely válassza ki, ha létrehoz egy replikációs csoport összes gépek együttes replikálásához a csoport részét képező lehetőséget biztosít.
A virtuális gépek lesz megosztott összeomlás-konzisztens és alkalmazáskonzisztens helyreállítási pontokat, hogy feladatátvételkor.
Olvassa el az oktatóanyag [több virtuális gépre kiterjedő konzisztencia engedélyezése](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Helyezhetem el a feladatátvételt egyetlen virtuális gép egy adott virtuális gépre kiterjedő konzisztencia replikációs csoport?
A "Virtuális gépre kiterjedő konzisztencia" lehetőség kiválasztásával, vannak feltüntetve, hogy az alkalmazás maga csoportban lévő összes virtuális gépet. Ezért egyetlen virtuális gép feladatátvétele nem engedélyezett. 

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hány virtuális gépet is replikálni a virtuális gépre kiterjedő konzisztencia replikációs csoport részeként?
16 virtuális gépeket együtt, egy replikációs csoport is lehet replikálni.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Mikor kell engedélyezni a virtuális gépre kiterjedő konzisztencia?
Intenzív CPU, mert több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a számítási feladatok teljesítményére. Akkor kell használni, csak akkor, ha a gépek ugyanazt a számítási feladatot futtat, és több gép közötti konzisztenciára van szükség. Például ha két SQL Server-példányokat, és két olyan webkiszolgáló egy alkalmazásban, rendelkezhet több virtuális gépre kiterjedő konzisztencia esetében csak az SQL Server-példányokat.


## <a name="failover"></a>Feladatátvétel

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A portálon egyetlen kattintással megkezdése folyamatban lévő feladatátvételi teszteket, vagy használhatja [PowerShell](azure-to-azure-powershell.md) feladatátvétel indításához. 

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Nyilvános IP-cím megőrizhetem a feladatátvételt követően?

A nyilvános IP-címét a termelési alkalmazások *nem őrzi meg a feladatátvételi*. A számítási feladatok kerülnek sorra, a feladatátvételi folyamat során hozzá kell rendelni az Azure nyilvános IP-erőforrás, amely a célként megadott régióban érhető el. Hajtsa végre ezt a lépést manuálisan vagy automatikusan a helyreállítási terv keretében is. Nyilvános IP-cím hozzárendelése a helyreállítási terv használatával, lásd: [a feladatátvételt követően nyilvános IP-címek beállítása](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Magánhálózati IP-cím megőrizhetem feladatátvétel során?
Igen, őrizheti meg magánhálózati IP-cím. Alapértelmezés szerint a Vészhelyreállítás Azure-beli virtuális gépek engedélyezésekor Site Recovery létrehoz célerőforrások adatforrás-erőforrás beállítások alapján. Az Azure virtuális gépek statikus IP-címmel konfigurálva a Site Recovery megpróbálja kiépítése a cél virtuális Gépen, azonos IP-címét, ha nem használja. Eltérő körülmények között a privát IP-cím megőrzése, lásd: [megőrzése IP-címek feladatátvétel során](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-the-server-doesnt-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-a-new-ip-address"></a>A feladatátvételt követően a kiszolgáló nem rendelkezik a azonos IP-címét a forrásoldali virtuális Géppel. Miért van, egy új IP-cím hozzárendelve?

A Site Recovery megpróbálja adja meg az IP-címet a feladatátvétel időpontjában. Ha egy másik virtuális gépet tart, hogy a cím, a Site Recovery és a cél állítja be a következő elérhető IP-címe. Hogyan kezeli a Site Recovery-címzés teljes ismertetése, lásd: [hálózatleképezés és virtuális hálózatok IP-címzés beállítása](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Mik a **legutóbbi (legkisebb helyreállítási Időkorlát)** helyreállítási pontok?
A **legutóbbi (legkisebb helyreállítási Időkorlát)** lehetőség először feldolgozza a helyreállítási pont létrehozása az egyes virtuális Gépekhez,-ba irányuló feladatátvétel előtt a Site Recovery szolgáltatásba küldött összes adatot. Ezt a lehetőséget biztosít a a legalacsonyabb helyreállítási időkorlátot (RPO), mert a virtuális gép létrehozása után a feladatátvételi rendelkezik a feladatátvétel elindításakor a Site Recoverybe replikált összes adattal.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Tegye **legutóbbi (legkisebb helyreállítási Időkorlát)** helyreállítási pontok hatással a feladatátvételi RTO?
Igen. A Site Recovery feladatátvétele, mielőtt dolgozza fel az összes függőben lévő adatokat, így ezzel a beállítással rendelkezik egy újabb helyreállítási időre vonatkozó célkitűzés (RTO) mint más beállításokat a korábban megszokott.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Mire jó a **legutóbb feldolgozott** lehetőség a helyreállítási pontok mean?
A **utoljára feldolgozva** lehetőséget minden virtuális gépre a legújabb helyreállítási tervben feladatátvételt hajt végre a Site Recovery feldolgozott mutasson. Tekintse meg a legutóbbi helyreállítási pont egy adott virtuális géphez, ellenőrizze a következőket **legutóbbi helyreállítási pontok** a virtuális gép beállításaiban. Ezt a lehetőséget biztosít egy alacsony RTO, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozása.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Ha a két Azure-régiók között végzek, mi történik, ha a elsődleges régiómban esetleges váratlan leállásakor?
A feladatátvétel után a szolgáltatáskimaradás elhárítása után is indíthat. A Site Recovery nem követeli meg, hajtsa végre a feladatátvételt az elsődleges régióból.

### <a name="what-is-a-rto-of-a-virtual-machine-failover-"></a>Mi az a virtuális gép feladatátvétel RTO?
A Site Recovery rendelkezik egy [2 óra RTO szolgáltatásiszint-szerződés](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Azonban az esetek többségében a Site Recovery virtuális gépek feladatátvétele percen belül. Kiszámíthatja az RTO nyissa meg a feladatátvételt feladatok, amely az időt jeleníti meg a virtuális Géphez csatlakozva tartottak. Helyreállítási terv az RTO, tekintse meg a szakasz alatt. 

## <a name="recovery-plans"></a>Helyreállítási tervek

### <a name="what-is-a-recovery-plan"></a>Mi a helyreállítási terv?
A helyreállítási terv a Site Recovery koordinálja a virtuális gépek feladatátvételi helyreállítás. Győződjön meg arról, a helyreállítás következetesen pontos, megismételhető és automatizált segít. A helyreállítási terv megszünteti a felhasználó a következő igényeit:

- A feladataikat együtt átadó virtuális gépek egy csoport meghatározása
- A virtuális gépek közötti függőségek meghatározása, hogy az alkalmazás betölt pontosan
- Egyéni manuális műveletek a feladatátvételt a virtuális gépek eltérő feladatok eléréséhez és a helyreállítás automatizálása

[További](site-recovery-create-recovery-plans.md) helyreállítási tervek ismertetése.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hogyan valósul alkalmazás-előkészítés a helyreállítási tervet?

A helyreállítási terv az alkalmazás-előkészítés eléréséhez több csoportot is létrehozhat. Minden csoport átadja a feladatokat egy időben. Virtuális gépek számára a ugyanazon csoport nem része felett együtt, majd egy másik csoportot. Egy alkalmazás modellezésére a helyreállítási terv használatával kapcsolatban lásd: [helyreállítási tervek](recovery-plan-overview.md#model-apps). 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hol találhatom meg a helyreállítási terv RTO?
A helyreállítási terv RTO ellenőrzéséhez hajtsa végre a helyreállítási terv feladatátvételi tesztjét, és nyissa meg **Site Recovery-feladatok**.
A következő példában a feladat SAPTestRecoveryPlan nevű tartott 8 perc és 59 másodperc átadja a feladatokat a virtuális gépeket, és végezze el a megadott műveleteket.

![Site Recovery-feladatok listája](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>A helyreállítási tervbe is hozzáadhatok automation-runbookok?
Igen, integrálható az Azure Automation-runbookok a helyreállítási tervbe. [További információk](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ismételt védelem és a feladat-visszavétel 

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>A feladatátvétel után az elsődleges régióból egy vész-helyreállítási régióba vannak a virtuális gépek automatikusan védett DR régióban?
Nem. Ha Ön [átadja a feladatokat](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) Azure virtuális gépek egyik régióból a másikba, a virtuális gépek indítása a DR régióban egy nem védett állapotban. Feladat-visszavételt a virtuális gépek az elsődleges régióra, kell [ismételt védelme](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) a virtuális gépek a másodlagos régióban.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Ismételt védelem időpontjában nem a Site Recovery mindazok az adatok a másodlagos régió-bA replikálni az elsődleges régió?
Azt az adott helyzettől függ. Például ha a forrásrégióban virtuális gép létezik, csak között a forrás és a cél lemezzel változtatások vannak szinkronizálva. A Site Recovery a különbözeti kiszámítja a lemezek összehasonlításával, és ezután az adatok átvitele. Ez a folyamat általában néhány órát vesz igénybe. Ismételt védelem során történtekkel kapcsolatos további információkért lásd: [ismételt védelem sikertelen volt az Azure virtuális gépen az elsődleges régióba]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Mennyi idő mindezt végezze el a feladat-visszavételt?
Ismételt védelem, miután idő a feladat-visszavételhez hasonlít általában az idő a feladatátvételhez az elsődleges régióból egy másodlagos régióba. 

## <a name="capacity"></a>Kapacitás
### <a name="does-site-recovery-work-with-reserved-instance"></a>A fenntartott példány működik a Site Recovery?
Igen, akkor is vásárolható [fenntartott példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/) a DR régióban, és automatikus feladatátvételi műveletek fogja használni őket. </br> További konfiguráció nélkül nem szükséges, hogy az ügyfeleknek.


## <a name="security"></a>Biztonság
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, a Site Recovery nem intercept a replikált adatokat, és nem rendelkezik a virtuális gépeken futó bármilyen információ. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  
Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, mindkét – az átvitel közbeni titkosítás és [titkosítás az Azure-ban](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.

## <a name="next-steps"></a>További lépések
* [Felülvizsgálat](azure-to-azure-support-matrix.md) követelmények támogatására.
* [Állítsa be a](azure-to-azure-tutorial-enable-replication.md) replikáció Azure-bA.
