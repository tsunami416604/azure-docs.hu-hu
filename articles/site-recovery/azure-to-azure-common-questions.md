---
title: Gyakori kérdések – vészhelyreállítás Azure-bA az Azure Site Recoveryvel |} A Microsoft Docs
description: Ez a cikk gyakori kérdésekre foglalja össze, üzembe helyezésekor meg az Azure virtuális gépek vészhelyreállítása aonther az Azure Site Recovery használatával az Azure-régióban
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969947"
---
# <a name="common-questions---azure-to-azure-replication"></a>Gyakori kérdések – Azure-Azure közötti replikáció

Ez a cikk gyakori kérdéseket, láthatjuk, egy másik Azure-régióba az Azure virtuális gépek vészhelyreállítása telepítésekor. Ha kérdése van a cikk elolvasása után, el őket az a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Általános kérdések
### <a name="how-is-site-recovery-priced"></a>Hogyan van a Site Recovery díjszabása?
Felülvizsgálat [Azure Site Recovery díjszabásáról](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) részleteit.

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Hogyan kell konfigurálni a Site Recovery az Azure virtuális gépekhez. Mik azok az ajánlott eljárások?
1. [Azure-bA architektúrájának ismertetése](azure-to-azure-architecture.md)
2. [Tekintse át a támogatott és nem támogatott konfigurációk](azure-to-azure-support-matrix.md)
3. [Vészhelyreállítás beállítása az Azure virtuális gépek](azure-to-azure-how-to-enable-replication.md)
4. [Feladatátvételi teszt futtatása](azure-to-azure-tutorial-dr-drill.md)
5. [Feladatátvétel és feladat-visszavétel az elsődleges régió](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replikáció

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Replikálhatok Azure lemez engedélyezhető a titkosítás virtuális gépek?
Igen, replikálhatja azokat. Tekintse meg [cikk](azure-to-azure-how-to-enable-replication-ade-vms.md) ahhoz, hogy engedélyezve van az Azure disk encryption (ADE) replikációs virtuális gépeket. Vegye figyelembe, hogy csak Azure virtuális gépeken futó Windows operációs rendszert futtató és engedélyezett a titkosítás az Azure AD-alkalmazás az Azure Site Recovery jelenleg támogatottak.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Replikálhatok virtuális gépeket egy másik előfizetéshez?
Igen, az Azure virtuális gépeket replikálhatja egy másik előfizetéshez, az azonos Azure Active Directory-bérlőben.
Vészhelyreállítás konfigurálása [előfizetésekben](https://azure.microsoft.com/blog/cross-subscription-dr) egyszerű. Kiválaszthat egy másik előfizetést, a replikáció során.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Replikálhatok a zónához rögzített Azure virtuális gépek másik régióba.
Igen, akkor is [a zónához rögzített virtuális gépek replikálása](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) egy másik régióba.

### <a name="can-i-exclude-disks"></a>Kizárhatok egyes lemezek?

Igen, olyan lemezeket zárhat power shell védelem idején. Tekintse meg [powershell-útmutató](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) lemez kizárása

###<a name="how-often-can-i-replicate-to-azure"></a>Milyen gyakran replikálhatja az Azure-bA?
Replikációs akkor folyamatos, ha az Azure virtuális gépek replikálása másik Azure-régióba. Ellenőrizze a [Azure-bA](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) replikáció architektúrája részleteinek megismeréséhez.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>Egy azonos régión belüli virtuális gépeket lehet replikálni? Ezt a virtuális gépek áttelepítése van szükség?
Egy azonos régión belüli virtuális gépek replikálása Azure-bA Vészhelyreállítási megoldás nem használható.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Replikálhatok virtuális gépeket minden Azure-régióban?
A Site Recovery replikálja, és a virtuális gépek helyreállítása ugyanazon a földrajzi fürtön belül bármely két régió között. Földrajzi fürtök és az adatok szem vannak meghatározva. További információkért lásd: a Site Recovery [régió támogatási mátrix](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>A Site Recovery igényel internetkapcsolatot?

Nem, a Site Recovery nem igényel internet kapcsolat, de a Site Recovery URL-címek és IP-címtartományok a hozzáférést említetteknek megfelelően ezzel [cikk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="replication-policy"></a>Replikációs házirend

### <a name="what-is-a-replication-policy"></a>Mi a replikációs szabályzat?
Azt határozza meg a helyreállítási pont megőrzési előzményekkel és alkalmazáskonzisztens pillanatkép készítésének gyakorisága beállításait. Alapértelmezés szerint az Azure Site Recovery egy új replikációs házirendet hoz beállítás alapértelmezett 24 órányi a helyreállítási pont megőrzése és az "60 percben az alkalmazáskonzisztens pillanatkép gyakorisága.

### <a name="what-is-crash-consistent-recovery-point"></a>Mi az összeomlás-konzisztens helyreállítási pont?
Összeomlás-konzisztens helyreállítási pont a lemezen lévő adatok jelöli, mintha a virtuális gép leállt, vagy a tápkábel felhasználásával lett létrehozva a kiszolgálóról a pillanatkép időpontjában. Nem tartalmazza, amelyeket volt, a memória, ha a pillanatkép. Napjainkban a legtöbb alkalmazás helyreállíthatja jól összeomlás-konzisztens pillanatképekkel. Összeomlás-konzisztens helyreállítási pont az általában elég a nincs adatbázis operációs rendszerek és alkalmazások, mint a fájlkiszolgálók, a DHCP-kiszolgálók, a nyomtatókiszolgálók és így tovább.

### <a name="what-is-application-consistent-recovery-point"></a>Mi az alkalmazáskonzisztens helyreállítási pont? 
Az alkalmazáskonzisztens pillanatképek összeomlás-konzisztens pillanatképekkel, a memóriában lévő összes adatot, és minden folyamatban lévő tranzakciót is ugyanazokat az adatokat rögzítő alkalmazáskonzisztens helyreállítási pontok jönnek létre. Miatt további tartalmak a alkalmazáskonzisztens pillanatképek a legtöbb metódusa vesz, és tegye meg a legrégebb végrehajtásához. Alkalmazáskonzisztens helyreállítási pontok használata akkor javasolt, adatbázis-operációs rendszerek és alkalmazások, például SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Hogyan helyreállítási pontok létrehozott és mentett?
Tudni, hogyan hoz létre a Site Recovery helyreállítási pontok lehetővé teszi, hogy igénybe vehet a replikációs házirendet, amely rendelkezik a helyreállítási pontok adatmegőrzési időtartam 24 óra és alkalmazás-konzisztens gyakoriság pillanatkép 1 órás egy példát.
Site Recovery létrehoz minden 5 perc és a felhasználó nem rendelkezik a gyakoriság módosítása vezérlőkaraktereket összeomlási alkalmazáskonzisztens pont. Ezért az elmúlt egy óra felhasználó rendelkezik 12 összeomlás-konzisztens pontok és 1 alkalmazás konzisztens pont közül választhat. Az idő előrehaladtával a helyreállítási pontok túl az elmúlt 1 óra és csak egy helyreállítási pont óránként, a Site Recovery szilva.
Az ábrán látható, az alábbi képernyőfelvételen látható:


1. Idő kevesebb, mint az elmúlt 1 óra a helyreállítási pontok, 5 perces gyakorisággal vannak.
2. Alkalommal túl az elmúlt 1 óra láthatjuk, hogy óránként csak egy helyreállítási pont tárolódik.

  ![helyreállítási pontok létrehozása](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Milyen biztonsági állíthatja helyre?
A legrégebbi helyreállítási pont használható érték 72 óra.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>Mi történik, ha egy 24 órás replikációs szabályzat van, és nincs helyreállítási pontok létrehozásának miatt probléma 24 óránál hosszabb. A korábbi helyreállítási pontok törlődnek nem?
Nem, a Site Recovery folyamatosan az összes korábbi helyreállítási pont ebben az esetben. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Miután egy virtuális gépen engedélyezve van a replikáció, hogyan módosíthatom a replikációs szabályzat? 
Lépjen a Site Recovery-tároló > Site Recovery-infrastruktúra > replikációs házirendek. Válassza ki a házirendet, amelyet szerkeszteni és menteni a módosításokat. Minden olyan változás túl a meglévő replikálását alkalmazza. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>A helyreállítási pontok teljes másolat, a virtuális gép vagy a különbségi vannak?
Esetén a kezdeti replikáció a első helyreállítási pont, amely lekérdezi a létrehozott lesz a teljes másolat, és egymást követő helyreállítási pontot kell a változásokat.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>Nem, növelje a helyreállítási pontok megőrzési ideje növeli a tárolási költségek?
Igen, ha növeli a megőrzési időtartam 24 óra, 72 óra és a Site Recovery menti a helyreállítási pontok is 48 óra, amely merülnek fel, a tárterületért. Például ha egyetlen helyreállítási pont rendelkezik az új módosításokkal, 10 GB-os és a gigabájtonkénti van 0,16 $ / hó, majd $ 1.6-os * 48 további díjak havi lenne.

## <a name="failover"></a>Feladatátvétel

### <a name="is-failover-automatic"></a>Automatikus a feladatátvétel?

A feladatátvétel nem automatikus. A portálon egyetlen kattintással feladatátvételt, vagy használhatja a Site Recovery [PowerShell](azure-to-azure-powershell.md) feladatátvétel indításához. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>Nyilvános IP-cím megőrizhetem a feladatátvételt követően?

Az éles alkalmazás nyilvános IP-cím **nem őrzi meg a feladatátvételi**. A számítási feladatok kerülnek sorra, a feladatátvétel során hozzá kell rendelni a célrégióban elérhető az Azure nyilvános IP-erőforrás. Ebben a lépésben történhet manuálisan vagy a helyreállítási tervek automatikusan végbemegy. Tekintse meg [cikk](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan) használata a helyreállítási terv nyilvános IP-címet szeretne hozzárendelni.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>Magánhálózati IP-cím megőrizhetem feladatátvétel során?
Igen, őrizheti meg magánhálózati IP-címet. Alapértelmezés szerint a vész-helyreállítási engedélyezése az Azure-beli virtuális gépek esetén a Site Recovery létrehoz célerőforrások adatforrás-erőforrás beállítások alapján. Az Azure virtuális gépek statikus IP-címmel konfigurálva a Site Recovery megpróbálja kiépítése a cél virtuális Gépen, azonos IP-címét, ha nem használja. Tekintse meg [cikk](site-recovery-retain-ip-azure-vm-failover.md) különböző körülmények között a magánhálózati IP-cím megőrzése.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>A feladatátvételt követően a kiszolgáló nem rendelkezik azonos IP-címét a forrásoldali virtuális Géppel. Miért van, rendelt új IP-cím?

A Site Recovery megpróbálja adja meg az IP-cím a feladatátvétel időpontjában elérhető a legjobb lehetőség alapján. Abban az esetben azt kerül néhány más virtuális gép, a következő elérhető IP-címe és a cél van beállítva. Hogyan kezeli a Site Recovery címzés, a teljes leírását [tekintse át az ebben a cikkben.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>Azt jelenti, hogy mire legutóbbi (legkisebb helyreállítási Időkorlát) helyreállítási pontok?
Ez a lehetőség először feldolgozza a helyreállítási pont létrehozása előtt feladatátadás azt a virtuális gépek Site Recovery szolgáltatásba küldött összes adat. Ezt a lehetőséget biztosít a a legkisebb helyreállítási Időkorlát (helyreállítási időkorlát), mert a virtuális gép létrehozása után a feladatátvételi lesz a feladatátvétel elindításakor a Site Recoverybe replikált összes adatot.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>Legutóbbi (legkisebb helyreállítási Időkorlát) helyreállítási pontok van hatással a feladatátvételi RTO?
Igen, a Site Recovery fel fog dolgozni a feladatátadás előtt az összes függőben lévő adatokat, mert ezt a beállítást mások képest magasabb RTO fog rendelkezni.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>Mire legújabb dolgozza fel a lehetőséget a helyreállítási pontok mean?
Ez a beállítás a Site Recovery által feldolgozott legutóbbi helyreállítási pontnak a csomag összes virtuális gép feladatait. Tekintse meg a legutóbbi helyreállítási pont egy adott virtuális géphez, ellenőrizze a virtuális gép beállításait a legutóbbi helyreállítási pontok. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Ha a két Azure-régiók között végzek, mi történik, ha az elsődleges régió esetleges váratlan leállásakor?
A feladatátvétel után a szolgáltatáskimaradás elhárítása után is indíthat. A Site Recovery nem követeli meg, hajtsa végre a feladatátvételt az elsődleges régióból.

## <a name="recovery-plan"></a>Helyreállítási terv

### <a name="what-is-a-recovery-plan-"></a>Mi a helyreállítási terv?
A Site Recovery helyreállítási tervek koordinálása a feladatátvétel helyreállítási virtuális gépet. Győződjön meg arról, a helyreállítás következetesen pontos, megismételhető és automatizált segít. A helyreállítási terv megszünteti a felhasználó a következő igényeit:

- Meghatározása egy csoporthoz, a virtuális gépek meg együtt, hogy a feladatátvétel.
- A virtuális gépek közötti függőségek meghatározása, így az alkalmazás betölt pontosan.
- Egyéni manuális műveleteket és a helyreállítás automatizálása, úgy, hogy a feladatátvételt a virtuális gépek eltérő feladatok is elérhető.

[További](site-recovery-create-recovery-plans.md) helyreállítási tervek ismertetése.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Hogyan biztosítja az alkalmazás-előkészítés érhető el a helyreállítási terv?

A helyreállítási terv alkalmazás-előkészítés eléréséhez több csoportot is létrehozhat. Minden csoport feladatátvétele egy időben, ami azt jelenti az ugyanabba a csoportba tartozó virtuális gépek feladatátvételi együtt követ egy másik csoport lesz. Ellenőrizze, hogyan [modell alkalmazást a helyreállítási terv használatával.](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Hol találhatom meg a helyreállítási terv RTO?
Ellenőrizze a helyreállítási terv RTO, a helyreállítási terv feladatátvételi teszt, és nyissa meg a Site Recovery-feladatok.
Például alább látható módon SAP teszt helyreállítási terv feladatátvételi 8 perc 59 másodpercig tartott a virtuális gépeket, és végezze el a megadott műveleteket.

  ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Automation-runbookok hozzáadása helyreállítási terv?
Igen, integrálható az Azure automation-runbookok a helyreállítási tervbe. [További információ](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Védelem-újrabeállítást és a feladat-visszavétel 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>Miután a feladatátvétel végrehajtása az elsődleges régióból a vészhelyreállítási régióban does a Vészhelyreállítási régióban lévő virtuális gépek beolvasása automatikusan védett legyen?
Nem, ha Ön [feladatátvételi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) Azure virtuális gépek egyik régióból a másikba, a virtuális gépek indul el a DR régióban egy nem védett állapotban. Feladat-visszavételt a virtuális gépek az elsődleges régióra, kell [ismételt védelme](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) a virtuális gépek a másodlagos régióban.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>Ismételt védelem idején már nem a Site Recovery teljes adatok replikálása másodlagos régióból elsődleges régió felé?
Ez a helyzet, például ha a forrásrégióban virtuális gép létezik, majd a forrás és a cél lemezzel között csak változásokat szinkronizálja a rendszer függ. A különbözeti számított összehasonlításával mindkét a lemezeket, és továbbítja. Ez általában néhány órát vesz igénybe. Tekintse meg [cikk]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) kapcsolatos információkért, mi történik a ismételt védelem során.

### <a name="how-much-time-does-it-take-to-failback"></a>Mennyi idő mindezt való feladat-visszavétel?
Ismételt védelem befejezése után általában akkor az elsődleges régióból a másodlagos régióba történő feladatátvételt hasonló idő. 

## <a name="security"></a>Biztonság
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>A replikációs adatok el lesznek küldve a Site Recovery szolgáltatáshoz?
Nem, a Site Recovery nem intercept a replikált adatokat, és nem rendelkezik semmilyen információval, hogy a virtuális gépeken futó. A Site Recovery szolgáltatás csak a replikáció és a feladatátvétel levezényléséhez szükséges metaadatokat kapja meg.  
Site Recovery szolgáltatás ISO 27001:2013, 27018, a HIPAA, DPA hitelesített, és SOC2 és FedRAMP JAB folyamatban van.

### <a name="does-site-recovery-encrypt-replication"></a>A Site Recovery titkosítja a replikációt?
Igen, mindkét – az átvitel közbeni titkosítás és [titkosítás az Azure-ban](https://docs.microsoft.com/azure/storage/storage-service-encryption) támogatottak.

## <a name="next-steps"></a>További lépések
* [Felülvizsgálat](azure-to-azure-support-matrix.md) követelmények támogatására.
* [Állítsa be a](azure-to-azure-tutorial-enable-replication.md) replikáció Azure-bA.
