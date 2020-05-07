---
title: Gyakori kérdések az Azure-beli virtuális gépek vész-helyreállításáról Azure Site Recovery
description: Ez a cikk az Azure-beli virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdésekre ad választ Azure Site Recovery használatakor.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: b6f665c5b0f2fbd291d20ef21d0a447d20f7c2da
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82738048"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Gyakori kérdések: Azure – Azure vész-helyreállítás

Ez a cikk az Azure-beli virtuális gépek vész-helyreállításával kapcsolatos gyakori kérdéseket válaszolja meg egy másik Azure-régióba [Azure site Recovery](site-recovery-overview.md)használatakor.

## <a name="general"></a>Általános kérdések

### <a name="how-is-site-recovery-priced"></a>Hogyan Site Recovery díjszabása?

Tekintse át [Azure site Recovery a virtuális gépek díjszabását](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Hogyan működik a Azure Site Recovery ingyenes szintje?

Minden Azure Site Recovery védelemmel ellátott példány díjmentes a védelem első 31 napján. Ezen időszak után az egyes példányok védelme az [Azure Virtual Machines Azure site Recovery díjszabásának](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/)díjszabása.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Az első 31 napban bármilyen más Azure-díjat számolunk fel?

Igen. Bár a védett példányok első 31 napján Azure Site Recovery ingyenes, az Azure Storage, a tárolási tranzakciók és az adatforgalom díját is felmerülhet. A helyreállított virtuális gépek is felmerülhetnek az Azure számítási díjaival. A díjszabással kapcsolatos részletes információkat a [Azure site Recovery díjszabásában](https://azure.microsoft.com/pricing/details/site-recovery)talál.

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Mik az Azure Virtual Machines vész-helyreállítás ajánlott eljárásai?

1. [Az Azure – Azure architektúra megismerése](azure-to-azure-architecture.md)
1. [Tekintse át a támogatott és nem támogatott konfigurációkat](azure-to-azure-support-matrix.md)
1. [Vész-helyreállítás beállítása Azure-beli virtuális gépekhez](azure-to-azure-how-to-enable-replication.md)
1. [Feladatátvételi teszt futtatása](azure-to-azure-tutorial-dr-drill.md)
1. [Feladatátvétel és feladat-visszavétel az elsődleges régióba](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Hogyan biztosítható a kapacitás a megcélzott régióban?

A Site Recovery csapat és az Azure Capacity Management csapata elegendő infrastrukturális kapacitást tervez. A feladatátvétel elindításakor a csapatok is segítenek biztosítani, hogy Site Recovery által védett virtuálisgép-példányok a célként megadott régióban legyenek telepítve.

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Replikálhatók az Azure Disk Encryption szolgáltatáson keresztül engedélyezett virtuális gépek?

Igen. Site Recovery támogatja a Azure Disk Encryption engedélyezett virtuális gépek vész-helyreállítását. Amikor engedélyezi a replikációt, az Azure átmásolja az összes szükséges lemez-titkosítási kulcsot és titkot a forrás régiójából a felhasználói környezetben lévő célként megadott régióba. Ha nem rendelkezik a megfelelő engedélyekkel, a biztonsági rendszergazda parancsfájlt használhat a kulcsok és a titkos kódok másolásához.

- Site Recovery támogatja a Windows rendszert futtató Azure-beli virtuális gépek Azure Disk Encryptionét.
- A Site Recovery támogatja az 0,1-es Azure Disk Encryption-verziót, amelyhez Azure Active Directory (Azure AD) szükséges sémát kell megadni. A Site Recovery a 1,1-es verziót is támogatja, amely nem igényel Azure AD-t. [További információ az Azure Disk Encryption bővítményi sémájáról](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - A Azure Disk Encryption 1,1-es verziójában a felügyelt lemezekkel rendelkező Windows rendszerű virtuális gépeket kell használnia.
  - [További](azure-to-azure-how-to-enable-replication-ade-vms.md) információ a titkosított virtuális gépek replikálásának engedélyezéséről.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Lehetséges a virtuális gépek másik előfizetésbe történő replikálása?

Igen, az Azure-beli virtuális gépeket egy másik előfizetésre is replikálhatja ugyanazon az Azure AD-bérlőn belül.

Állítsa be a vész-helyreállítást az [előfizetések között](https://azure.microsoft.com/blog/cross-subscription-dr) egy másik előfizetés kiválasztásával a replikálás időpontjában.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Replikálható a zóna-rögzített Azure-beli virtuális gépek egy másik régióba?

Igen, a [zóna által rögzített virtuális gépeket replikálhatja](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) egy másik régióba.

### <a name="can-i-exclude-disks"></a>Ki lehet zárni a lemezeket?

Igen, a PowerShell használatával kizárhatja a lemezeket a védelem időpontjában. További információ: [Lemezek kizárása a replikációból](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Hozzáadhatok új lemezeket a replikált virtuális gépekhez, és engedélyezheti számukra a replikálást?

Igen, az új lemezek a replikált virtuális gépekhez való hozzáadása és a replikálás engedélyezése a felügyelt lemezekkel rendelkező Azure-beli virtuális gépek esetében támogatott. Amikor új lemezt ad hozzá egy replikációra engedélyezett Azure-beli virtuális géphez, a virtuális gép replikációs állapota figyelmeztetést jelenít meg. Ez a figyelmeztetés azt jelzi, hogy a virtuális gép egy vagy több lemeze védelmet biztosít. Engedélyezheti a további lemezek replikálását.

- Ha engedélyezi a hozzáadott lemezek védelmét, a figyelmeztetés a kezdeti replikálás után eltűnik.
- Ha nem engedélyezi a replikációt a lemezhez, elhagyhatja a figyelmeztetést.
- Ha olyan virtuális gépet hajt végre, amelyen engedélyezve van a hozzáadott lemez és a replikáció, vannak replikációs pontok. A replikációs pontok megjelennek a helyreállításhoz elérhető lemezek.

Tegyük fel például, hogy egy virtuális gép egyetlen lemezzel rendelkezik, és egy újat ad hozzá. Lehet, hogy a lemez hozzáadása előtt létrejött egy replikációs pont. Ez a replikációs pont azt mutatja, hogy az "1/2 lemezből áll."

A Site Recovery nem támogatja a lemez "gyors eltávolítását" egy replikált virtuális gépről. Ha eltávolít egy VM-lemezt, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.

### <a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran lehet replikálni az Azure-ba?

Az Azure-beli virtuális gépek másik Azure-régióba történő replikálásakor a replikáció folyamatos. További információkért lásd az [Azure – Azure replikálási architektúrát](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Replikálható a virtuális gépek egy adott régión belül? A virtuális gépek áttelepítéséhez szükség van erre a funkcióra.

Nem használhat Azure – Azure lemezes helyreállítási megoldást a virtuális gépek régión belüli replikálására.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Replikálhat virtuálisgép-példányokat bármely Azure-régióba?

Site Recovery használatával replikálhatja és helyreállíthatja a virtuális gépeket az ugyanazon földrajzi fürtön belüli két régió között. A földrajzi fürtök adatkéséssel és szuverenitással vannak meghatározva. További információ: Site Recovery [régió támogatási mátrixa](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Az Site Recovery internetkapcsolat szükséges?

Nem, Site Recovery nem igényel internetkapcsolatot. Azonban szükség van a Site Recovery URL-címek és IP-címtartományok elérésére, ahogy azt a [hálózatkezelés az Azure-beli virtuális gép](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)vész-helyreállítási szolgáltatásában.

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Lehet replikálni egy olyan alkalmazást, amely külön erőforráscsoporthoz tartozik a különálló rétegek számára?

Igen, replikálhatja az alkalmazást, és megtarthatja a vész-helyreállítási konfigurációt egy külön erőforráscsoporthoz is.

Ha például az alkalmazás egy különálló erőforráscsoport alkalmazásával, adatbázisával és webhelyével rendelkezik, akkor a [replikálási varázslót](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) háromszor kell kiválasztania az összes szinten való védelem érdekében. Site Recovery a három szintet három különböző erőforráscsoporthoz fogja replikálni.

## <a name="replication-policy"></a>Replikációs szabályzat

### <a name="what-is-a-replication-policy"></a>Mi a replikációs házirend?

A replikációs házirend határozza meg a helyreállítási pontok megőrzési előzményeinek beállításait. A szabályzat az alkalmazás-konzisztens Pillanatképek gyakoriságát is meghatározza. Alapértelmezés szerint a Azure Site Recovery új replikációs házirendet hoz létre az alapértelmezett beállításokkal:

- 24 óra a helyreállítási pontok megőrzési előzményeihez.
- 60 perc az alkalmazás-konzisztens Pillanatképek gyakoriságához.

[További információ a replikációs beállításokról](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?

Az összeomlás-konzisztens helyreállítási pont a lemezen lévő adatokkal rendelkezik, mintha a pillanatkép során kihúzta a hálózati kábelt a kiszolgálóról. Az összeomlás-konzisztens helyreállítási pont nem tartalmaz olyan semmit, ami memóriában volt a pillanatkép készítésekor.

Napjainkban a legtöbb alkalmazás helyreállíthatja az összeomlás-konzisztens pillanatképeket is. Az összeomlás-konzisztens helyreállítási pontok általában elegendőek az adatbázis nélküli operációs rendszerekhez és alkalmazásokhoz, például a fájlkiszolgálók, a DHCP-kiszolgálók és a nyomtatókiszolgálók számára.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Milyen gyakorisággal történik az összeomlás-konzisztens helyreállítási pontok létrehozása?

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot.

### <a name="what-is-an-application-consistent-recovery-point"></a>Mi az az alkalmazás-konzisztens helyreállítási pont?

Az alkalmazással konzisztens helyreállítási pontok az alkalmazás-konzisztens Pillanatképek alapján jönnek létre. Az alkalmazás-konzisztens helyreállítási pontok ugyanazokat az adatmennyiségeket rögzítik, mint az összeomlás-konzisztens Pillanatképek, ugyanakkor a memóriában lévő és a folyamatban lévő összes tranzakció rögzítése is.

A további tartalom miatt az alkalmazás-konzisztens Pillanatképek a leginkább érintettek, és a leghosszabb időt veszik igénybe. Javasoljuk, hogy az alkalmazás-konzisztens helyreállítási pontokat az adatbázis-operációs rendszerekhez és alkalmazásokhoz, például a SQL Serverhoz.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Milyen hatással van az alkalmazás-konzisztens helyreállítási pontok alkalmazása az alkalmazások teljesítményére?

Az alkalmazás-konzisztens helyreállítási pontok rögzítik a memóriában és a folyamatban lévő összes adatmennyiséget. Mivel a helyreállítási pontok rögzítik ezeket az adatkereteket, a Windows rendszerhez Kötet árnyékmásolata szolgáltatás hasonló keretrendszerre van szükségük, hogy fokozatos leválasztása az alkalmazást. Ha a rögzítési folyamat gyakori, akkor hatással lehet a teljesítményre, ha a munkaterhelés már foglalt. A nem adatbázis-alapú számítási feladatok esetében nem ajánlott alacsony gyakorisággal használni az alkalmazás-konzisztens helyreállítási pontokat. Még az adatbázis-munkaterhelés esetében is, 1 óra elegendő.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Mi a minimális gyakorisága az alkalmazás-konzisztens helyreállítási pontok generálásának?

Site Recovery létrehozhat egy alkalmazás-konzisztens helyreállítási pontot, amelynek minimális gyakorisága 1 óra.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan történik a helyreállítási pontok létrehozása és mentése?

Ha szeretné megtudni, hogyan hozza létre a Site Recovery helyreállítási pontokat, tekintse meg a replikációs házirendet. Ez a replikációs házirend egy 24 órás adatmegőrzési időszaktal rendelkező helyreállítási ponttal és 1 órás alkalmazás-konzisztens gyakorisági pillanatképtel rendelkezik.

A Site Recovery 5 percenként létrehoz egy összeomlás-konzisztens helyreállítási pontot. Ez a gyakoriság nem módosítható. Az elmúlt órában 12 összeomlás-konzisztens pont és 1 alkalmazás-konzisztens pont közül választhat. Az idő előrehaladtával Site Recovery az utolsó órában túli összes helyreállítási pontot, és óránként csak 1 helyreállítási pontot ment.

A következő képernyőkép szemlélteti a példát. A képernyőképen:

- Az elmúlt órában a helyreállítási pontok 5 perces gyakorisággal vannak ellátva.
- Az elmúlt órában az Site Recovery csak 1 helyreállítási pontot tart.

   ![Generált helyreállítási pontok listája](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Meddig lehet visszaállítani?

A legrégebben használható helyreállítási pont 72 óra.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>24 órás replikációs házirendtel rendelkezem. Mi történik, ha egy probléma megakadályozza, hogy Site Recovery a helyreállítási pontokat 24 óránál hosszabb ideig hozza létre? A korábbi helyreállítási pontok elvesznek?

Nem, Site Recovery fogja megőrizni az összes korábbi helyreállítási pontot. A helyreállítási pontok adatmegőrzési időszaka alapján a Site Recovery csak akkor cseréli le a legrégebbi pontot, ha új pontokat hoz létre. A probléma miatt Site Recovery nem tud új helyreállítási pontokat előállítani. Amíg új helyreállítási pontra nem kerül sor, a régi pontok addig maradnak, amíg el nem éri a megőrzöttség ablakát.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Ha a replikáció engedélyezve van egy virtuális gépen, hogyan változtathatom meg a replikációs házirendet?

Nyissa meg **site Recovery** > tároló**site Recovery infrastruktúra** > -**replikációs házirendek**lehetőséget. Válassza ki a szerkeszteni kívánt szabályzatot, és mentse a módosításokat. A módosítások az összes meglévő replikációra érvényesek lesznek.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Az összes helyreállítási pont a virtuális gép teljes másolatát vagy a különbözetet?

Az első létrehozott helyreállítási pont a teljes másolattal rendelkezik. Az egymást követő helyreállítási pontok különbözeti változásokkal rendelkeznek.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Növeli a helyreállítási pontok megőrzési időtartamát?

Igen, ha 24 óra és 72 óra között növeli a megőrzési időtartamot, Site Recovery a helyreállítási pontokat további 48 óráig fogja menteni. A hozzáadott idő tárolási díjat von maga után. Előfordulhat például, hogy egy helyreállítási pont 10 GB-os különbözeti változásokkal rendelkezik, és GB-os $0,16-os díjat tartalmaz. További díjak: $1,60 × 48/hó.

## <a name="multi-vm-consistency"></a>Több virtuális gépre kiterjedő konzisztencia

### <a name="what-is-multi-vm-consistency"></a>Mi a több virtuális gépre kiterjedő konzisztencia?

A több virtuális gépre kiterjedő konzisztencia biztosítja, hogy a helyreállítási pont konzisztens legyen az összes replikált virtuális gép között.

A Site Recovery egy **több virtuális gépre kiterjedő konzisztencia-** beállítást biztosít, amely az összes gép replikációs csoportját hozza létre.

Ha a virtuális gépek feladatátvételét hajtja végre, az összeomlás-konzisztens és az alkalmazás-konzisztens helyreállítási pontokat fogja tartalmazni.

Ugorjon végig az oktatóanyagban a [több virtuális gépre kiterjedő konzisztencia engedélyezéséhez](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>A több virtuális GÉPRE kiterjedő konzisztencia-replikációs csoporton belül egyetlen virtuális gép feladatátvétele végezhető el?

Ha kiválasztja a **több virtuális gépre** kiterjedő konzisztencia lehetőséget, azt jelzi, hogy az alkalmazás egy csoporton belüli virtuális gépektől függ. Az egyetlen virtuális gép feladatátvétele nem engedélyezett.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Hány virtuális gépet lehet replikálni egy több virtuális GÉPRE kiterjedő konzisztencia-replikációs csoport részeként?

A 16 virtuális gépet egyszerre replikálhatja egy replikációs csoportba.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Mikor engedélyezzem a több virtuális gépre kiterjedő konzisztenciát?

Mivel a több virtuális gépre kiterjedő konzisztencia a CPU-igényes, ami hatással lehet a számítási feladatok teljesítményére. A több virtuális gépre kiterjedő konzisztencia csak akkor használható, ha a gépek ugyanazt a számítási feladatot futtatják, és több gépen is konzisztencia szükséges. Ha például két SQL Server példánya van, és két webkiszolgálója van egy alkalmazásban, akkor a több virtuális gépre kiterjedő konzisztencia csak a SQL Server példányok esetében szükséges.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Hozzáadhat egy már replikáló virtuális gépet egy replikációs csoporthoz?
A replikáció engedélyezése során hozzáadhat egy virtuális gépet egy új replikációs csoporthoz. A replikáció engedélyezése során hozzáadhat egy virtuális gépet egy meglévő replikációs csoporthoz is. Nem adhat hozzá azonban már replikáló virtuális gépet egy új replikációs csoporthoz vagy meglévő replikációs csoporthoz.
 
## <a name="failover"></a>Feladatátvétel


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hogyan biztosítható a kapacitás az Azure-beli virtuális gépek számára a célként megadott régióban?

A Site Recovery csapat és az Azure Capacity Management csapata elegendő infrastrukturális kapacitást tervez. A feladatátvétel elindításakor a csapatok is segítenek biztosítani, hogy Site Recovery által védett virtuálisgép-példányok a célként megadott régióban legyenek telepítve.

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A feladatátvételt egyetlen kattintással elindíthatja a portálon, vagy a [PowerShell](azure-to-azure-powershell.md) használatával elindíthatja a feladatátvételt.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Megtarthatok egy nyilvános IP-címet feladatátvétel után?

Feladatátvétel után az üzemi alkalmazás nyilvános IP-címe nem tartható fenn.

Ha a feladatátvételi folyamat részeként munkaterhelést hoz létre, egy Azure nyilvános IP-erőforrást kell hozzárendelni a munkaterheléshez. Az Azure-beli nyilvános IP-erőforrásnak elérhetőnek kell lennie a cél régióban. Az Azure nyilvános IP-erőforrását manuálisan is hozzárendelheti, vagy automatizálhatja egy helyreállítási terv használatával. Megtudhatja, hogyan [állíthatja be a nyilvános IP-címeket a feladatátvétel után](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Megtarthatok egy magánhálózati IP-címet a feladatátvétel során?

Igen, megtarthat egy magánhálózati IP-címet is. Alapértelmezés szerint az Azure-beli virtuális gépek vész-helyreállításának engedélyezésekor Site Recovery a forrás erőforrás-beállítások alapján hozza létre a cél erőforrásokat. Az Azure-Virtual Machines statikus IP-címekkel vannak konfigurálva, Site Recovery a célként megadott virtuális gép esetében ugyanazt az IP-címet próbálja kiépíteni, ha az nincs használatban.
Ismerje meg, hogyan [tarthatja meg az IP-címeket a feladatátvétel során](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>A feladatátvételt követően a kiszolgáló új IP-címet rendelt hozzá?

Site Recovery megpróbálja megadni az IP-címet a feladatátvétel időpontjában. Ha egy másik virtuális gép ezt a címet veszi, Site Recovery beállítja a következő elérhető IP-címet célként.

További információ a [hálózati leképezés és a virtuális hálózatok IP-címzésének beállításáról](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Mik a **Legutóbbi (legalacsonyabb RPO)** helyreállítási pontok?

A **legújabb (legalacsonyabb RPO)** beállítás először feldolgozza a site Recoveryba elküldett összes adatfeldolgozást. Miután a szolgáltatás feldolgozta az adatfeldolgozást, egy helyreállítási pontot hoz létre az egyes virtuális gépek számára, mielőtt feladatátvételt hajt végre a virtuális gépen. Ez a beállítás a legalacsonyabb helyreállítási időkorlátot (RPO) adja meg. A feladatátvételt követően létrehozott virtuális gép minden, a feladatátvétel elindításakor Site Recovery replikált adattal rendelkezik.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>A **Legutóbbi (legalacsonyabb RPO)** helyreállítási pontok hatással vannak a feladatátvételi RTO?

Igen. Site Recovery feldolgozza az összes függőben lévő adatát a feladatátvétel előtt, így ez a beállítás magasabb helyreállítási időcélkitűzéssel (RTO) rendelkezik a többi lehetőséghez képest.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>Mit jelent a helyreállítási pontok **legújabb feldolgozott** lehetősége?

A **legújabb feldolgozott** lehetőség a tervben lévő összes virtuális gép átadása a site Recovery feldolgozott legújabb helyreállítási pontra. Egy adott virtuális gép legutóbbi helyreállítási pontjának megtekintéséhez tekintse meg a virtuális gép beállításainak **legutóbbi helyreállítási pontjait** . Ez a beállítás alacsony RTO biztosít, mivel a feldolgozatlan adatmennyiségek feldolgozása nem történik meg.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Mi történik, ha az elsődleges régió váratlan kimaradást tapasztal?

A leállás utáni feladatátvételt indíthat. Site Recovery nem kell kapcsolódnia az elsődleges régióhoz a feladatátvétel elvégzéséhez.

### <a name="what-is-an-rto-of-a-vm-failover"></a>Mi a virtuális gép feladatátvételének RTO?

Site Recovery [RTO SLA-ja 2 óra](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). Azonban a legtöbb esetben a Site Recovery perceken belül feladatátvételt hajt végre a virtuális gépeken. A RTO kiszámításához lépjen a feladatátvételi feladatok pontra, amely megjeleníti a virtuális gép üzembe helyezésének idejét. A helyreállítási terv RTO tekintse meg a következő szakaszt.

## <a name="recovery-plans"></a>Helyreállítási tervek

### <a name="what-is-a-recovery-plan"></a>Mi az a helyreállítási terv?

A Site Recovery helyreállítási terve összehangolja a virtuális gépek feladatátvételének helyreállítását. Ezzel a módszerrel a helyreállítás konzisztens, ismételhető és automatizált lehet. A helyreállítási terv a következő igényeket javítja:

- A feladatátvétel alatt álló virtuális gépek csoportjának definiálása
- A virtuális gépek közötti függőségek meghatározása, hogy az alkalmazás pontosan felkerüljön
- A helyreállítás automatizálása a virtuális gépek feladatátvételén kívüli feladatok végrehajtásához szükséges egyéni manuális műveletekkel

További információ [a helyreállítási tervek létrehozásáról](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Hogyan valósul meg az előkészítés egy helyreállítási tervben?

Egy helyreállítási tervben több csoportot is létrehozhat az előkészítési sorrend eléréséhez. Egyszerre minden csoport feladatátvételt hajt végre. Azok a virtuális gépek, amelyek ugyanabba a csoportba tartoznak, feladatátvételt végeznek, majd egy másik csoportot követnek. Ha meg szeretné tudni, hogyan modellezhető egy alkalmazás egy helyreállítási terv használatával, olvassa el [a helyreállítási tervek](recovery-plan-overview.md#model-apps)című témakört.

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hogyan találhatom meg a helyreállítási terv RTO?

A helyreállítási terv RTO ellenőrzéséhez végezzen feladatátvételi tesztet a helyreállítási tervhez, és lépjen **site Recovery feladatok**elemre.
A következő példában tekintse meg a **SAPTestRecoveryPlan**feladatot. A feladat 8 perc és 59 másodperc között eltelt az összes virtuális gép feladatátvétele és a megadott műveletek végrehajtása.

![Site Recovery feladatok listája](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Hozzáadhatok Automation-runbookok a helyreállítási tervhez?

Igen, integrálhatja Azure Automation runbookok a helyreállítási tervbe. További információ a [Azure Automation runbookok hozzáadásáról](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Ismételt védelem és feladat-visszavétel

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Átadottam az elsődleges régióból a vész-helyreállítási régióba. A DR régióban lévő virtuális gépek automatikusan védettek?

Nem. Ha az Azure-beli virtuális gépeket az egyik régióból a másikba hajtja végre, a virtuális gépek [nem](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) védett állapotban kezdődnek a Dr régióban. Ahhoz, hogy a virtuális gépeket az elsődleges régióba lehessen visszaadni, újra kell [védetté](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) tenni a virtuális gépeket a másodlagos régióban.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>Az ismételt védelem során a Site Recovery replikálja a teljes adatmennyiséget a másodlagos régióból az elsődleges régióba?

Ez a helyzettől függ. Ha a forrásoldali virtuális gép létezik, akkor a rendszer csak a forrásfájl és a céllemez közötti változásokat szinkronizálja. Site Recovery kiszámítja a különbségeket a lemezek összehasonlításával, majd átviszi azokat. Ez a folyamat általában néhány órát vesz igénybe. További információ arról, hogy mi történik az ismételt védelem során: [Az Azure-beli virtuálisgép-példányok ismételt védelme az elsődleges régióban](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Mennyi ideig tart a feladat-visszavétel?

Az ismételt védelem után a feladat-visszavétel az elsődleges régióból a másodlagos régióba való feladatátvételhez szükséges időt veszi igénybe.

## <a name="capacity"></a><a name="capacity"></a>Kapacitás

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Hogyan biztosítható a kapacitás az Azure-beli virtuális gépek számára a célként megadott régióban?

A Site Recovery csapat és az Azure Capacity Management csapata elegendő infrastrukturális kapacitást tervez. A feladatátvétel elindításakor a csapatok is segítenek biztosítani, hogy Site Recovery által védett virtuálisgép-példányok a célként megadott régióban legyenek telepítve.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Működik Site Recovery fenntartott példányokkal?

Igen, a vész-helyreállítási régióban megvásárolhatja a [fenntartott Azure-beli virtuális gépeket](https://azure.microsoft.com/pricing/reserved-vm-instances/) , és site Recovery feladatátvételi műveletek is felhasználhatják őket. Nincs szükség további konfigurálásra.

## <a name="security"></a>Biztonság

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?

Nem, Site Recovery nem metszi a replikált adatokat, és nem rendelkezik információval arról, hogy mi fut a virtuális gépeken. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.

A Site Recovery ISO 27001:2013, 27018, HIPAA és DPA tanúsítvánnyal rendelkezik. A szolgáltatás SOC2 és FedRAMP a ÜSS értékeléseket.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?

Igen, az Azure-ban az átvitelben és a [titkosításban](https://docs.microsoft.com/azure/storage/storage-service-encryption) egyaránt támogatott a titkosítás.

## <a name="next-steps"></a>További lépések

- [Tekintse át az Azure-ról Azure-ra vonatkozó támogatási követelményeket](azure-to-azure-support-matrix.md).
- [Azure – Azure replikálás beállítása](azure-to-azure-tutorial-enable-replication.md).
- Ha kérdése van a cikk elolvasása után, tegye közzé őket az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
