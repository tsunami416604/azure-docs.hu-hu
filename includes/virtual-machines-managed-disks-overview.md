---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 444e64488b185b1ff2aa7cc63fbeffafc591dbfe
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68319984"
---
## <a name="benefits-of-managed-disks"></a>A felügyelt lemezek előnyei

Nézzük át a felügyelt lemezek használatával járó előnyöket.

### <a name="highly-durable-and-available"></a>Tartós és magas rendelkezésre állású

A Managed Disks szolgáltatás a 99,999%-os rendelkezésre álláshoz lett tervezve. A Managed Disks ezt az adatait három replikával biztosítja, ami lehetővé teszi a magas tartósságot. Ha egy vagy két replika meghibásodik, a fennmaradó replikák azok feladatátvételével garantálják az adatok megőrzését és a magas fokú hibatűrő képességet. Ez az architektúra segített abban, hogy az Azure következetesen nagyvállalati szintű tartósságot biztosítson a szolgáltatásként nyújtott infrastruktúra (IaaS) számára, amely az iparágban vezető nulla%-os sikertelen meghibásodási arányt eredményez.

### <a name="simple-and-scalable-vm-deployment"></a>Egyszerű és méretezhető virtuális gépek üzembe helyezése

A felügyelt lemezek használatával akár 50 000 virtuálisgép- **lemezt** is létrehozhat egy előfizetésben régiónként, így akár több ezer **virtuális** gépet is létrehozhat egyetlen előfizetésben. Ez a funkció tovább növeli a [virtuálisgép](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) -méretezési csoportok méretezhetőségét azáltal, hogy lehetővé teszi, hogy akár 1 000 virtuális gépet hozzon létre egy virtuálisgép-méretezési csoporton Piactéri rendszerkép használatával.

### <a name="integration-with-availability-sets"></a>Integráció a rendelkezésre állási csoportokkal

A felügyelt lemezek integrálva vannak a rendelkezésre állási csoportokkal annak biztosítása érdekében, hogy a [rendelkezésre állási csoportba tartozó virtuális gépek](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) lemezei elég elszigeteltek legyenek egymástól, hogy elkerülje a meghibásodást. A lemezeket a rendszer automatikusan a különböző tárolási skálázási egységekben (bélyegekben) helyezi el. Ha a bélyegző hardveres vagy szoftveres hiba miatt meghiúsul, csak a bélyeggel rendelkező virtuálisgép-példányok meghibásodnak. Tegyük fel például, hogy egy alkalmazás öt virtuális gépen fut, és a virtuális gépek rendelkezésre állási csoportba tartoznak. Ezeknek a virtuális gépeknek a lemezei nem lesznek ugyanazon bélyegzőn tárolva, így ha az egyik bélyegző leáll, az alkalmazás többi példánya továbbra is futni fog.

### <a name="integration-with-availability-zones"></a>Integráció a Availability Zones

A Managed Disks támogatja a [Availability Zonest](../articles/availability-zones/az-overview.md), amely egy magas rendelkezésre állású ajánlat, amely védelmet nyújt az alkalmazásoknak az adatközpont hibáiból. A rendelkezésre állási zónák egyedi fizikai helyszínek az Azure-régióban. Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. A rugalmasság biztosításához legalább három különálló zónának kell lennie az összes engedélyezett régióban. Az Azure Availability Zones az iparág legjobb 99,99%-os rendelkezésre állását kínálja a virtuális gép számára.

### <a name="azure-backup-support"></a>Azure Backup támogatás

A regionális katasztrófák elleni védelem érdekében [Azure Backup](../articles/backup/backup-overview.md) használható biztonsági mentési feladatok létrehozására időalapú biztonsági mentéssel és adatmegőrzési szabályzatokkal. Így egyszerűen elvégezheti a virtuális gépek helyreállítását. A Azure Backup jelenleg legfeljebb négy tebibyte (TiB) lemezt támogat.  Azure Backup támogatja a felügyelt lemezek biztonsági mentését és helyreállítását. [További](../articles/backup/backup-support-matrix-iaas.md) információ az Azure virtuális gépek biztonsági mentésének támogatásáról.

### <a name="granular-access-control"></a>Részletes hozzáférés-vezérlés

Az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md) használatával konkrét engedélyeket rendelhet egy felügyelt lemezhez egy vagy több felhasználó számára. A felügyelt lemezek számos műveletet tesznek elérhetővé, beleértve az olvasást, az írást (létrehozás/frissítés), a törlést és a lemez [megosztott hozzáférési aláírása (SAS) URI azonosítójának](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) beolvasását. Csak azokhoz a műveletekhez adhat hozzáférést, amelyeket egy személynek el kell végeznie a feladatának elvégzéséhez. Ha például nem szeretné, hogy egy személy átmásolja a felügyelt lemezt egy Storage-fiókba, dönthet úgy, hogy nem engedélyez hozzáférést az adott felügyelt lemez exportálási műveletéhez. Hasonlóképpen, ha nem szeretné, hogy egy személy SAS URI-t használjon egy felügyelt lemez másolásához, akkor dönthet úgy, hogy nem adja meg ezt az engedélyt a felügyelt lemez számára.

## <a name="encryption"></a>Encryption

A felügyelt lemezek két különböző típusú titkosítást kínálnak. Az első Storage Service Encryption (SSE), amelyet a Storage szolgáltatás hajt végre. A második Azure Disk Encryption, amelyet engedélyezheti a virtuális gépek operációsrendszer-és adatlemezei számára.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

Az [Azure Storage Service encryption](../articles/storage/common/storage-service-encryption.md) titkosítást biztosít, és gondoskodik az adatok védelméről a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítése érdekében. Az SSE alapértelmezés szerint engedélyezve van minden felügyelt lemez, pillanatkép és rendszerkép számára az összes olyan régióban, ahol a felügyelt lemezek elérhetők. További részletekért látogasson el a [Managed Disks GYIK oldalára](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) .

### <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

Azure Disk Encryption lehetővé teszi a IaaS virtuális gépek által használt operációs rendszer és adatlemezek titkosítását. Ez a titkosítás felügyelt lemezeket tartalmaz. A Windows rendszerben a meghajtók titkosítása az iparági szabványnak megfelelő BitLocker titkosítási technológiával történik. A Linux rendszerben a lemezek titkosítása a DM-Crypt technológiával történik. A titkosítási folyamat integrálva van Azure Key Vault, hogy lehetővé tegye a lemezes titkosítási kulcsok vezérlését és kezelését. További információ: [Azure Disk Encryption IaaS virtuális gépekhez](../articles/security/azure-security-disk-encryption-overview.md).

## <a name="disk-roles"></a>Lemez szerepkörei

Az Azure-ban három fő lemez szerepkör található: az adatlemez, az operációsrendszer-lemez és az ideiglenes lemez. Ezek a szerepkörök leképezik a virtuális géphez csatolt lemezeket.

![A lemez szerepkörei működés közben](media/virtual-machines-managed-disks-overview/disk-types.png)

### <a name="data-disk"></a>Adatlemez

Az adatlemezek egy felügyelt lemez, amely egy virtuális géphez van csatolva az alkalmazásadatok tárolásához, vagy más, a megtartani kívánt adatvédelemhez. Az adatlemezek SCSI-meghajtóként vannak regisztrálva, és a kiválasztott betűvel vannak megjelölve. Minden adatlemez maximális kapacitása 32 767 gibibájtban értendők (GiB). A virtuális gép mérete határozza meg, hogy hány adatlemezt tud csatolni hozzá, valamint a lemezek tárolására használható tárterület típusát.

### <a name="os-disk"></a>Operációsrendszer-lemez

Minden virtuális gépnek van egy csatlakoztatott operációsrendszer-lemeze. Az operációsrendszer-lemezen telepítve van egy előre telepített operációs rendszer, amely a virtuális gép létrehozásakor lett kiválasztva.

A lemez maximális kapacitása 2 048 GiB.

### <a name="temporary-disk"></a>Ideiglenes lemez

Minden virtuális gép tartalmaz egy ideiglenes lemezt, amely nem felügyelt lemez. Az ideiglenes lemez rövid távú tárolást biztosít az alkalmazások és folyamatok számára, és kizárólag az olyan adattárolást célozza meg, mint például az oldal vagy a lapozófájlok. Előfordulhat, hogy az ideiglenes lemezen lévő adatvesztés egy [karbantartási esemény](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) bekövetkezésekor vagy [egy virtuális gép újratelepítése](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)során elvész. Az Azure Linux rendszerű virtuális gépeken az ideiglenes lemez alapértelmezés szerint/dev/sdb, és a Windows rendszerű virtuális gépeken az ideiglenes lemez alapértelmezés szerint D:. A virtuális gép sikeres újraindítása során az ideiglenes lemezen tárolt adatmennyiség továbbra is fennáll.

## <a name="managed-disk-snapshots"></a>Felügyelt lemezek pillanatképei

A felügyelt lemez pillanatfelvétele egy felügyelt lemez írásvédett, alapértelmezés szerint a standard szintű felügyelt lemezként tárolt teljes másolata. A pillanatképekkel bármikor készíthet biztonsági mentést a felügyelt lemezekről. Ezek a pillanatképek függetlenek a forrásoldali lemeztől, és új felügyelt lemezek létrehozásához használhatók. A díjak a felhasznált méret alapján lesznek kiszámlázva. Ha például létrehoz egy pillanatképet egy felügyelt lemezről, amely 64 GiB kiépített kapacitással rendelkezik, és a tényleges felhasznált adatok mérete 10 GiB, a pillanatkép számlázása csak a 10 GiB felhasznált adatméretre történik.  

Ha szeretne többet megtudni arról, hogyan hozhat létre pillanatképeket a felügyelt lemezekkel, tekintse meg a következő forrásokat:

* [Felügyelt lemezként tárolt VHD másolatának létrehozása Pillanatképek használatával a Windowsban](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Képek

A felügyelt lemezek támogatják a felügyelt Egyéni rendszerképek létrehozását is. Létrehozhat egy rendszerképet az egyéni VHD-ből egy Storage-fiókban, vagy közvetlenül egy általánosított (Sysprep használatával létrehozott) virtuális gépről. Ez a folyamat egyetlen lemezképet rögzít. Ez a rendszerkép a virtuális géphez társított összes felügyelt lemezt tartalmazza, beleértve az operációs rendszert és az adatlemezeket is. Ez a felügyelt egyéni rendszerkép lehetővé teszi több száz virtuális gép létrehozását az egyéni rendszerkép használatával anélkül, hogy bármilyen Storage-fiókot kellene másolni vagy kezelnie.

A rendszerképek létrehozásával kapcsolatos információkért tekintse meg a következő cikkeket:

* [Általánosított virtuális gép felügyelt rendszerképének rögzítése az Azure-ban](../articles/virtual-machines/windows/capture-image-resource.md)
* [Linux rendszerű virtuális gép általánosítása és rögzítése Azure CLI használatával](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Képek és Pillanatképek

Fontos megérteni a képek és a pillanatképek közötti különbséget. A felügyelt lemezekkel olyan általánosított virtuális gép képe látható, amely fel lett foglalva. Ez a rendszerkép tartalmazza a virtuális géphez csatolt összes lemezt. Ezt a rendszerképet használhatja a virtuális gép létrehozásához, és tartalmazza az összes lemezt.

A pillanatkép egy lemez másolata a pillanatkép készítésének időpontjában. Csak egy lemezre vonatkozik. Ha van olyan virtuális gépe, amely egy lemezzel rendelkezik (az operációsrendszer-lemezzel), pillanatképet vagy képet készíthet róla, és létrehozhat egy virtuális gépet a pillanatképből vagy a lemezképből.

A pillanatképek nem rendelkeznek minden lemezről, kivéve a benne lévőket. Ez olyan helyzetekben jelent problémát, amelyek több lemez (például csíkozás) koordinálását igénylik. A pillanatképeknek össze kell tudniuk hangolni egymással, és ez jelenleg nem támogatott.

## <a name="disk-allocation-and-performance"></a>Lemez kiosztása és teljesítménye

A következő ábra a sávszélesség és a IOPS valós idejű kiosztását ábrázolja a három szintű kiépítési rendszer használatával:

![Három szintű kiépítési rendszeren a sávszélesség és a IOPS kiosztása látható](media/virtual-machines-managed-disks-overview/real-time-disk-allocation.png)

Az első szintű kiépítés a lemezes IOPS és a sávszélesség-hozzárendelést állítja be.  A második szinten a számítási kiszolgáló gazdagépe az SSD-kiépítés megvalósítását végzi, csak a kiszolgáló SSD-fájljain tárolt adatokra alkalmazza, beleértve a gyorsítótárazást (ReadWrite és ReadOnly) tartalmazó lemezeket, valamint a helyi és a temp lemezeket. Végezetül, a virtuálisgép-hálózat kiépítés minden olyan I/O-szinten zajlik, amelyet a számítási gazdagép az Azure Storage háttérbe küld. Ezzel a rendszerrel a virtuális gépek teljesítménye számos tényezőtől függ, attól, hogy a virtuális gép hogyan használja a helyi SSD-t, a csatlakoztatott lemezek számát, valamint a csatlakoztatott lemezek teljesítményét és gyorsítótárazási típusát.

A korlátozások példái közé a Standard_DS1v1 virtuális gép megakadályozza a P30-lemezek 5 000-es IOPS-potenciáljának megvalósítását, függetlenül attól, hogy gyorsítótárazva van-e vagy sem, az SSD-k és a hálózati szintek korlátai miatt:

![Standard_DS1v1-példa foglalása](media/virtual-machines-managed-disks-overview/example-vm-allocation.png)

Az Azure rangsorolt hálózati csatornát használ a lemezes forgalomhoz, ami elsőbbséget élvez a hálózati forgalom más alacsony prioritásával szemben. Ez segít a lemezeknek a várt teljesítményük fenntartásában hálózati tartalom esetén. Hasonlóképpen, az Azure Storage az erőforrás-tartalmakat és a háttérben lévő egyéb problémákat az automatikus terheléselosztással kezeli. Az Azure Storage a szükséges erőforrásokat foglalja le lemez létrehozásakor, és az erőforrások proaktív és reaktív kiegyensúlyozását alkalmazza a forgalmi szint kezeléséhez. Ez biztosítja, hogy a lemezek továbbra is megtartsák a várt IOPS és teljesítménybeli célokat. A virtuális gépek szintjének és a lemez szintű metrikák segítségével nyomon követheti a teljesítményt, és szükség szerint beállíthatja a riasztásokat.

Tekintse meg a [nagy teljesítményű](../articles/virtual-machines/windows/premium-storage-performance.md) cikk kialakítását ismertető cikket, amelyből MEGISMERHETI a virtuális gépek és lemezek konfigurációinak optimalizálásával kapcsolatos ajánlott eljárásokat, hogy elérhető legyen a kívánt teljesítmény

## <a name="next-steps"></a>További lépések

További információ az Azure-ajánlatok egyes típusairól, amelyek típusa jól illeszkedik az igényeihez, és megismerheti a teljesítményre vonatkozó célokat a lemez típusainak leírásában.
