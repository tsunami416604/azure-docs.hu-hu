---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: rogara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 399479de0ce9bab29d0338b1155f8b0c1bab542c
ms.sourcegitcommit: caebf2bb2fc6574aeee1b46d694a61f8b9243198
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2018
ms.locfileid: "35414586"
---
# <a name="azure-managed-disks-overview"></a>Az Azure felügyelt lemezekhez – áttekintés

Azure-lemezeket felügyelt egyszerűbbé teszi a Lemezkezelés Azure IaaS virtuális gépekhez való kezelésekor a [tárfiókok](../articles/storage/common/storage-introduction.md) a virtuális gépek lemezei társított. Csak akkor kell megadnia a típus ([szabványos HDD](../articles/virtual-machines/windows/standard-storage.md), szabványos SSD vagy [prémium SSD](../articles/virtual-machines/windows/premium-storage.md)) kell lemez méretét, és Azure hoz létre, és az Ön kezeli a lemezt.

## <a name="benefits-of-managed-disks"></a>Felügyelt lemezek előnyei

Vessen egy pillantást, ettől kezdve által kezelt lemezek segítségével kezdődő, és ezt a Channel 9 videót előnyöket nyújtja [jobb Azure VM rugalmassági felügyelt lemezzel](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Egyszerű és skálázható módon telepíthetők a Virtuálisgép-telepítéshez

Kezelve lemezek leírók tárolási a háttérben. Korábban meg kellett storage-fiókok a lemezeket (VHD-fájlokat) az Azure virtuális gépek tárolására. Ha vertikális felskálázásával, ellenőrizze, hogy további tárfiókok létrehozott, a lemezek egyik tárolási IOPS-korláttal nem haladhatja meg kellett. A felügyelt lemezek kezelése tárolási, már nem korlátozott a tárfiókok korlátai által (például a 20 000 IOPS / fiók). Már nem kell több tárfiókot másolja az egyéni lemezképek (VHD-fájlokat). – Egy tárfiókot Azure régiónként – központi helyen kezelheti azokat, és hozhatók létre a virtuális gépek több száz egy előfizetésben.

Felügyelt lemezek lehetővé teszi legfeljebb 50 000 virtuális gép létrehozása **lemezek** egy előfizetésben régiónként eltérő típusú, amely lehetővé teszi a több ezer létrehozásához **virtuális gépek** az egy-egy előfizetéshez. Ez a szolgáltatás tovább növeli a méretezhetőséget biztosít a [virtuálisgép-méretezési csoportok](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) azáltal, hogy legfeljebb egy ezred virtuális gépek hozzon létre egy virtuálisgép-méretezési Piactéri rendszerkép használatával be. 

### <a name="better-reliability-for-availability-sets"></a>A rendelkezésre állási csoportok jobb megbízhatóságot

Felügyelt lemezek biztosít nagyobb megbízhatóságot rendelkezésre állási készletek biztosítva, hogy a lemezek [virtuális gépek rendelkezésre állási csoport](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) elég különítve egymástól elkerülése érdekében a hibaérzékeny pontokat. Lemezek különböző tárolási méretezési egységeit (bélyegek) automatikusan kerülnek. A stamp hardver-vagy szoftverhiba miatt meghiúsul, ha csak a Virtuálisgép-példányok az adott bélyegzők lemezzel sikertelen. Ha tegyük fel például, öt virtuális gépeken futó egy alkalmazást, és a virtuális gépek rendelkezésre állási csoport. A lemezek a virtuális gépek nem tárolja az azonos bélyegen, így ha egyetlen stampet konfiguráljon leáll, az alkalmazás más példányát tovább futnak.

### <a name="highly-durable-and-available"></a>Tartós és magas rendelkezésre állású

Az Azure Disks 99,999%-os elérhetőséggel büszkélkedhet. REST-könnyebb ismerete, hogy rendelkezik-e az adatok, amely lehetővé teszi a magas tartósság három replikákat. Ha egy vagy két replika meghibásodik, a fennmaradó replikák azok feladatátvételével garantálják az adatok megőrzését és a magas fokú hibatűrő képességet. Ezzel a kialakítással az Azure rendszeresen vállalati szintű tartósságot nyújthat az IaaS-lemezeknek, iparágvezető NULLA %-os Éves Hibaszázalékkal. 

### <a name="granular-access-control"></a>A részletes hozzáférés-vezérlés

Használhat [átruházásához hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md) felügyelt lemezes jellemző engedélyek hozzárendelése egy vagy több felhasználó. Felügyelt lemezek tesz elérhetővé a különféle műveletek, beleértve az olvasási, írási (létrehozása/frissítése), törölje és lekérése egy [közös hozzáférésű jogosultságkód (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) a lemezhez. Csak azokat a műveleteket, a feladatok elvégzéséhez szükséges egy személy hozzáférést biztosíthat. Például ha nem szeretné felügyelt lemezes tárfiókba másolása egy személy, is nem kíván hozzáférést biztosíthat az exportálási művelet a felügyelt lemezt. Hasonlóképpen ha egy személy egy SAS URI követve másolja át a felügyelt lemezes, hogy nem szeretné, dönthet úgy, nem engedélyt szeretne megadni, hogy a kezelt lemezre.

### <a name="azure-backup-service-support"></a>Az Azure Backup szolgáltatás támogatása
A kezelt lemezek Azure Backup szolgáltatás használatával hozzon létre egy biztonsági mentési feladat idő-alapú biztonsági mentések, könnyű VM-helyreállítás és biztonsági mentési adatmegőrzési. Felügyelt lemezek csak a következő replikálási beállítás helyileg redundáns tárolás (LRS) támogatják. Az adatok három másolatot egyetlen régión belül tárolják. Regionális katasztrófa utáni helyreállítás, biztonsági másolatot kell készíteni a virtuális gépek lemezei be egy másik régióban [Azure Backup szolgáltatás](../articles/backup/backup-introduction-to-azure-backup.md) és a biztonsági mentési tárolóval Georedundáns tárfiókot. Azure Backup szolgáltatás jelenleg összes lemez méretének beleértve 4 TB-os lemezeken. Kell [frissítési virtuális gép biztonsági mentési verem V2](../articles/backup/backup-upgrade-to-vm-backup-stack-v2.md) 4 TB-os lemezeken támogatásához. További információkért lásd: [használata Azure Backup szolgáltatás felügyelt lemezzel rendelkező virtuális gépek](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Árak és számlázás

Felügyelt lemezek használatakor az alábbi számlázási szempontok érvényesek:
* Tárolási típus

* Lemezméret

* Tranzakciók száma

* Kimenő adatforgalom

* Felügyelt lemez – pillanatfelvételek (teljes lemezmásolás)

Megtudhatja, hogy ezek a lehetőségek részletes bemutatása.

**Tárolási típus:** felügyelt lemezek 3 teljesítmény rétegek kínál: [szabványos HDD](../articles/virtual-machines/windows/standard-storage.md), szabványos SSD (előzetes verzió), és [prémium](../articles/virtual-machines/windows/premium-storage.md). Egy kezelt lemez számlázási függ a lemez kiválasztott tárolási típusát.


**Lemezméret**: felügyelt lemezek számlázási a kiépített lemez méretének függ. Azure a kiépített mérete (kerekítve) van leképezve a legközelebbi felügyelt lemezek beállítást az alábbi táblázatban megadottak. Minden felügyelt lemezes egy, a támogatott kiosztott méretű van leképezve, és ennek megfelelően van-e terhelve. Például hozzon létre egy standard szintű felügyelt lemezes és 200 GB-os kiosztott méretet adjon meg, ha díját is felszámítjuk a S15 lemeztípus díjszabás szerint.

Íme egy prémium szintű felügyelt lemezes a lemezméret:

| **Prémium szintű kezelt <br>lemez típusa** | **P4** | **P6** |**P10** | **P15** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Lemezméret        | 32 GiB   | 64 GiB   | 128 GiB  | 256 GiB  | 512 GiB  | 1024 GiB (1 TiB) | 2048 giB (2 TiB) | 4095 GiB (4 TiB) | 

Íme egy SSD standard szintű felügyelt lemezes a lemezméret:

| **Standard szintű felügyelt SSD <br>lemez típusa** | **E10** | **E15** | **E20** | **E30** | **E40** | **E50** |
|------------------|--------|--------|--------|----------------|----------------|----------------| 
| Lemezméret        | 128 GiB | 256 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 giB (2 TiB) | 4095 GiB (4 TiB) | 

Íme egy HDD standard szintű felügyelt lemezes a lemezméret:

| **Standard szintű felügyelt HDD <br>lemez típusa** | **S4** | **S6** | **S10** | **S15** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|--------|----------------|----------------|----------------| 
| Lemezméret        | 32 GiB  | 64 GiB  | 128 GiB | 256 GiB | 512 GiB | 1024 GiB (1 TiB) | 2048 giB (2 TiB) | 4095 GiB (4 TiB) | 


**A tranzakciók számának**: hajt végre egy standard szintű felügyelt lemezes tranzakciók száma a kell fizetni.

Standard SSD-lemezek I/O foglalásiegység-méret 256 KB-os használja. Ha az átvitt adatok kisebb, mint 256 KB, ez 1 i/o-egység tekintendő. Nagyobb i/o-méretek számlálási több i/o mérete 256 KB. Egy 1100 KB i/o például, öt i/o-egységek számítanak.

Nincs a prémium szintű felügyelt lemezes tranzakciók költség nélkül.

**Kimenő adatátvitel**: [kimenő adatátviteli](https://azure.microsoft.com/pricing/details/data-transfers/) (az adatok Azure-adatközpont kilépő) gigabájtalapú sávszélesség-használat.

Felügyelt lemezek árakkal kapcsolatos részletes információkért lásd: [lemezek árképzési felügyelt](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Felügyelt lemezes pillanatképek

Felügyelt pillanatkép egy csak olvasható, teljes másolata egy felügyelt lemezes, amely alapértelmezés szerint egy standard szintű felügyelt lemezes tárolja. A pillanatképekkel akkor készíthet biztonsági másolatot a felügyelt lemezek bármikor időben. Ezeket a pillanatképeket független a forrás lemez létezik, és új kezelt lemezek létrehozásához használható. Azok a felhasznált mérete alapján számlázzuk. Például 64 GiB kiosztott kapacitását és tényleges használt adatok mérete 10 GiB felügyelt lemezes pillanatképet hoz létre, ha pillanatkép alapján számlázzuk csak a 10 GiB használt adatok méretét.  

[Növekményes pillanatképek](../articles/virtual-machines/windows/incremental-snapshots.md) lemezek felügyelete jelenleg nem támogatottak.

A felügyelt lemezek pillanatképek létrehozásával kapcsolatos további tudnivalókért lásd a következőket:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Windows alatt](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Képek

Felügyelt lemezeket is támogatja a felügyelt egyéni lemezkép létrehozása. Kép hozhat létre, az egyéni virtuális merevlemez olyan tárfiókban illetve közvetlenül egy általánosított (sys prepped) virtuális Gépet. Ez a folyamat minden felügyelt egy virtuális Gépet, beleértve az operációs rendszer és az adatok lemezek hozzárendelt egyetlen rendszerképbe rögzíti. A felügyelt egyéni iamge lehetővé teszi, hogy a virtuális gépek az egyéni lemezképet, nem kell másolnia, vagy a storage-fiókok kezelése létrehozása több száz.

Lemezképek létrehozásával kapcsolatos további információkért lásd: a következő cikkeket:
* [Egy felügyelt képre az Azure-ban egy általánosított virtuális gép rögzítése](../articles/virtual-machines/windows/capture-image-resource.md)
* [Generalize és az Azure CLI 2.0 használatával Linux virtuális gép rögzítése](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>A pillanatképek és lemezképek

Gyakran használt virtuális gépek "kép" szó látható, és ekkor megjelenik a "pillanatképek" is. Fontos, hogy ezek a feltételek közötti különbségek megértése. A felügyelt lemezek esetében is igénybe vehet egy általánosított virtuális Gépet, amely fel lettek szabadítva képe. Ez a rendszerkép tartalmazza a virtuális Géphez csatlakozik, a lemezeket. Ez a rendszerkép használatával hozzon létre egy új virtuális Gépet, és ez magában foglalja a lemezeket.

Pillanatkép van szükséges idő a ponton a lemez egy példányát. Csak egy lemezre vonatkozik. Ha egy lemez (az operációs rendszer) rendelkező virtuális gép, pillanatkép vagy egy képet, és vagy a pillanatképet, vagy a lemezképet a virtuális gép létrehozása.

Mi történik, ha a virtuális gépek öt lemezzel rendelkezik, és azok csíkozott? Minden lemez pillanatképet készíthet, de nincs nincs belül a virtuális Gépet, a lemezek – állapotának figyelése a pillanatképek csak tudni, hogy egy lemez. Ebben az esetben a pillanatképek kell lennie a koordinált egymással, és, amely jelenleg nem támogatott.

## <a name="managed-disks-and-encryption"></a>Felügyelt lemezek és titkosítás

Nincsenek titkosítási és beszéljék meg, állapotalapú felügyelt lemezek kétféle. Az elsőt a Storage szolgáltatás titkosítási (SSE), amely végzi el a társzolgáltatás. A második érték engedélyezheti a virtuális gépek az operációs rendszer és az adatok lemezeken Azure Disk Encryption.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

[Az Azure Storage szolgáltatás titkosítási](../articles/storage/common/storage-service-encryption.md) nyugalmi titkosítási biztosít, és megakadályozhatja az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi jár kötelezettségekkel. SSE alapértelmezés szerint engedélyezve van a minden kezelt lemezek, a pillanatképek és a képek minden régióban, ahol a felügyelt lemezek elérhetők. 2017. június 10., kezdve az összes új felügyelt lemezek/pillanatképek/képek és új adatokat írni a meglévő felügyelt lemezek automatikusan titkosítva nyugalmi alapértelmezés szerint a Microsoft által felügyelt kulcsokkal. Látogasson el a [kezelt lemezek gyakori kérdéseket tartalmazó oldal](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) további részleteket.


### <a name="azure-disk-encryption-ade"></a>Az Azure Disk Encryption (ADE)

Az Azure Disk Encryption lehetővé teszi, hogy az operációsrendszer- és adatlemezek egy infrastruktúra-szolgáltatási virtuális gép által használt titkosítását. A titkosítás használatába beletartozik a felügyelt lemezek. A Windows a meghajtók titkosítása szabványos BitLocker titkosítás technológia használatával. Linux a lemez titkosítása a DM-Crypt technológia használatával. A titkosítási folyamat integrálva van az Azure Key Vault lehetővé teszi a lemez titkosítási kulcsok kezeléséhez, és szabályozhatja. További információkért lásd: [lemez titkosítás a Windows Azure és a Linux IaaS virtuális gépeket](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>További lépések

A felügyelt lemezek kapcsolatos további információkért tekintse meg a következő cikkekben talál.

### <a name="get-started-with-managed-disks"></a>Ismerkedés a Managed Disks szolgáltatással

* [Virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Linux-alapú virtuális gép létrehozása az Azure CLI 2.0-s verziójával](../articles/virtual-machines/linux/quick-create-cli.md)

* [Felügyelt adatlemezt csatolni egy Windows-VM PowerShell-lel](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Felügyelt lemez hozzáadása Linux rendszerű virtuális géphez](../articles/virtual-machines/linux/add-disk.md)

* [Felügyelt lemezek PowerShell-mintaparancsfájlok](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Felügyelt lemezek használhatók Azure Resource Manager-sablonok](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Felügyelt lemezek tárolási lehetőségek összehasonlítása

* [Premium SSD-lemezek](../articles/virtual-machines/windows/premium-storage.md)

* [Standard SSD és HDD-lemezek](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Műveleti útmutató

* [Az Azure-ban kezelt lemezek áttelepítésére AWS és más platformokra](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Alakítsa át a Azure virtuális gépeken futó felügyelt Azure-ban](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
