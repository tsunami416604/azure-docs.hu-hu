---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3e660dbcbfecd57c43ecec6006581364660adb44
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50737235"
---
# <a name="azure-managed-disks-overview"></a>Az Azure Managed Disks – áttekintés

Az Azure Managed Disks leegyszerűsíti az Azure IaaS virtuális gépek Lemezkezelés, mivel kezeli az [tárfiókok](../articles/storage/common/storage-introduction.md) társított Virtuálisgép-lemezek. Csak meg kell adnia a típusa ([Standard HDD](../articles/virtual-machines/windows/standard-storage.md), [Standard SSD](../articles/virtual-machines/windows/disks-standard-ssd.md), vagy [prémium szintű SSD](../articles/virtual-machines/windows/premium-storage.md)) és a lemez kívánt méretét kell, és az Azure létrehozza és felügyeli a lemezt Ön helyett.

## <a name="benefits-of-managed-disks"></a>Felügyelt lemezek előnyei

Vessünk egy pillantást, ha megkapta a felügyelt lemezek használatával kezdve a Channel 9-videók előnyöket nyújtja [jobb Azure VM-rugalmasságot a Managed Disks](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Egyszerű és méretezhető virtuális gépek üzembe helyezése

A színfalak mögött a felügyelt lemezek kezeli a tárterületet az Ön számára. Korábban el kellett tárfiókok, amely tárolja a lemezek (VHD-fájlok) az Azure-beli virtuális gépek létrehozása. Ha vertikális felskálázása kellett győződjön meg arról, hogy további tárfiókokat létrehozni, így azokkal a lemezek nem haladhatja meg a Storage IOPS-korlátját. A Managed Disks storage kezelését, már nem korlátozza a tárfiókok korlátai (például 20 000 IOPS / fiók). Már nem kell több tárfiók másolja az egyéni lemezképeket (VHD-fájlok). – Egy storage-fiók Azure-régiónként – központi helyen kezelheti őket, és ezek segítségével több száz virtuális gép létrehozása az előfizetéshez.

A felügyelt lemezek lehetővé teszi, hogy legfeljebb 50 000 virtuális gép létrehozása **lemezek** egy előfizetésben régiónként típusú, amely lehetővé teszi, hogy hozzon létre több ezer **virtuális gépek** egy előfizetés. Ez a szolgáltatás emellett tovább növeli a méretezhetőségét [Virtual Machine Scale Sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) azáltal, hogy akár egy ezer virtuális gép létrehozása méretezési csoportban lévő virtuális gép egy Piactéri rendszerkép használatával.

### <a name="better-reliability-for-availability-sets"></a>Nagyobb megbízhatóságot, rendelkezésre állási csoportok

Felügyelt lemezek nagyobb megbízhatóságot biztosít a rendelkezésre állási csoportok biztosítva, hogy a lemezek [rendelkezésre állási csoport virtuális gépeinek](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) hibapontok egymástól a kritikus hibapontok elkerülése érdekében. Lemezek automatikusan különböző tárolóskálázási egységekbe (stampek) kerülnek. Ha egy stamp hardver-vagy szoftverhiba miatt meghiúsul, csak azokat a stampek lévő lemezek VM-példányok sikertelen. Például tegyük fel, hogy öt virtuális gépeken futó alkalmazásokhoz, és a virtuális gépek rendelkezésre állási csoportba. A lemezek a virtuális gépeken nem tárolja az azonos blokkba, ezért ha egyetlen stamphez konfiguráljon leáll, az alkalmazás többi példánya tovább futnak.

### <a name="highly-durable-and-available"></a>Tartós és magas rendelkezésre állású

Az Azure Disks 99,999%-os elérhetőséggel büszkélkedhet. Nyugodtan alhat, hogy három replika adatait, amely jelentős tartósságot garantál. Ha egy vagy két replika meghibásodik, a fennmaradó replikák azok feladatátvételével garantálják az adatok megőrzését és a magas fokú hibatűrő képességet. Ezzel a kialakítással az Azure rendszeresen vállalati szintű tartósságot nyújthat az IaaS-lemezeknek, iparágvezető NULLA %-os Éves Hibaszázalékkal. 

### <a name="granular-access-control"></a>Részletes hozzáférés-vezérlés

Használhat [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md) felügyelt lemezes konkrét engedély hozzárendelése egy vagy több felhasználó. Felügyelt lemezek közzéteszi a különböző műveleteket, köztük a olvasási, írási (létrehozási/frissítési), törlési és beolvasása egy [közös hozzáférésű jogosultságkód (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) a lemezhez. Is hozzáférést biztosít egy személy a feladat végrehajtásához szükséges műveleteket. Például ha egy felügyelt lemez másolása egy tárfiókba, aki nem szeretné, is nem kíván hozzáférést biztosítani az exportálási művelet a felügyelt lemez. Hasonlóképpen ha egy személy egy SAS URI-t használja egy felügyelt lemez másolása nem szeretné, lehet váltani, nem engedélyezi, hogy a felügyelt lemez.

### <a name="azure-backup-service-support"></a>Az Azure Backup szolgáltatás támogatása

A Managed Disks Azure Backup szolgáltatás használatával hozzon létre egy biztonsági mentési feladat idő-alapú biztonsági mentések, könnyű VM-helyreállítás és a biztonsági másolatok megőrzési házirendeket. A felügyelt lemezek csak helyileg redundáns tárolás (LRS) támogatja a következő replikálási beállítás. Az adatok három példányban őrzi meg egy adott régión belül. A regionális vészhelyreállítás, biztonsági másolatot kell készíteni a Virtuálisgép-lemezek be egy másik régióban [Azure Backup szolgáltatás](../articles/backup/backup-introduction-to-azure-backup.md) és a egy biztonsági mentési tár GRS társzolgáltatás fiókját. Az Azure Backup jelenleg legfeljebb 4 TB-os lemez adatlemez-méretet támogatja. Kell [frissítési virtuális gép biztonsági másolat verem v2](../articles/backup/backup-upgrade-to-vm-backup-stack-v2.md) 4 TB-os lemezek támogatásához. További információkért lásd: [használata az Azure Backup szolgáltatás a felügyelt lemezekkel rendelkező virtuális gépek](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Árak és számlázás

Managed Disks használata esetén az alábbi számlázási szempontok érvényesek:

* Tárolás típusa

* Lemezméret

* Tranzakciók száma

* Kimenő adatforgalom

* Felügyelt lemez-pillanatképek (teljes lemez másolása)

Vizsgáljuk meg közelebbről ezeket a beállításokat.

**Tárolás típusa:** Managed Disks 3 teljesítményszint kínálja: [Standard HDD](../articles/virtual-machines/windows/standard-storage.md), [Standard SSD](../articles/virtual-machines/windows/disks-standard-ssd.md), és [prémium](../articles/virtual-machines/windows/premium-storage.md). Felügyelt lemez a számlázás a a lemez kiválasztott tárolási típustól függ.

**Lemezméret**: felügyelt lemezek díjszabása a kiépített a lemez mérete függ. Azure maps-a kiépítési méret (kerekítve) a legközelebbi Managed Disks beállítás, az alábbi táblázatban megadott módon. Minden egyes felügyelt lemezéhez vannak leképezve a támogatott kiosztott méretek egyikét, és ennek megfelelően történik. Például ha standard szintű felügyelt lemez létrehozása, és adja meg a 200 GB kiosztott méretét, számítjuk fel a díjszabás a S15 lemez típusa alapján.

Íme egy prémium szintű felügyelt lemez a lemezek mérete:

| **Prémium szintű SDD felügyelt <br>lemez típusa** | **P4** | **P6** | **P10** | **P15** | **P20** | **P30** | **P40** | **P50** | **A P60** | **P70** | **P80** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Lemezméret        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TiB) | A 2048 giB (2 Tib-ra) | 4095 giB (4 TiB) | 8192 giB (8 TiB) | 16384 giB (16 TiB) | – 32 767 giB (TiB) |

Íme egy standard szintű felügyelt SSD-lemez a lemezek mérete:

| **Standard SSD-felügyelt <br>lemez típusa** | **E10** | **E15** | **E20** | **E30** | **E40** | **E50** | **E60** | **E70** | **E80** |
|------------------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Lemezméret        | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TiB) | A 2048 giB (2 Tib-ra) | 4095 giB (4 TiB) | 8192 giB (8 TiB) | 16384 giB (16 TiB) | – 32 767 giB (TiB) |

Itt érhetők el a lemezméretet HDD standard szintű felügyelt lemez:

| **Felügyelt standard HDD <br>lemez típusa** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** | **S60** | **S70** | **S80** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------|----------------|----------------|----------------|
| Lemezméret        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 giB (1 TiB) | A 2048 giB (2 Tib-ra) | 4095 giB (4 TiB) | 8192 giB (8 TiB) | 16384 giB (16 TiB) | – 32 767 giB (TiB) |

**A tranzakciók**: a standard szintű felügyelt lemez végrehajtott tranzakciók száma számlázzuk ki.

Standard SSD-lemez i/o-egység mérete 256 KB-os használja. Ha az átvitt adatok kisebb, mint 256 KB-os, akkor számít 1 i/o-egységek. Nagyobb méretű i/o-méretek számítanak több i/o-méret 256 KB. Ha például egy 1100 KB i/o öt i/o-egységek számítanak.

Nincs tranzakció a prémium szintű felügyelt lemez számára ingyenesen.

**Kimenő adatforgalom**: [kimenő adatforgalom](https://azure.microsoft.com/pricing/details/data-transfers/) (adatok csak az Azure adatközpontok kimenő adatforgaloma) díjak lépnek fel a sávszélesség-használat.

A Managed Disks díjszabása a részletes információkért lásd: [Managed Disks díjszabását ismertető](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Felügyelt lemez-pillanatképek

Egy felügyelt pillanatkép alapértelmezés szerint egy standard szintű felügyelt lemezként tárolt felügyelt lemez csak olvasható teljes másolatát. Pillanatképek készíthető a felügyelt lemezek bármikor időben. Ezek a pillanatképek létezik a forráslemez független, és új felügyelt lemezek létrehozására használható. Azok a felhasznált mérete alapján számlázzuk. Például ha létrehoz egy felügyelt lemez pillanatképének 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatok mérete, pillanatkép az csak a felhasznált adatok mérete 10 GB számítjuk.  

[Növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md) Managed Disks esetében jelenleg nem támogatottak.

A Managed Disks pillanatképek létrehozásával kapcsolatos további tudnivalókért lásd a következőket:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

## <a name="images"></a>Képek

Felügyelt lemezeket is támogatja a felügyelt egyéni lemezképek létrehozásával. Rendszerképet hozhat létre, a storage-fiókban lévő egyéni VHD-ből, vagy közvetlenül általánosított virtuális gépből (sys felhőkonfigurációt). Ez a folyamat minden kezelt hozzárendelt virtuális gép, beleértve az operációs rendszer és a az adatlemezek egyetlen rendszerképbe rögzíti. A felügyelt egyéni lemezképek létrehozása több száz virtuális gépeket kellene az egyéni rendszerkép használatával másolja, vagy minden olyan storage-fiókok kezelése lehetővé teszi.

Lemezképek létrehozásával kapcsolatos információkért tekintse meg a következő cikkeket:

* [Az Azure-beli általánosított virtuális gép felügyelt rendszerképének rögzítése](../articles/virtual-machines/windows/capture-image-resource.md)
* [Hogyan generalize és az Azure parancssori felületével Linux rendszerű virtuális gép rögzítése](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Képek és pillanatképek

Gyakran használt virtuális gépek az "image" szó látható, és a "pillanatkép" is megjelenik. Fontos feltételek közötti különbségek megértése. A Managed Disks is igénybe vehet fel lettek szabadítva, általánosított virtuális gép képe. Ez a rendszerkép tartalmazza a virtuális Géphez csatlakoztatott lemezeket. Ez a rendszerkép használatával hozzon létre egy új virtuális Gépet, és az összes lemez tartalmazza.

Pillanatkép használatban van ideje azon a ponton a lemez egy példányát. Egy lemezt csak vonatkozik. Ha csak egy lemezt (az operációs rendszer) rendelkező virtuális gép, egy pillanatkép vagy a kép azt, és hozzon létre egy virtuális Gépet a pillanatkép vagy a kép.

Mi történik, ha egy virtuális gép öt lemezzel rendelkezik, és azok csíkozott? Minden lemez pillanatképét is eltarthat, de nincs nincs a virtuális gépen, a lemezek – állapotának figyelése a pillanatképek csak egy lemezen tudni. Ebben az esetben a pillanatképek kell lennie a koordinált egymással, és, amely jelenleg nem támogatott.

## <a name="managed-disks-and-encryption"></a>A felügyelt lemezek és a titkosítás

Nincsenek kétféle titkosítási vitatni a felügyelt lemezek kontextusában. Az elsőt a Storage Service Encryption (SSE), ami a storage szolgáltatás végzi. A második érték a virtuális gépek számára az operációs rendszer és az adatlemezek engedélyezheti az Azure Disk Encryption.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

[Az Azure Storage Service Encryption](../articles/storage/common/storage-service-encryption.md) inaktív titkosítási biztosít, és az adatok biztonsági és megfelelőségi követelmények kielégítésével biztonságos. Az SSE alapértelmezés szerint engedélyezve van a minden Managed Disks, pillanatképeket és lemezképeket minden olyan régióban, ahol a felügyelt lemezek elérhetők. 2017. június 10., kezdve az összes új felügyelt lemezek vagy pillanatképek és lemezképek és a meglévő felügyelt lemezekre írt új adathoz automatikusan titkosított inaktív alapértelmezés szerint a Microsoft által felügyelt kulcsokkal. Látogasson el a [felügyelt lemezek gyakori kérdéseket tartalmazó oldal](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) további részletekért.

### <a name="azure-disk-encryption-ade"></a>Az Azure Disk Encryption (ADE)

Az Azure Disk Encryption lehetővé teszi, hogy az operációs rendszer és az adatlemezek, az IaaS virtuális gép által használt titkosítását. A titkosítás a felügyelt lemezeket tartalmaz. A Windows a meghajtók titkosítottak, iparági szabványnak megfelelő BitLocker titkosítási technológia használatával. A Linux rendszerre a lemezek titkosítottak, a DM-Crypt technológia használatával. A titkosítási folyamat integrálva van az Azure Key Vault lehetővé teszi a vezérlőelemet és kezelheti a lemeztitkosítási kulcsok. További információkért lásd: [Azure Disk Encryption a Windows és Linux rendszerű IaaS virtuális gépek](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>További lépések

Felügyelt lemezekkel kapcsolatos további információkért tekintse meg a következő cikkekben talál.

### <a name="get-started-with-managed-disks"></a>Ismerkedés a Managed Disks szolgáltatással

* [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Linux virtuális gép létrehozása az Azure parancssori felülettel](../articles/virtual-machines/linux/quick-create-cli.md)

* [A PowerShell használatával Windows virtuális gép felügyelt adatlemez csatolása](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Felügyelt lemez hozzáadása Linux rendszerű virtuális géphez](../articles/virtual-machines/linux/add-disk.md)

* [Felügyelt lemezek PowerShell-Mintaszkriptek](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Felügyelt lemezek használata Azure Resource Manager-sablonok](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Managed Disks storage lehetőségek összehasonlítása

* [Prémium szintű SSD-lemez](../articles/virtual-machines/windows/premium-storage.md)

* [Standard SSD és HDD-lemezek](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Műveleti útmutató

* [AWS-ről és más platformokra át az Azure Managed Disks](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Azure virtuális gépek konvertálása a managed Disks szolgáltatásba az Azure-ban](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
