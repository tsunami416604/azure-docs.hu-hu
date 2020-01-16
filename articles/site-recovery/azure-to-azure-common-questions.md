---
title: Gyakori kérdések az Azure-beli virtuális gépek vész-helyreállításáról Azure Site Recovery
description: Ez a cikk az Azure-beli virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdésekre ad választ Azure Site Recovery használatával.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 051e8b0add7cf7ab2c4cb2f02ed4e33ea9a23c9b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75973803"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Gyakori kérdések: Azure – Azure vész-helyreállítás

Ez a cikk az Azure-beli virtuális gépeknek egy másik Azure-régióba való vész-helyreállításával kapcsolatos gyakori kérdésekre ad választ a [site Recovery](site-recovery-overview.md)használatával.


## <a name="general"></a>Általános

### <a name="how-is-site-recovery-priced"></a>Hogyan Site Recovery díjszabása?
Tekintse át [Azure site Recovery díjszabásának](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) részleteit.
### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Mit jelent az Azure Site Recovery szolgáltatás esetében az ingyenes szint?
Az Azure Site Recovery szolgáltatás által védett példányok a védelem első 31 napja során díjmentesek. A 32. naptól kezdődően a példányokra vonatkozó védelem díjának felszámítása a fenti díjszabás alapján történik.
### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 nap során kell valamilyen más Azure-díjat fizetni?
Igen. Bár az Azure Site Recovery szolgáltatás a védett példányok tekintetében díjmentes az első 31 nap során, az Azure Storage szolgáltatás, a tárolási tranzakciók és az adatforgalom díjkötelesek lehetnek. Továbbá a helyreállított virtuális gépekre is vonatkozhatnak Azure-díjak a számítási idő alapján. A díjszabással kapcsolatos részletes információkat [itt](https://azure.microsoft.com/pricing/details/site-recovery) talál

### <a name="where-can-i-find-best-practices-for-azure-vm-disaster-recovery"></a>Hol találhatok bevált eljárásokat az Azure-beli virtuális gépek vész-helyreállításához?
1. [Az Azure – Azure architektúra megismerése](azure-to-azure-architecture.md)
2. [Tekintse át a támogatott és nem támogatott konfigurációkat](azure-to-azure-support-matrix.md)
3. [Vész-helyreállítás beállítása Azure-beli virtuális gépekhez](azure-to-azure-how-to-enable-replication.md)
4. [Feladatátvételi teszt futtatása](azure-to-azure-tutorial-dr-drill.md)
5. [Feladatátvétel és feladat-visszavétel az elsődleges régióba](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-guaranteed-in-the-target-region"></a>Hogyan garantált a kapacitás a megcélzott régióban?
A Site Recovery csapat együttműködik az Azure Capacity Management csapatával az infrastruktúra megfelelő kapacitásának megtervezése és annak biztosítása érdekében, hogy a Site Recovery által védett virtuális gépeket a rendszer sikeresen üzembe helyezi a feladatátvétel kezdeményezése esetén.

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Replikálhatók az Azure Disk Encryption szolgáltatáson keresztül engedélyezett virtuális gépek?

Igen, Site Recovery támogatja a virtuális gépek vész-helyreállítását az Azure Disk Encryption (ADE) engedélyezésével. Ha engedélyezi a replikációt, a rendszer az összes szükséges lemez-titkosítási kulcsot és titkot átmásolja a forrás régiójából a felhasználói környezetben lévő célként megadott régióba. Ha nem rendelkezik megfelelő engedélyekkel, a rendszer használatra kész parancsfájlt adhat a biztonsági rendszergazdának a kulcsok és a titkos kódok másolásához.

- Site Recovery támogatja az ADE használatát a Windows rendszerű Azure-beli virtuális gépekhez.
- Site Recovery támogatja az ADE 0,1-es verzióját, amelynek sémája Azure Active Directory (HRE) és 1,1-es verzióval rendelkezik, HRE nélkül. [További információk](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schemata).
- Az ADE 1,1-es verziója a Windows rendszerű virtuális gépeken felügyelt lemezeket kell használni.
- [További](azure-to-azure-how-to-enable-replication-ade-vms.md) információ a titkosított virtuális gépek replikálásának engedélyezéséről.



### <a name="can-i-replicate-vms-to-another-subscription"></a>Replikálhatók a virtuális gépek egy másik előfizetésre?
Igen, az Azure-beli virtuális gépeket egy másik előfizetésre is replikálhatja ugyanazon az Azure AD-bérlőn belül.
A DR [előfizetések közötti](https://azure.microsoft.com/blog/cross-subscription-dr) konfigurálása egyszerű. A replikálás időpontjában választhat másik előfizetést.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Replikálható a zóna-rögzített Azure-beli virtuális gépek egy másik régióba?
Igen, a [zóna által rögzített virtuális gépeket replikálhatja](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) egy másik régióba.

### <a name="can-i-exclude-disks"></a>Ki lehet zárni a lemezeket?

Igen, a PowerShell használatával kizárhatja a lemezeket a védelem időpontjában. További információért lásd a [cikket](azure-to-azure-exclude-disks.md)

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Hozzáadhatok új lemezeket a replikált virtuális gépekhez, és engedélyezheti számukra a replikálást?

Igen, ez támogatott a felügyelt lemezekkel rendelkező Azure-beli virtuális gépek esetében. Amikor új lemezt ad hozzá egy replikációra engedélyezett Azure-beli virtuális géphez, a virtuális gép replikálási állapota figyelmeztetést jelenít meg, amely azt jelzi, hogy a virtuális gép egy vagy több lemeze védelmet biztosít. Engedélyezheti a további lemezek replikálását.
- Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés a kezdeti replikálás után eltűnik.
- Ha úgy dönt, hogy nem engedélyezi a lemez replikálását, kiválaszthatja, hogy elhagyhatja a figyelmeztetést.
- Ha feladatátvételt hajt végre egy olyan virtuális gépen, amelyhez lemezt ad hozzá, és engedélyezi a replikációt, a replikációs pontok megjelenítik a helyreállításhoz elérhető lemezeket. Ha például egy virtuális gép egyetlen lemezzel rendelkezik, és egy újat ad hozzá, a lemez hozzáadása előtt létrehozott replikációs pontok azt mutatják, hogy a replikációs pont "2 lemezből álló 1".

A Site Recovery nem támogatja a lemez "gyors eltávolítását" egy replikált virtuális gépről. Ha eltávolít egy VM-lemezt, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.


### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran lehet replikálni az Azure-ba?
Az Azure-beli virtuális gépek másik Azure-régióba történő replikálásakor a replikáció folyamatos. További információkért lásd az [Azure – Azure replikálási architektúrát](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-to-migrate-vms"></a>Replikálható a virtuális gépek egy adott régión belül? A virtuális gépek áttelepítésére van szükségem.
Nem használhat Azure – Azure DR-megoldást a virtuális gépek régión belüli replikálására.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Replikálhat virtuális gépeket bármely Azure-régióba?
A Site Recovery használatával replikálhatja és helyreállíthatja a virtuális gépeket az azonos földrajzi fürtön belüli két régió között. A földrajzi fürtök adatkéséssel és szuverenitással vannak meghatározva. További információ: Site Recovery [régió támogatási mátrixa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Az Site Recovery internetkapcsolat szükséges?

Nem, Site Recovery internetkapcsolatra nincs szükség. Azonban Site Recovery URL-címek és IP-címtartományok elérését igényli, ahogy azt [ebben a cikkben](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)is említettük.

### <a name="can-i-replicate-the-application-having-separate-resource-group-for-separate-tiers"></a>Elvégezhető-e az alkalmazás replikálása különálló erőforráscsoporthoz külön-külön rétegek esetén?
Igen, replikálhatja az alkalmazást, és a vész-helyreállítási konfigurációt külön erőforráscsoporthoz is megtarthatja.
Ha például az egyes rétegek alkalmazásokhoz, az adatbázishoz és a web-hoz külön erőforráscsoport van, akkor az összes szinten történő védelem érdekében kattintson háromszor a [replikálás varázslóra](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) . A Site Recovery három különböző erőforráscsoport esetében fogja replikálni a három szintet.

## <a name="replication-policy"></a>Replikációs szabályzat

### <a name="what-is-a-replication-policy"></a>Mi a replikációs házirend?
Meghatározza a helyreállítási pontok megőrzési előzményeinek és az alkalmazás-konzisztens Pillanatképek gyakoriságának beállításait. Alapértelmezés szerint a Azure Site Recovery új replikációs házirendet hoz létre az alapértelmezett beállításokkal:

* 24 óra a helyreállítási pontok megőrzési előzményeihez.
* 60 perc az alkalmazás-konzisztens Pillanatképek gyakoriságához.

[További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?
Egy összeomlás-konzisztens helyreállítási pont a lemezen tárolt adatoknak felel meg, mintha a virtuális gép összeomlott volna, vagy a hálózati tápkábelt a kiszolgálóról húzta le a pillanatkép időpontjában. A pillanatkép elkészítésekor nem tartalmaz semmit a memóriában.

Napjainkban a legtöbb alkalmazás helyreállíthatja az összeomlás-konzisztens pillanatképeket is. Az összeomlás-konzisztens helyreállítási pontok általában elegendőek az adatbázis nélküli operációs rendszerekhez és alkalmazásokhoz, például a fájlkiszolgálók, a DHCP-kiszolgálók és a nyomtatókiszolgálók számára.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Milyen gyakorisággal történik az összeomlás-konzisztens helyreállítási pontok létrehozása?
A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot.

### <a name="what-is-an-application-consistent-recovery-point"></a>Mi az az alkalmazás-konzisztens helyreállítási pont?
Az alkalmazással konzisztens helyreállítási pontok az alkalmazás-konzisztens Pillanatképek alapján jönnek létre. Az alkalmazás-konzisztens helyreállítási pontok ugyanazokat az adatokkal rögzítik, mint az összeomlás-konzisztens Pillanatképek, a memóriában lévő összes adatmennyiség és a folyamatban lévő összes tranzakció hozzáadásával.
A további tartalom miatt az alkalmazás-konzisztens Pillanatképek a leginkább érintettek, és a leghosszabb időt kell elvégezniük. Javasoljuk, hogy az alkalmazás-konzisztens helyreállítási pontokat az adatbázis-operációs rendszerekhez és alkalmazásokhoz, például a SQL Serverhoz.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Milyen hatással van az alkalmazás-konzisztens helyreállítási pontok alkalmazása az alkalmazások teljesítményére?
Ha az alkalmazással konzisztens helyreállítási pontok rögzítik a memóriában és a folyamatban lévő összes adatmennyiséget, a keretrendszerhez, például a VSS-hez kell fokozatos leválasztása az alkalmazást a Windowsban. Ez, ha nagyon gyakran történik, a teljesítmény hatással lehet, ha a munkaterhelés már nagyon elfoglalt. Általában azt javasoljuk, hogy ne használja az alkalmazás-konzisztens helyreállítási pontokat a nem adatbázis-alapú számítási feladatokhoz, és még az adatbázis számítási feladataihoz is elég 1 óra.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mi a minimális gyakorisága az alkalmazás-konzisztens helyreállítási pontok generálásának?
A Site Recovery egy olyan alkalmazás-konzisztens helyreállítási pontot hozhat létre, amelynek minimális gyakorisága 1 óra.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan történik a helyreállítási pontok létrehozása és mentése?
Annak megismeréséhez, hogy Site Recovery hozza létre a helyreállítási pontokat, tekintsük át a replikációs házirendet, amely 24 órás helyreállítási pont megőrzési időszakot tartalmaz, valamint egy 1 órás alkalmazás-konzisztens gyakorisági pillanatképet.

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. A felhasználó nem módosíthatja ezt a gyakoriságot. Tehát az elmúlt 1 órában a felhasználó 12 Crash-konzisztens pontot és 1 alkalmazás-konzisztens pontot fog választani. Az idő előrehaladtával Site Recovery az elmúlt 1 órában az összes helyreállítási pontot, és óránként csak 1 helyreállítási pontot takarít meg.

A következő képernyőkép szemlélteti a példát. A képernyőképen:

1. Az elmúlt 1 órában rövidebb idő alatt a helyreállítási pontok 5 perces gyakorisággal vannak ellátva.
2. Az elmúlt 1 órában eltelt idő után Site Recovery csak 1 helyreállítási pontot tart.

   ![Generált helyreállítási pontok listája](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Meddig lehet visszaállítani?
A legrégebben használható helyreállítási pont 72 óra.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mi történik, ha 24 órás replikációs házirendtel rendelkezem, és egy probléma megakadályozza, hogy Site Recovery a helyreállítási pontokat 24 óránál hosszabb ideig hozza létre? A korábbi helyreállítási pontok elvesznek?
Nem, Site Recovery fogja megőrizni az összes korábbi helyreállítási pontot. Ebben az esetben a helyreállítási pontok adatmegőrzési időszaka alapján a Site Recovery csak akkor váltja ki a legrégebbi pontot, ha az új pontok generációja van. Ebben az esetben, mivel egy probléma miatt nem jön létre új helyreállítási pont, a régi pontok érintetlenek maradnak, ha elérjük a megőrzöttség ablakát.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Ha a replikáció engedélyezve van egy virtuális gépen, hogyan változtathatom meg a replikációs házirendet?
Nyissa meg **site Recovery** -tároló > **site Recovery infrastruktúra** > **replikációs házirendek**lehetőséget. Válassza ki a szerkeszteni kívánt szabályzatot, és mentse a módosításokat. A módosítások az összes meglévő replikációra érvényesek lesznek.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Az összes helyreállítási pont a virtuális gép teljes másolatát vagy a különbözetet?
Az első létrehozott helyreállítási pont a teljes másolattal rendelkezik. Az egymást követő helyreállítási pontok különbözeti változásokkal rendelkeznek.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Növeli a helyreállítási pontok megőrzési időtartamát?
Igen. Ha 24 órától 72 óráig növeli a megőrzési időtartamot, Site Recovery a helyreállítási pontokat további 48 óráig fogja menteni. A hozzáadott idő tárolási díjat von maga után. Ha például egyetlen helyreállítási pont a 10 GB-os különbözeti változásokkal rendelkezik, és a GB-os költség $0,16 havonta, a további díjak havi $1,6 * 48.

## <a name="multi-vm-consistency"></a>Több virtuális gépre kiterjedő konzisztencia

### <a name="what-is-multi-vm-consistency"></a>Mi a több virtuális gépre kiterjedő konzisztencia?
Ez azt jelenti, hogy a helyreállítási pont konzisztens az összes replikált virtuális gépen.
Site Recovery lehetőséget biztosít a "több virtuális gépre kiterjedő konzisztencia" beállítására, amely kiválasztásakor létrehoz egy replikációs csoportot a csoport részét képező összes gép replikálásához.
Az összes virtuális gép megosztott összeomlás-konzisztens és alkalmazás-konzisztens helyreállítási pontokkal fog rendelkezni a feladatátvétel során.
Ugorjon végig az oktatóanyagban a [több virtuális gépre kiterjedő konzisztencia engedélyezéséhez](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-failover-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Az egyetlen virtuális gép feladatátvétele több virtuális gépre kiterjedő konzisztencia-replikációs csoporton belül végezhető?
A "több virtuális gépre kiterjedő konzisztencia" lehetőség kiválasztásával megadhatja, hogy az alkalmazás egy csoporton belüli virtuális gépektől függ. Ezért az egyetlen virtuális gép feladatátvétele nem engedélyezett.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hány virtuális gépet lehet replikálni egy több virtuális GÉPRE kiterjedő konzisztencia-replikációs csoport részeként?
A 16 virtuális gépet egyszerre replikálhatja egy replikációs csoportba.

### <a name="when-should-i-enable-multi-vm-consistency-"></a>Mikor engedélyezzem a több virtuális gépre kiterjedő konzisztenciát?
Mivel ez a CPU-igényes, a több virtuális gépre kiterjedő konzisztencia engedélyezése hatással lehet a munkaterhelés teljesítményére. Ezt csak akkor kell használni, ha a gépek ugyanazt a számítási feladatot futtatják, és több gépen is konzisztensen kell lenniük. Ha például két SQL Server példánya van, és két webkiszolgálója van egy alkalmazásban, akkor a több virtuális gépre kiterjedő konzisztencia csak a SQL Server példányok esetében szükséges.


## <a name="failover"></a>Feladatátvétel

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hogyan biztosítható a kapacitás az Azure-beli virtuális gépek számára a célzott régióban?
A Site Recovery csapat együttműködik az Azure Capacity Management csapatával a megfelelő infrastrukturális kapacitás megtervezése érdekében, így biztosítva, hogy a vész-helyreállítást engedélyező virtuális gépeket a rendszer sikeresen üzembe helyezi a cél régióban a feladatátvétel kezdeményezése során.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással elindíthatja a portálon, vagy a [PowerShell](azure-to-azure-powershell.md) használatával aktiválhatja a feladatátvételt.

### <a name="can-i-retain-a-public-ip-address-after-failover"></a>Megtárolhatom a nyilvános IP-címet a feladatátvétel után?

A feladatátvétel után nem lehet megőrizni a termelési alkalmazás nyilvános IP-címét.
- A feladatátvételi folyamat részeként felépített munkaterhelésekhez hozzá kell rendelni egy Azure nyilvános IP-erőforrást, amely elérhető a célként megadott régióban.
- Ezt manuálisan is megteheti, vagy automatizálhatja egy helyreállítási terv használatával.
- Megtudhatja, hogyan [állíthatja be a nyilvános IP-címeket a feladatátvétel után](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).  

### <a name="can-i-retain-a-private-ip-address-during-failover"></a>Megtartható a privát IP-cím a feladatátvétel során?
Igen, megtarthat egy magánhálózati IP-címet is. Alapértelmezés szerint az Azure-beli virtuális gépek vész-helyreállításának engedélyezésekor Site Recovery a forrás erőforrás-beállítások alapján hozza létre a cél erőforrásokat. – Statikus IP-címmel konfigurált Azure-beli virtuális gépek esetén a Site Recovery a célként megadott virtuális gép esetében ugyanazt az IP-címet próbálja kiépíteni, ha az nincs használatban.
További információ az [IP-címek megtartásáról a feladatátvétel során](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-failover-why-is-the-server-assigned-a-new-ip-address"></a>A feladatátvételt követően a kiszolgáló új IP-címet rendelt hozzá?

Site Recovery megpróbálja megadni az IP-címet a feladatátvétel időpontjában. Ha egy másik virtuális gép ezt a címet veszi, Site Recovery beállítja a következő elérhető IP-címet célként.
További információ a [virtuális hálózatok hálózati leképezésének és IP-címzésének beállításáról](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Mik a **Legutóbbi (legalacsonyabb RPO)** helyreállítási pontok?
A **legújabb (legalacsonyabb RPO)** beállítás először feldolgozza a site Recovery szolgáltatásnak elküldett összes adatát, hogy minden virtuális gép számára létrehozzon egy helyreállítási pontot, mielőtt a művelet elvégezte a feladatátvételt. Ez a beállítás a legalacsonyabb helyreállítási időkorlátot (RPO) adja meg, mert a feladatátvételt követően létrehozott virtuális gép minden, a feladatátvételt kiváltó Site Recovery replikált adattal rendelkezik.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>A **Legutóbbi (legalacsonyabb RPO)** helyreállítási pontok hatással vannak a feladatátvételi RTO?
Igen. Site Recovery a feladatátvétel előtt dolgozza fel az összes függőben lévő adatát, így ez a beállítás a többi lehetőséghez képest magasabb helyreállítási időcélkitűzést (RTO) is tartalmaz.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Mit jelent a helyreállítási pontok **legújabb feldolgozott** lehetősége?
Az **utolsó feldolgozott** lehetőség a tervben lévő összes virtuális gép átadása a site Recovery feldolgozott legújabb helyreállítási pontra. Egy adott virtuális gép legutóbbi helyreállítási pontjának megtekintéséhez tekintse meg a virtuális gép beállításainak **legutóbbi helyreállítási pontjait** . Ez a beállítás alacsony RTO biztosít, mivel a feldolgozatlan adatmennyiségek feldolgozása nem történik meg.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Mi történik, ha az elsődleges régió váratlan kimaradást tapasztal?
A leállás utáni feladatátvételt indíthat. A feladatátvétel végrehajtásához a Site Recovery nem kell kapcsolódnia az elsődleges régióhoz.

### <a name="what-is-a-rto-of-a-vm-failover-"></a>Mi a virtuális gép feladatátvételének RTO?
Site Recovery [RTO SLA-ja 2 óra](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Az idő nagy részében azonban néhány percen belül Site Recovery feladatátvételt hajt végre a virtuális gépeken. A RTO kiszámításához nyissa meg a feladatátvételi feladatokat, amely a virtuális gép üzembe helyezésének idejét mutatja. A helyreállítási terv RTO tekintse meg az alábbi szakaszt.

## <a name="recovery-plans"></a>Helyreállítási tervek

### <a name="what-is-a-recovery-plan"></a>Mi az a helyreállítási terv?
A Site Recovery helyreállítási terve összehangolja a virtuális gépek feladatátvételének helyreállítását. Ezzel a módszerrel a helyreállítás konzisztens, ismételhető és automatizált lehet. A helyreállítási terv a felhasználó számára a következő igényeket javítja:

- A feladatátvétel alatt álló virtuális gépek csoportjának definiálása
- A virtuális gépek közötti függőségek meghatározása, hogy az alkalmazás pontosan felkerüljön
- A helyreállítás automatizálása a virtuális gépek feladatátvételén kívüli feladatok végrehajtásához szükséges egyéni manuális műveletekkel

[További](site-recovery-create-recovery-plans.md) információ a helyreállítási tervekről.

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hogyan valósul meg az előkészítés egy helyreállítási tervben?

Egy helyreállítási tervben több csoportot is létrehozhat az előkészítési sorrend eléréséhez. Egyszerre minden csoport feladatátvételt hajt végre. Azok a virtuális gépek, amelyek ugyanahhoz a csoporthoz tartoznak, feladatátvételt hajtanak végre, majd egy másik csoportot követnek. Ha meg szeretné tudni, hogyan modellezhető egy alkalmazás egy helyreállítási terv használatával, olvassa el [a helyreállítási tervek](recovery-plan-overview.md#model-apps)című témakört.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hogyan találhatom meg a helyreállítási terv RTO?
A helyreállítási terv RTO ellenőrzéséhez végezzen feladatátvételi tesztet a helyreállítási tervhez, és lépjen **site Recovery feladatok**elemre.
A következő példában a SAPTestRecoveryPlan nevű feladat 8 percet és 59 másodpercet vett igénybe az összes virtuális gép feladatátvétele és a megadott műveletek végrehajtása során.

![Site Recovery feladatok listája](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Hozzáadhatok Automation-runbookok a helyreállítási tervhez?
Igen, integrálhatja Azure Automation runbookok a helyreállítási tervbe. [További információk](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ismételt védelem és feladat-visszavétel

### <a name="after-a-failover-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Az elsődleges régióból a vész-helyreállítási régióba irányuló feladatátvétel után a rendszer automatikusan védett DR régióban lévő virtuális gépeket?
Nem. Ha az Azure-beli virtuális gépeket az egyik régióból a másikba hajtja végre, a virtuális gépek [nem](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) védett állapotban kezdődnek a Dr régióban. Ahhoz, hogy a virtuális gépeket az elsődleges régióba lehessen visszaadni, újra kell [védetté](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) tenni a virtuális gépeket a másodlagos régióban.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Az ismételt védelem során a Site Recovery replikálja a teljes adatmennyiséget a másodlagos régióból az elsődleges régióba?
Ez a helyzettől függ. Ha például a forrásoldali virtuális gép létezik, a rendszer csak a forrásfájl és a céllemez közötti változásokat szinkronizálja. Site Recovery kiszámítja a különbségeket a lemezek összehasonlításával, majd átviszi azokat. Ez a folyamat általában néhány órát vesz igénybe. További információ arról, hogy mi történik az ismételt védelem során: az [Azure-beli virtuális gépek ismételt védelme az elsődleges régióra történt]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Mennyi ideig tart a feladat-visszavétel?
Az ismételt védelem után a feladat-visszavételi idő általában az elsődleges régióból a másodlagos régióba történő feladatátvételhez szükséges időtartamhoz hasonlít.

## <a name="capacity"></a>Kapacitás

### <a name="how-is-capacity-assured-in-target-region-for-azure-vms"></a>Hogyan biztosítható a kapacitás az Azure-beli virtuális gépek számára a célzott régióban?
A Site Recovery csapat együttműködik az Azure Capacity Management csapatával a megfelelő infrastrukturális kapacitás megtervezése érdekében, így biztosítva, hogy a vész-helyreállítást engedélyező virtuális gépeket a rendszer sikeresen üzembe helyezi a cél régióban a feladatátvétel kezdeményezése során.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Működik Site Recovery fenntartott példányokkal?
Igen, a vész-helyreállítási régióban vásárolhat [tartalék példányokat](https://azure.microsoft.com/pricing/reserved-vm-instances/) , és site Recovery feladatátvételi műveletek is felhasználják őket. </br> Nincs szükség további konfigurálásra.


## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  
A Site Recovery ISO 27001:2013, 27018, HIPAA, DPA tanúsítvánnyal rendelkezik, és a SOC2 és a FedRAMP ÜSS értékelésének folyamata folyamatban van.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, [Az Azure-ban](https://docs.microsoft.com/azure/storage/storage-service-encryption) a titkosítás és a titkosítás is támogatott.

## <a name="next-steps"></a>Következő lépések
* [Tekintse át](azure-to-azure-support-matrix.md) a támogatási követelményeket.
* [Beállítás](azure-to-azure-tutorial-enable-replication.md) Azure-ról Azure-ba történő replikálásra.
- Ha kérdése van a cikk elolvasása után, tegye közzé őket az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
