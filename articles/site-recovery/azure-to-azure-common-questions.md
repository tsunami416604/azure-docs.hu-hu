---
title: Gyakori kérdések az Azure VM vész-helyreállítási az Azure Site Recovery
description: Ez a cikk az Azure Site Recovery használatakor az Azure-beli virtuális gép vész-helyreállítási gyakori kérdéseire válaszol.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7d3bcc32dc8f1412a5adbc175a5f8618628bce83
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547895"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Gyakori kérdések: Azure-ból Azure-ba irányuló vészhelyreállítás

Ez a cikk az Azure Site [Recovery](site-recovery-overview.md)használata kori azure-beli virtuális gépek vészhelyreállításával kapcsolatos gyakori kérdésekre ad választ.

## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan történik a Site Recovery ára?

Tekintse át [az Azure Site Recovery díjszabását a virtuális gépekhez.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hogyan működik az Azure Site Recovery ingyenes szintje?

Az Azure Site Recovery minden olyan példány, amely a védelem első 31 napjára ingyenes. Ezen időszak után az egyes példányok védelme az [Azure Site Recovery díjszabása](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)az Azure virtuális gépek.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 napban felmerülnek-e egyéb Azure-díjak?

Igen. Annak ellenére, hogy az Azure Site Recovery ingyenes a védett példány első 31 napjában, díjat felkérhet az Azure Storage- és tárolási tranzakciókért és az adatátvitelért. A helyreállított virtuális gép azure-számítási díjakat is felszámíthat. Az [Azure Site Recovery díjszabásával](https://azure.microsoft.com/pricing/details/site-recovery)kapcsolatos részletes adatok.

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Melyek az Azure Virtual Machines vész-helyreállítási ajánlott eljárásai?

1. [Az Azure azure-beli architektúra megismerése](azure-to-azure-architecture.md)
1. [Tekintse át a támogatott és a nem támogatott konfigurációkat](azure-to-azure-support-matrix.md)
1. [Vészhelyreállítás beállítása az Azure-beli virtuális gépekhez](azure-to-azure-how-to-enable-replication.md)
1. [Feladatátvételi teszt futtatása](azure-to-azure-tutorial-dr-drill.md)
1. [Feladatátvétel és feladat-visszavétel az elsődleges régióba](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Hogyan biztosítják a kapacitást a célrégióban?

A Site Recovery csapata és az Azure kapacitáskezelési csapata megfelelő infrastruktúra-kapacitást tervez. Feladatátvétel indításakor a csapatok is segítenek biztosítani, hogy a Site Recovery által védett virtuálisgép-példányok a célrégióban települnek.

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Replikálható az Azure lemeztitkosításán keresztül engedélyezett virtuális gépek replikálása?

Igen. A Site Recovery támogatja az Azure Disk Encryption engedélyezett virtuális gépek vészhelyreállítási szolgáltatását. Ha engedélyezi a replikációt, az Azure átmásolja az összes szükséges lemeztitkosítási kulcsot és titkos kulcsot a forrásrégióból a felhasználói környezetben lévő célrégióba. Ha nem rendelkezik a megfelelő engedélyekkel, a biztonsági rendszergazda parancsfájl segítségével másolhatja a kulcsokat és a titkos kulcsokat.

- A Site Recovery támogatja a Windows rendszert futtató Azure-beli virtuális gépek Azure lemeztitkosítását.
- A Site Recovery támogatja az Azure Disk Encryption 0.1-es verzióját, amely az Azure Active Directoryt (Azure AD) igénylő sémával rendelkezik. A Site Recovery az 1.1-es verziót is támogatja, amely hez nincs szükség az Azure AD-re. [További információ az Azure lemeztitkosításbővítmény-sémáról.](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema)
  - Az Azure Disk Encryption 1.1-es verziójához a Windows virtuális gépeket felügyelt lemezekkel kell használnia.
  - [További információ](azure-to-azure-how-to-enable-replication-ade-vms.md) a titkosított virtuális gépek replikációjának engedélyezéséről.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Lehetséges a virtuális gépek másik előfizetésbe történő replikálása?

Igen, replikálhatja az Azure virtuális gépekegy másik előfizetésugyanabban az Azure AD-bérlőn belül.

Konfigurálja a [vészhelyreállítást az előfizetések között](https://azure.microsoft.com/blog/cross-subscription-dr) egy másik előfizetés kiválasztásával a replikáció időpontjában.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Replikálhatom a zóna által rögzített Azure-virtuális gépeket egy másik régióba?

Igen, a zóna által rögzített virtuális gépek et egy másik régióba [replikálhatja.](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region)

### <a name="can-i-exclude-disks"></a>Kizárhatom a lemezeket?

Igen, a PowerShell használatával kizárhatja a lemezeket a védelem idején. További információt a [lemezek replikációból való kizárásáról](azure-to-azure-exclude-disks.md)talál.

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Hozzáadhatok új lemezeket a replikált virtuális gépekhez, és engedélyezhetem a replikációt?

Igen, új lemezek hozzáadása a replikált virtuális gépekhez, és a replikáció engedélyezése számukra támogatott azure virtuális gépek felügyelt lemezekkel. Amikor új lemezt ad hozzá egy azure-beli virtuális géphez, amely engedélyezve van a replikációhoz, a virtuális gép replikációs állapota figyelmeztetést jelenít meg. Ez a figyelmeztetés kimondja, hogy a virtuális gép egy vagy több lemeze védelemre rendelkezésre áll. Engedélyezheti a replikációt a hozzáadott lemezek számára.

- Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés eltűnik a kezdeti replikáció után.
- Ha nem engedélyezi a lemez replikációját, elvetheti a figyelmeztetést.
- Ha feladatátvételi feladatátvételt egy virtuális gép, amely egy hozzáadott lemez és a replikáció engedélyezve van, vannak replikációs pontok. A replikációs pontok a helyreállításhoz rendelkezésre álló lemezeket jelenítik meg.

Tegyük fel például, hogy egy virtuális gép egyetlen lemezzel rendelkezik, és hozzáad egy újat. Lehet, hogy a lemez hozzáadása előtt létrehozott replikációs pont is található. Ez a replikációs pont azt mutatja, hogy "2 lemezből 1"-ből áll.

A Site Recovery nem támogatja a lemez "gyors eltávolítását" a replikált virtuális gépről. Ha eltávolít egy virtuális gép lemezét, le kell tiltania, majd újra engedélyeznie kell a replikációt a virtuális gép számára.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatok az Azure-ba?

A replikáció folyamatos, amikor az Azure-beli virtuális gépeket replikálja egy másik Azure-régióba. További információt az [Azure-to-Azure replikációs architektúra című témakörben talál.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process)

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Replikálható virtuális gépek egy régión belül? Szükségem van erre a funkcióra a virtuális gépek áttelepítéséhez.

Az Azure-to-Azure lemez-helyreállítási megoldás nem használható virtuális gépek replikálásához egy régión belül.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Replikálhatom a virtuálisgép-példányokat bármely Azure-régióba?

A Site Recovery használatával replikálhatja és helyreállíthatja a virtuális gépeket az ugyanazon földrajzi fürtön belüli két régió között. A földrajzi fürtök adatkésést és szuverenitást szem előtt tartva vannak definiálva. További információt a Webhely-helyreállítási [régió támogatási mátrixában](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)talál.

### <a name="does-site-recovery-require-internet-connectivity"></a>A site recovery igényel internetkapcsolatot?

Nem, a Site Recovery nem igényel internetkapcsolatot. De ez nem igényel hozzáférést a site recovery URL-címek és IP-tartományok, ahogy azt az [Azure VM vész-helyreállítási hálózatba.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Replikálhatok egy olyan alkalmazást, amely külön erőforráscsoporttal rendelkezik a különböző szintekhez?

Igen, replikálhatja az alkalmazást, és a vész-helyreállítási konfiguráció egy külön erőforráscsoportban is tarthatja.

Ha például az alkalmazás minden réteg alkalmazásával, adatbázisával és webjével egy külön erőforráscsoportban található, akkor háromszor kell kijelölnie a [replikációs varázslót](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) az összes réteg védelme érdekében. A Site Recovery ezt a három réteget három különböző erőforráscsoportba replikálja.

## <a name="replication-policy"></a>Replikációs szabályzat

### <a name="what-is-a-replication-policy"></a>Mi az a replikációs házirend?

A replikációs házirend határozza meg a helyreállítási pontok megőrzési előzményeinek beállításait. A szabályzat az alkalmazáskonzisztens pillanatképek gyakoriságát is meghatározza. Alapértelmezés szerint az Azure Site Recovery új replikációs házirendet hoz létre a következő alapértelmezett beállításokkal:

- 24 óra a helyreállítási pontok megőrzési előzményeinek megőrzéséhez.
- 60 perc az alkalmazáskonzisztens pillanatképek gyakorisága esetén.

[További információ a replikációs beállításokról](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?

Az összeomlás-konzisztens helyreállítási pont rendelkezik a lemezen lévő adatokkal, mintha a pillanatkép során lehúzta volna a tápkábelt a kiszolgálóról. Az összeomlás-konzisztens helyreállítási pont nem tartalmaz semmit, ami a memóriában volt, amikor a pillanatkép készült.

Ma a legtöbb alkalmazás jól helyretudja állítani az összeomlás-konzisztens pillanatképeket. Az összeomlás-konzisztens helyreállítási pont általában elegendő az adatbázis nélküli operációs rendszerek és alkalmazások, például fájlkiszolgálók, DHCP-kiszolgálók és nyomtatókiszolgálók számára.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Mi a gyakorisága a törés-konzisztens helyreállítási pont generáció?

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot.

### <a name="what-is-an-application-consistent-recovery-point"></a>Mi az alkalmazáskonzisztens helyreállítási pont?

Alkalmazáskonzisztens helyreállítási pontok jönnek létre az alkalmazás-konzisztens pillanatképek. Az alkalmazáskonzisztens helyreállítási pontok ugyanazokat az adatokat rögzítik, mint az összeomlás-konzisztens pillanatképek, miközben adatokat rögzítenek a memóriában és a folyamatban lévő összes tranzakcióban.

Extra tartalmuk miatt az alkalmazáskonzisztens pillanatképek a leginkább érintettek, és a leghosszabb ideig tartanak. Alkalmazáskonzisztens helyreállítási pontokat javasoljuk az adatbázis-operációs rendszerekhez és alkalmazásokhoz, például az SQL Serverhez.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Milyen hatással van az alkalmazáskonzisztens helyreállítási pontok az alkalmazás teljesítményére?

Az alkalmazáskonzisztens helyreállítási pontok rögzítik a memóriában és a folyamatban lévő összes adatot. Mivel a helyreállítási pontok rögzítik ezeket az adatokat, a Windows kötetárnyékmásolat-szolgáltatásához hasonló keretrendszerre van szükség az alkalmazás kiegyenlítéséhez. Ha a rögzítési folyamat gyakori, hatással lehet a teljesítményre, ha a munkaterhelés már foglalt. Nem javasoljuk, hogy alacsony gyakorisággal használja az alkalmazáskonzisztens helyreállítási pontok nem adatbázis-számítási feladatokhoz. Még az adatbázis-munkaterhelés, 1 óra is elég.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mi az alkalmazáskonzisztens helyreállítási pont generálásának minimális gyakorisága?

A Site Recovery legalább 1 órás gyakorisággal létrehozhat egy alkalmazáskonzisztens helyreállítási pontot.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan történik a helyreállítási pontok létrehozása és mentése?

Ha tudni szeretné, hogy a Site Recovery hogyan hozza létre a helyreállítási pontokat, mutasson egy példát a replikációs házirendre. Ez a replikációs házirend egy helyreállítási ponttal rendelkezik egy 24 órás megőrzési ablakkal és egy 1 órás alkalmazáskonzisztens gyakorisági pillanatképpel.

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ezt a frekvenciát nem lehet megváltoztatni. Az elmúlt órában 12 összeomlás-konzisztens pont és 1 alkalmazáskonzisztens pont közül választhat. Az idő előrehaladtával a Site Recovery az elmúlt egy órában elasztad az összes helyreállítási pontot, és óránként csak 1 helyreállítási pontot ment.

A következő képernyőkép bemutatja a példát. A képernyőképen:

- Az elmúlt egy órában 5 perces gyakorisággal vannak helyreállítási pontok.
- Az elmúlt egy órában túl a Site Recovery csak 1 helyreállítási pontot tart.

   ![A létrehozott helyreállítási pontok listája](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Milyen messzire tudok visszaállni?

A legrégebbi helyreállítási pont, amely et használhatja a 72 óra.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24 órás replikációs házirendem van. Mi történik, ha egy probléma megakadályozza, hogy a Site Recovery több mint 24 órán keresztül helyreállítási pontokat generáljon? Elvesznek-e a korábbi helyreállítási pontjaim?

Nem, a Site Recovery megtartja az összes korábbi helyreállítási pontot. A helyreállítási pontok megőrzési ablakától függően a Site Recovery csak akkor helyettesíti a legrégebbi pontot, ha új pontokat hoz létre. A probléma miatt a Site Recovery nem tud új helyreállítási pontokat létrehozni. Amíg nincsenek új helyreállítási pontok, a régi pontok maradnak, miután elérte az ablak megtartása.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Miután a virtuális gépen engedélyezve van a replikáció, hogyan módosíthatom a replikációs házirendet?

Nyissa meg a **Site Recovery Vault** > **helyhelyreállítási infrastruktúra** > **replikációs infrastruktúrájának replikációs házirendjeit.** Jelölje ki a módosítani kívánt házirendet, és mentse a módosításokat. Minden módosítás az összes meglévő replikációra vonatkozik.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Az összes helyreállítási pont a virtuális gép teljes másolata vagy egy különbözet?

Az első létrehozott helyreállítási pont rendelkezik a teljes példányt. Az egymást követő helyreállítási pontok delta változásokkal rendelkeznek.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>A helyreállítási pontok megőrzési idejének növelése növeli a tárolási költséget?

Igen, ha a megőrzési időszakot 24 óráról 72 órára növeli, a Site Recovery további 48 órára menti a helyreállítási pontokat. A hozzáadott idő tárolási költségeket von maga után. Egy helyreállítási pont például 10 GB-os különbözeti módosításokkal rendelkezhet, gb-onkénti költsége $0.16 havonta. További díjak lenne 1,60 $ × 48 havonta.

## <a name="multi-vm-consistency"></a>Több vm-konzisztencia

### <a name="what-is-multi-vm-consistency"></a>Mi a több virtuális gép konzisztenciája?

A több virtuális gép konzisztenciája biztosítja, hogy a helyreállítási pont konzisztens legyen az összes replikált virtuális gépen.

A Site Recovery **többvirtuális gép konzisztenciabeállítását** biztosítja, amely az összes gép replikációs csoportját hozza létre.

Amikor feladatátvételt a virtuális gépek, akkor lesz megosztott összeomlás-konzisztens és app-konzisztens helyreállítási pontokat.

Menjen végig az oktatóanyagon a [több virtuális gép konzisztenciájának engedélyezéséhez.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm)

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Átvehetek egy virtuális gépet egy többvirtuális gép konzisztenciareplikációs csoporton belül?

Ha a **Többvirtuális gép konzisztencia** beállítás, azt állítja, hogy az alkalmazás függősége van a csoporton belüli összes virtuális gépek. Egyetlen virtuális gép feladatátvétel nem engedélyezett.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hány virtuális gépet replikálhatok egy többvirtuális gép konzisztencia-replikációs csoportjának részeként?

16 virtuális gépet replikálhat együtt egy replikációs csoportban.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Mikor engedélyezzem a több virtuális gép konzisztenciáját?

Mivel a több virtuális gép konzisztenciája cpu-igényes, az engedélyezés hatással lehet a számítási feladatok teljesítményére. Csak akkor használjon több virtuális gép konzisztenciát, ha a gépek ugyanazt a számítási feladatot futtatják, és több gép közötti konzisztenciára van szükség. Ha például egy alkalmazásban két SQL Server-példány és két webkiszolgáló található, akkor csak az SQL Server-példányok esetében kell több virtuális gép konzisztenciát biztosítani.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Hozzá tud adni egy már replikáló virtuális gép egy replikációs csoporthoz?

Virtuális gép hozzáadása egy új replikációs csoporthoz a replikáció engedélyezése közben. Virtuális gép hozzáadása egy meglévő replikációs csoporthoz a replikáció engedélyezése közben. Azonban nem adhat hozzá már replikáló virtuális gép egy új replikációs csoport vagy meglévő replikációs csoport.

## <a name="failover"></a>Feladatátvétel

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hogyan biztosítja a kapacitást az Azure-beli virtuális gépek célrégiójában?

A Site Recovery csapata és az Azure kapacitáskezelési csapata megfelelő infrastruktúra-kapacitást tervez. Feladatátvétel indításakor a csapatok is segítenek biztosítani, hogy a Site Recovery által védett virtuálisgép-példányok a célrégióban települnek.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással elindíthatja a portálon, vagy használhatja a [PowerShellt](azure-to-azure-powershell.md) feladatátvétel indításához.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Megtarthatok nyilvános IP-címet a feladatátvétel után?

Feladatátvétel után nem tarthatja meg az éles alkalmazás nyilvános IP-címét.

Amikor a feladatátvételi folyamat részeként egy számítási feladatot hoz létre, hozzá kell rendelnie egy Azure nyilvános IP-erőforrást a számítási feladathoz. Az Azure nyilvános IP-erőforrás elérhetőnek kell lennie a célrégióban. Az Azure nyilvános IP-erőforrás manuálisan is hozzárendelhető, vagy automatizálhatja azt egy helyreállítási tervvel. További információ a [nyilvános IP-címek beállításáról a feladatátvétel után.](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan)

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Megtarthatok egy privát IP-címet feladatátvétel közben?

Igen, megtarthatja a privát IP-címet. Alapértelmezés szerint, ha engedélyezi a vészhelyreállítást az Azure virtuális gépek, site recovery létrehoz célerőforrások at forráserőforrás-beállítások alapján. A statikus IP-címekkel konfigurált Azure virtuális gépek esetében a Site Recovery megpróbálja ugyanazt az IP-címet létesíteni a célvirtuális géphez, ha nincs használatban.
További információ az [IP-címek feladatátvétel során való megtartásáról.](site-recovery-retain-ip-azure-vm-failover.md)

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Feladatátvétel után miért van a kiszolgálóhoz új IP-cím rendelve?

A Site Recovery megpróbálja megadni az IP-címet a feladatátvétel időpontjában. Ha egy másik virtuális gép veszi ezt a címet, site recovery beállítja a következő rendelkezésre álló IP-címet, mint a cél.

További információ a [virtuális hálózatok hálózati leképezésének és IP-címzésének beállításáról.](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Mik azok **a legújabb (legalacsonyabb RPO)** helyreállítási pontok?

A **Legújabb (legalacsonyabb RPO)** beállítás először feldolgozza a Site Recovery-nek küldött összes adatot. Miután a szolgáltatás feldolgozza az adatokat, létrehoz egy helyreállítási pontot minden virtuális géphez, mielőtt átadnák a virtuális gépnek. Ez a beállítás biztosítja a legalacsonyabb helyreállítási pont célkitűzést (RPO). A feladatátvétel után létrehozott virtuális gép az összes adatot replikált a site recovery a feladatátvétel aktiválásakor.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>A **legújabb (legalacsonyabb RPO)** helyreállítási pontok hatással vannak a feladatátvételi RTO-ra?

Igen. A Site Recovery feldolgozza az összes függőben lévő adatot a feladás előtt, így ez a beállítás magasabb helyreállítási idő célkitűzéssel (RTO) rendelkezik, mint más beállításokkal.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Mit jelent a **legutóbbi feldolgozott** lehetőség a helyreállítási pontokon?

A **legújabb feldolgozott** beállítás átadja a tervben lévő összes virtuális gépet a site recovery által feldolgozott legújabb helyreállítási pontnak. Egy adott virtuális gép legújabb helyreállítási pontjának megtekintéséhez tekintse meg **a legújabb helyreállítási pontokat** a virtuális gép beállításaiban. Ez a beállítás alacsony RTO-t biztosít, mivel a feldolgozatlan adatok feldolgozásával nem kell időt tölteni.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Mi történik, ha az elsődleges régióm váratlan leállást tapasztal?

A feladatátvételt a kimaradás után is elindíthatja. A Site Recovery-nek nincs szüksége az elsődleges régióból származó kapcsolatra a feladatátvételhez.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Mi az rto egy virtuális gép feladatátvétel?

Site Recovery egy [RTO SLA 2 óra](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Azonban az idő nagy részében a Site Recovery perceken belül átadja a virtuális gépeket. Az RTO-t a feladatátvételi feladatok hoz, amelyek azt mutatják, hogy a virtuális gép fel, kiszámítható. A helyreállítási terv RTO, lásd a következő szakaszban.

## <a name="recovery-plans"></a>Helyreállítási tervek

### <a name="what-is-a-recovery-plan"></a>Mi az a helyreállítási terv?

A site recovery helyreállítási helyreállítási helyreállítási terv vezényli a virtuális gépek feladatátvételi helyreállítása. Segít abban, hogy a helyreállítás következetesen pontos, megismételhető és automatizált legyen. A helyreállítási terv a következő igényeket elégíti ki:

- A feladatátvételt felhozó virtuális gépek csoportjának meghatározása
- A virtuális gépek közötti függőségek meghatározása, hogy az alkalmazás pontosan jelenik meg
- A helyreállítás automatizálása egyéni manuális műveletekkel a virtuális gépek feladatátvételén kívül más feladatok elérése érdekében

További információ [a helyreállítási tervek létrehozásáról.](site-recovery-create-recovery-plans.md)

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hogyan érhető el a szekvenálás a helyreállítási tervben?

A helyreállítási tervben több csoportot is létrehozhat a szekvenálás eléréséhez. Minden csoport egyszerre dönt. Az ugyanannak a csoportnak a részét vevő virtuális gépek együttesen adják át a feladatátvételt, amelyet egy másik csoport követ. Az alkalmazások helyreállítási terv használatával történő modellezéséről a [Helyreállítási tervek – ismertet](recovery-plan-overview.md#model-apps)( Ismerd meg a lehetőséget) témakört ismerteti.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hogyan találhatom meg a helyreállítási terv RTO-ját?

A helyreállítási terv RTO-jának ellenőrzéséhez végezze el a helyreállítási terv feladatátvételi tesztjét, és lépjen a **Site Recovery feladatokhoz.**
A következő példában tekintse meg az **SAPTestRecoveryPlan**feladatot. A feladat 8 perc és 59 másodperc alatt felelt meg az összes virtuális gép, és a megadott műveleteket.

![Hely-helyreállítási feladatok listája](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Hozzáadhatok automatizálási runbookokat a helyreállítási tervhez?

Igen, integrálhatja az Azure Automation runbookokat a helyreállítási tervbe. További információ az [Azure Automation runbookok hozzáadásáról.](site-recovery-runbook-automation.md)

## <a name="reprotection-and-failback"></a>Újravédelem és feladat-visszavétel

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Átmentem az elsődleges régióból egy vész-helyreállítási régióba. A vész-és biztonságú virtuális gépek egy VÉSZ-régióban automatikusan védettek?

Nem. Ha [feladatátvételi az](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) egyik régióból a másikba, a virtuális gépek indítása a VÉSZ-régióban egy nem védett állapotban. A virtuális gépek az elsődleges régióban, a másodlagos régióban a virtuális gépek [újbóli kell védenie.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect)

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Az újravédelem időpontjában a Site Recovery replikálja a teljes adatokat a másodlagos régióból az elsődleges régióba?

Ez a helyzettől függ. Ha a forrásrégió virtuális gép létezik, majd csak a forráslemez és a céllemez közötti módosítások lesznek szinkronizálva. A Site Recovery kiszámítja a különbözeteket a lemezek összehasonlításával, majd továbbítja az adatokat. Ez a folyamat általában néhány órát vesz igénybe. Az újravédelem során történt műveletekről az [Újravédett Azure virtuálisgép-példányok újbóli védelme az elsődleges régióban](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection)című témakörben talál további információt.

### <a name="how-much-time-does-it-take-to-fail-back"></a>Mennyi ideig tart a feladat-visszavétel?

Az újravédelem után a feladat-visszavétel körülbelül ugyanannyi időt vesz igénybe, amíg az elsődleges régióból egy másodlagos régióba kerül a feladat-feladat.

## <a name="capacity"></a><a name="capacity"></a>Kapacitás

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hogyan biztosítja a kapacitást az Azure-beli virtuális gépek célrégiójában?

A Site Recovery csapata és az Azure kapacitáskezelési csapata megfelelő infrastruktúra-kapacitást tervez. Feladatátvétel indításakor a csapatok is segítenek biztosítani, hogy a Site Recovery által védett virtuálisgép-példányok a célrégióban települnek.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Működik a Site Recovery fenntartott példányokkal?

Igen, megvásárolhatja a [fenntartott Azure-beli virtuális gépeket](https://azure.microsoft.com/pricing/reserved-vm-instances/) a vész-helyreállítási régióban, és a Site Recovery feladatátvételi műveletek et fogja használni. Nincs szükség további konfigurációra.

## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?

Nem, a Site Recovery nem fogja el a replikált adatokat, és nem rendelkezik semmilyen információt arról, hogy mi fut a virtuális gépeken. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.

Site Recovery az ISO 27001:2013, 27018, HIPAA és DPA tanúsítvánnyal rendelkezik. A szolgáltatás SOC2 és FedRAMP JAB értékelésen megy keresztül.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?

Igen, mind a titkosítás átvitele és [az In-ben az Azure-ban a titkosítás](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatott.

## <a name="next-steps"></a>További lépések

- [Tekintse át az Azure-to-Azure támogatási követelményeit.](azure-to-azure-support-matrix.md)
- [Állítsa be az Azure-to-Azure replikációt.](azure-to-azure-tutorial-enable-replication.md)
- Ha a cikk elolvasása után kérdései vannak, tegye közzé őket az [Azure Recovery Services fórumán.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)
