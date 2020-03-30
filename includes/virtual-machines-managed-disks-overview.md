---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 72b6c1f86b18df172994827ec78eb109fe82454e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75913766"
---
## <a name="benefits-of-managed-disks"></a>A felügyelt lemezek előnyei

Vegyük át a felügyelt lemezek használatával szerzett előnyök et.

### <a name="highly-durable-and-available"></a>Rendkívül tartós és elérhető

A felügyelt lemezek 99,999%-os rendelkezésre állásra vannak tervezve. A felügyelt lemezek ezt úgy érik el, hogy az adatok három replikáját biztosítják, ami lehetővé teszi a nagy tartósságot. Ha egy vagy akár két replikák problémákat tapasztal, a fennmaradó replikák segít biztosítani az adatok megőrzése és a hibák magas tolerancia. Ez az architektúra segítségével az Azure következetesen nagyvállalati szintű tartósságot biztosít az infrastruktúra szolgáltatásként (IaaS) lemezként, az iparág vezető ZERO% éves hibaarányával.

### <a name="simple-and-scalable-vm-deployment"></a>Egyszerű és méretezhető virtuális gépek telepítése

Felügyelt lemezek használatával létrehozhat akár 50 000 virtuálisgép-lemezek egy típusú előfizetés régiónként, amely lehetővé teszi, hogy hozzon létre több ezer **virtuális gépek** egyetlen előfizetésben. **disks** Ez a funkció tovább növeli a [virtuálisgép-méretezési készletek](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) méretezhetőségét azáltal, hogy lehetővé teszi, hogy akár 1000 virtuális gépet hozzon létre egy virtuálisgép-méretezési készletben egy Marketplace-lemezkép használatával.

### <a name="integration-with-availability-sets"></a>Integráció a rendelkezésre állási készletekkel

A felügyelt lemezek integrálva vannak a rendelkezésre állási készletekkel annak érdekében, hogy a [rendelkezésre állási csoportban lévő virtuális gépek](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) lemezei eléggé el legyenek különítve egymástól, hogy elkerüljék a meghibásodás egyetlen pontját. A lemezek automatikusan különböző tárolóméretezési egységekbe (bélyegzőkbe) kerülnek. Ha egy bélyegző hardver- vagy szoftverhiba miatt meghibásodik, csak a lemezeket tartalmazó virtuálisgép-példányok sikertelenek. Tegyük fel például, hogy öt virtuális gépen futó alkalmazással rendelkezik, és a virtuális gépek egy rendelkezésre állási csoportban vannak. A virtuális gépek lemezei nem minden ugyanabban a bélyegzőben tárolódnak, így ha az egyik bélyegző leáll, az alkalmazás többi példánya továbbra is fut.

### <a name="integration-with-availability-zones"></a>Integráció a rendelkezésre állási zónákkal

A felügyelt lemezek [támogatják a rendelkezésre állási zónákat,](../articles/availability-zones/az-overview.md)amely egy magas rendelkezésre állású ajánlat, amely megvédi az alkalmazásokat az adatközponti hibáktól. A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rugalmasság biztosítása érdekében legalább három különálló zóna van az összes engedélyezett régióban. A rendelkezésre állási zónákkal az Azure az iparág legjobb 99,99%-os virtuálisgép-rendelkezésre állási SLA-ját kínálja.

### <a name="azure-backup-support"></a>Az Azure Backup támogatása

A regionális katasztrófák elleni védelem érdekében [az Azure Backup](../articles/backup/backup-overview.md) segítségével időalapú biztonsági mentéseket és biztonsági mentési adatmegőrzési szabályzatokat hozhat létre. Ez lehetővé teszi, hogy egyszerű virtuális gép-visszaállítások lesz. Jelenleg az Azure Backup legfeljebb négy tebibyte (TiB) lemezméretet támogat.  Az Azure Backup támogatja a felügyelt lemezek biztonsági mentését és visszaállítását. [További információ](../articles/backup/backup-support-matrix-iaas.md) az Azure virtuális gépek biztonsági mentésének támogatásáról.

### <a name="granular-access-control"></a>Részletes hozzáférés-vezérlés

[Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md) használatával adott engedélyeket rendelhet egy felügyelt lemezhez egy vagy több felhasználóhoz. A felügyelt lemezek számos műveletet elérhetővé, például olvasási, írási (létrehozási/frissítési), törlési és a [lemez sas-uri-jának](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) lekérésével. Csak azadott műveletekhez adhat hozzáférést, amelyeknek egy személynek el kell végeznie a feladatát. Ha például nem szeretné, hogy egy személy felügyelt lemezt másoljon egy tárfiókba, dönthet úgy, hogy nem ad hozzáférést az adott felügyelt lemez exportálási műveletéhez. Hasonlóképpen, ha nem szeretné, hogy egy személy SAS URI-t használjon felügyelt lemez másolásához, dönthet úgy, hogy nem adja meg ezt az engedélyt a felügyelt lemeznek.

### <a name="upload-your-vhd"></a>Töltse fel a vhd

 A közvetlen feltöltés megkönnyíti a vhd átvitelét egy Azure által felügyelt lemezre. Korábban egy nagyobb részt vevő folyamatot kellett követnie, amely az adatok tárfiókban való átmeneti beállítását tartalmazta. Most már kevesebb a lépés. A helyszíni virtuális gépek et könnyebb feltölteni az Azure-ba, nagy felügyelt lemezekre feltölteni, és a biztonsági mentési és visszaállítási folyamat egyszerűsödik. Emellett csökkenti a költségeket azáltal, hogy lehetővé teszi az adatok feltöltését a felügyelt lemezekre közvetlenül anélkül, hogy a virtuális gépekhez csatolná őket. A közvetlen feltöltés segítségével akár 32 TiB méretű vhd-eket is feltölthetsz.

 A vhd Azure-ba való átvitelének módjáról a [CLI-](../articles/virtual-machines/linux/disks-upload-vhd-to-managed-disk-cli.md) vagy a [PowerShell-cikkekben](../articles/virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md) olvashat.

## <a name="encryption"></a>Titkosítás

A felügyelt lemezek két különböző titkosítást kínálnak. Az első a kiszolgálóoldali titkosítás (SSE), amelyet a tárolószolgáltatás hajt végre. A második az Azure Disk Encryption (ADE), amely et az operációs rendszer és a virtuális gépek adatlemezek engedélyezheti.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

[Az Azure Server-side Encryption](../articles/virtual-machines/windows/disk-encryption.md) inaktív titkosítást biztosít, és védi az adatokat, hogy megfeleljenek a szervezeti biztonsági és megfelelőségi kötelezettségvállalásoknak. A kiszolgálóoldali titkosítás alapértelmezés szerint engedélyezve van az összes felügyelt lemez, pillanatkép és lemezkép számára minden olyan régióban, ahol felügyelt lemezek érhetők el. Engedélyezheti, hogy az Azure kezelje a kulcsokat, ezek platformáltal kezelt kulcsok, vagy saját maga kezelheti a kulcsokat, ezek az ügyfelek által felügyelt kulcsok. További részletekért látogasson el a [Felügyelt lemezek GYIK oldalra.](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption)

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Az Azure Disk Encryption lehetővé teszi az IaaS virtuális gép által használt operációs rendszer és adatlemezek titkosítását. Ez a titkosítás felügyelt lemezeket is tartalmaz. A Windows esetében a meghajtók az iparági szabványnak megfelelő BitLocker titkosítási technológiával vannak titkosítva. Linux esetén a lemezek a DM-Crypt technológiával vannak titkosítva. A titkosítási folyamat integrálva van az Azure Key Vaulttal, így vezérelheti és felügyelheti a lemeztitkosítási kulcsokat. További információ: [Azure Disk Encryption for IaaS virtuális gépek.](../articles/security/azure-security-disk-encryption-overview.md)

## <a name="disk-roles"></a>Lemezes szerepkörök

Az Azure-ban három fő lemezszerepkör található: az adatlemez, az operációs rendszer lemeze és az ideiglenes lemez. Ezek a szerepkörök a virtuális géphez csatlakoztatott lemezekre vannak leképezve.

![Lemezszerepkörök működés közben](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Adatlemez

Az adatlemez olyan felügyelt lemez, amely egy virtuális géphez van csatolva az alkalmazásadatok vagy egyéb megtartandó adatok tárolására. Az adatlemezek SCSI-meghajtóként vannak regisztrálva, és a kiválasztott betűvel vannak ellátva. Minden adatlemez maximális kapacitása 32 767 gibibájt (GiB). A virtuális gép mérete határozza meg, hogy hány adatlemez t csatolhat hozzá, és hogy milyen típusú tárolót használhat a lemezek üzemeltetéséhez.

### <a name="os-disk"></a>Operációsrendszer-lemez

Minden virtuális gép rendelkezik egy csatlakoztatott operációsrendszer-lemezzel. Az operációs rendszer lemeze rendelkezik egy előre telepített operációs rendszerrel, amely a virtuális gép létrehozásakor lett kijelölve. Ez a lemez tartalmazza a rendszerindító kötetet.

A lemez maximális kapacitása 2048 GiB.

### <a name="temporary-disk"></a>Ideiglenes lemez

Minden virtuális gép tartalmaz egy ideiglenes lemezt, amely nem felügyelt lemez. Az ideiglenes lemez rövid távú tárolást biztosít az alkalmazások és folyamatok számára, és csak olyan adatok tárolására szolgál, mint az oldal- vagy lapozófájlok. Az ideiglenes lemezen lévő adatok elveszhetnek egy [karbantartási esemény](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) során, vagy amikor [újratelepít egy virtuális gép.](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) Az Azure Linux virtuális gépeken az ideiglenes lemez alapértelmezés szerint /dev/sdb, windows os virtuális gépeken pedig az ideiglenes lemez alapértelmezés szerint D: . A virtuális gép sikeres szabványos újraindítása során az ideiglenes lemezen lévő adatok megmaradnak.

## <a name="managed-disk-snapshots"></a>Felügyelt lemezpillanatképek

A felügyelt lemez pillanatképe egy alapértelmezetten szabványos felügyelt lemezként tárolt felügyelt lemez írásvédett összeomlás-konzisztens teljes másolata. A pillanatképek segítségével bármikor biztonsági másolatot készíthet a felügyelt lemezekről. Ezek a pillanatképek a forráslemeztől függetlenül léteznek, és új felügyelt lemezek létrehozására használhatók. 

A pillanatképek számlázása a használt méret alapján lesz számlázva. Ha például egy 64 GiB-kapacitású felügyelt lemezről és 10 GiB tényleges használt adatmérettel rendelkező felügyelt lemez pillanatképét hoz létre, a pillanatkép csak a 10 GiB használt adatméretért kerül számlázásra. A pillanatképek használt méretét az [Azure-használati jelentés](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)ből láthatja. Ha például egy pillanatkép használt adatmérete 10 GiB, a **napi** használati jelentés 10 GiB/(31 nap) = 0,3226 lesz a felhasznált mennyiség.

Ha többet szeretne tudni arról, hogyan hozhat létre pillanatképeket felügyelt lemezekhez, olvassa el az alábbi erőforrásokat:

* [Felügyelt lemez pillanatképének létrehozása a Windows rendszerben](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemez pillanatképének létrehozása Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Képek

A felügyelt lemezek is támogatják a felügyelt egyéni lemezkép létrehozását. Létrehozhat egy lemezképet az egyéni virtuális merevlemezből egy tárfiókban vagy közvetlenül egy általános (sysprepped) virtuális gépről. Ez a folyamat egyetlen képet rögzít. Ez a lemezkép tartalmazza a virtuális géphez társított összes felügyelt lemezt, beleértve az operációs rendszert és az adatlemezeket is. Ez a felügyelt egyéni lemezkép lehetővé teszi, hogy több száz virtuális gép létrehozása az egyéni lemezkép használatával, anélkül, hogy másolni vagy kezelni a tárfiókokat.

A képek létrehozásáról az alábbi cikkekben talál további információt:

* [Általános gép felügyelt rendszerképének rögzítése az Azure-ban](../articles/virtual-machines/windows/capture-image-resource.md)
* [Linux rendszerű virtuális gép általánosítása és rögzítése Azure CLI használatával](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Képek és pillanatképek

Fontos megérteni a képek és pillanatképek közötti különbséget. A felügyelt lemezek, egy általános virtuális gép, amely felszabadított egy lemezképet. Ez a lemezkép tartalmazza a virtuális géphez csatlakoztatott összes lemezt. Ezt a lemezképet használhatja egy virtuális gép létrehozásához, és tartalmazza az összes lemezt.

A pillanatkép egy lemez másolata a pillanatkép készítéseke időpontjában. Csak egy lemezre vonatkozik. Ha egy virtuális gép, amely egy lemez (az operációs rendszer lemeze), készíthet egy pillanatképet vagy egy képet róla, és hozzon létre egy virtuális gép akár a pillanatkép vagy a lemezkép.

A pillanatkép nem rendelkezik a lemez, kivéve a benne található. Ez problémássá teszi a több lemez, például a csíkozás koordinációját igénylő forgatókönyvekben való használatát. A pillanatképeknek képesnek kell lenniük egymással egyeztetni, és ez jelenleg nem támogatott.

## <a name="disk-allocation-and-performance"></a>Lemezfoglalás és teljesítmény

Az alábbi ábra a sávszélesség és az IOPS valós idejű lefoglalását ábrázolja a lemezek hez egy háromszintű létesítési rendszer használatával:

![Háromszintű kiépítési rendszer a sávszélesség és az IOPS-lefoglalás megjelenítésével](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Az első szintű kiépítés beállítja a lemezenkénti IOPS és a sávszélesség-hozzárendelés.  A második szinten a számítási kiszolgálógazda ssd-kiépítést valósít meg, és csak a kiszolgáló SSD-n tárolt adatokra alkalmazza, amely gyorsítótárazással (ReadWrite és ReadOnly), valamint helyi és ideiglenes lemezekkel rendelkező lemezeket tartalmaz. Végül a virtuális gép hálózati kiépítése a harmadik szinten történik minden olyan I/O-kapcsolaton, amelyet a számítási gazdagép küld az Azure Storage háttérrendszerének. Ezzel a sémával a virtuális gép teljesítménye számos tényezőtől függ, attól kezdve, hogy a virtuális gép hogyan használja a helyi SSD-t, a csatlakoztatott lemezek számáig, valamint a csatlakoztatott lemezek teljesítményétől és gyorsítótárazási típusától.

Ezeknek a korlátozásoknak a példájaként a virtuális gép Standard_DS1v1 nem éri el a P30-lemez 5000 IOPS-potenciálját, függetlenül attól, hogy az gyorsítótárba van-e tárva, akár nem, az SSD és a hálózati szintek korlátai miatt:

![Standard_DS1v1 példa felosztása](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Az Azure a rangsorolt hálózati csatornát használja a lemezforgalomhoz, amely elsőbbséget élvez a hálózati forgalom más alacsony prioritásával szemben. Ez segít a lemezeknek a várt teljesítmény fenntartásában hálózati versengésesetén. Hasonlóképpen az Azure Storage kezeli az erőforrás-versengéseket és a háttérben lévő egyéb problémákat az automatikus terheléselosztással. Az Azure Storage lefoglalja a szükséges erőforrásokat, amikor létrehoz egy lemezt, és alkalmazza a proaktív és reaktív erőforrások kiegyensúlyozása a forgalmi szint kezelésére. Ez biztosítja továbbá, hogy a lemezek képesek fenntartani a várt IOPS-és átviteli célokat. A virtuális gép szintű és a lemezszintű metrikák segítségével nyomon követheti a teljesítmény és a beállítási riasztások szükség szerint.

Tekintse meg [a tervezés nagy teljesítményű](../articles/virtual-machines/windows/premium-storage-performance.md) cikket, hogy megismerjék a legjobb gyakorlatok optimalizálása VM + Disk konfigurációk, hogy el tudja érni a kívánt teljesítményt

## <a name="next-steps"></a>További lépések

Ha azt szeretné, hogy egy videó részletesebben a felügyelt lemezeken, olvassa el a [következő tetszés: Jobb Azure virtuális gép rugalmassága a felügyelt lemezek.](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency)

Tudjon meg többet az Azure által nyújtott egyes lemeztípusokról, amelyek típusa megfelel az igényeinek, és a lemeztípusokról szóló cikkünkben olvashat a teljesítménycéljaikról.
