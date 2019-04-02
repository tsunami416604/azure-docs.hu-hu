---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfd91caf67592b349bd16bab673a3e45397ad282
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807682"
---
## <a name="benefits-of-managed-disks"></a>Felügyelt lemezek előnyei

Vegyük át révén a felügyelt lemezek használatával előnyöket nyújtja.

### <a name="highly-durable-and-available"></a>Tartós és magas rendelkezésre állású

A felügyelt lemezek 99,999 %-os rendelkezésre állást lettek kialakítva. A felügyelt lemezek ennek érdekében az adatokról, ami nagyfokú tartósság három replika biztosításával. Ha egy vagy két replika meghibásodik, a fennmaradó replikák azok feladatátvételével garantálják az adatok megőrzését és a magas fokú hibatűrő képességet. Ez az architektúra segített Azure konzisztens módon biztosít a nagyvállalati szintű tartósságot infrastruktúra szolgáltatás (IaaS) lemezek, az iparágvezető nulla % érvényes évesített hibaaránya.

### <a name="simple-and-scalable-vm-deployment"></a>Egyszerű és méretezhető virtuális gépek üzembe helyezése

Felügyelt lemezeket használ, legfeljebb 50 000 virtuális Gépet hozhat létre **lemezek** egy előfizetésben régiónként típusú, hogy lehetővé teszi több ezer **virtuális gépek** egy előfizetés. Ez a szolgáltatás emellett tovább növeli a méretezhetőségét [a virtual machine scale sets](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) azáltal, hogy legfeljebb 1000 virtuális gép létrehozása méretezési csoportban lévő virtuális gép egy Piactéri rendszerkép használatával.

### <a name="integration-with-availability-sets"></a>Integráció a rendelkezésre állási csoportok

Annak érdekében, hogy a lemezek a rendelkezésre állási csoportokkal integrálva vannak a felügyelt lemezek [virtuális gépet egy rendelkezésre állási csoportban](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) egypontos meghibásodás elkerülése érdekében hibapontok. Lemezek automatikusan különböző tárolóskálázási egységekbe (stampek) kerülnek. Ha egy stamp hardver-vagy szoftverhiba miatt meghiúsul, csak azokat a stampek lévő lemezek VM-példányok sikertelen. Például tegyük fel, hogy öt virtuális gépeken futó alkalmazásokhoz, és a virtuális gépek rendelkezésre állási csoportba. A lemezek a virtuális gépeken nem tárolja az azonos blokkba, ezért ha egyetlen stamphez konfiguráljon leáll, az alkalmazás többi példánya tovább futnak.

### <a name="azure-backup-support"></a>Az Azure Backup-támogatás

Regionális katasztrófák elleni védelem érdekében [Azure Backup](../articles/backup/backup-introduction-to-azure-backup.md) segítségével hozzon létre egy biztonsági mentési feladat idő-alapú biztonsági mentések és a biztonsági másolatok megőrzési házirendeket. Ez lehetővé teszi, hogy könnyen virtuális gépek visszaállítását, majd hajtsa végre. Jelenleg az Azure Backup támogatja a legfeljebb négy tebibyte (TiB) lemezek. További információkért lásd: [Azure Backup segítségével a felügyelt lemezekkel rendelkező virtuális gépek](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

### <a name="granular-access-control"></a>Részletes hozzáférés-vezérlés

Használhat [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](../articles/role-based-access-control/overview.md) felügyelt lemezes konkrét engedély hozzárendelése egy vagy több felhasználó. A felügyelt lemezek fiókszinten elérhető a különböző műveleteket, köztük a olvasási, írási (létrehozási/frissítési), törlési és beolvasása egy [közös hozzáférésű jogosultságkód (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) a lemezhez. Is hozzáférést biztosít egy személy a feladat végrehajtásához szükséges műveleteket. Például ha egy felügyelt lemez másolása egy tárfiókba, aki nem szeretné, is nem kíván hozzáférést biztosítani az exportálási művelet a felügyelt lemez. Hasonlóképpen ha egy személy egy SAS URI-t használja egy felügyelt lemez másolása nem szeretné, lehet váltani, nem engedélyezi, hogy a felügyelt lemez.

## <a name="disk-roles"></a>Lemez szerepkörök

### <a name="data-disks"></a>Adatlemezek

Adatlemez egy felügyelt lemezt, amely az alkalmazás vagy egyéb adatok továbbra is szeretné tárolni a virtuális géphez van csatolva. Adatlemezek SCSI-meghajtók, regisztrálva van, és betűvel, Ön által választott vannak ellátva. Az egyes adatlemezek kapacitása maximum, – 32 767 gibibájtban (GiB). A virtuális gép mérete határozza meg, hány adatlemez csatolható, és a tárolás típusát használhatja a lemezek.

### <a name="os-disks"></a>Operációsrendszer-lemezek

Minden virtuális gép rendelkezik egy csatlakoztatott operációsrendszer-lemez. Hogy az operációsrendszer-lemez egy előre telepített operációs rendszer, amely a virtuális gép létrehozásakor kiválasztott rendelkezik.

Ez a lemez maximális kapacitása, 2048 GIB-ra.

### <a name="temporary-disk"></a>Ideiglenes lemez

Minden virtuális gép egy ideiglenes lemezt, amely nem egy felügyelt lemezt tartalmaz. Az ideiglenes lemez rövid távú tárolást biztosít az alkalmazások és folyamatok, és csak az adatok, például lapozófájlokat tárolására szolgál. Az ideiglenes lemezen lévő adatok elveszhetnek során egy [karbantartási esemény](../articles/virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) esemény, vagy ha Ön [egy virtuális gép ismételt üzembe](../articles/virtual-machines/troubleshooting/redeploy-to-new-node-windows.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json). Során a virtuális gép sikeres standard újraindítása az ideiglenes meghajtón lévő adatok megmaradnak.

## <a name="managed-disk-snapshots"></a>Felügyelt lemez-pillanatképek

Felügyelt lemez-pillanatképből alapértelmezés szerint egy standard szintű felügyelt lemezként tárolt felügyelt lemez csak olvasható teljes másolatát. Pillanatképek készíthető a felügyelt lemezek bármikor időben. Ezek a pillanatképek létezik a forráslemez független, és új felügyelt lemezek létrehozására használható. Azok a felhasznált mérete alapján számlázzuk. Például egy felügyelt lemez pillanatképének létrehozása 64 GB kiosztott kapacitással és 10 GB tényleges felhasznált adatok mérete, a pillanatkép számoljuk fel csak a felhasznált adatok mérete 10 GB.  

A pillanatképek létrehozása felügyelt lemezekkel kapcsolatos további információkért lásd a következőket:

* [Pillanatképekkel Windows felügyelt lemezként tárolt VHD másolatának létrehozása](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Felügyelt lemezként tárolt VHD másolatának létrehozása pillanatképekkel Linux alatt](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)

### <a name="images"></a>Képek

Felügyelt lemezeket is támogatja a felügyelt egyéni lemezképek létrehozásával. Kép a storage-fiókban lévő egyéni VHD-ből vagy közvetlenül egy általánosított (syspreppel előkészített) virtuális Gépet hozhat létre. Ez a folyamat egy egyetlen lemezképének rögzítésére használatos. Ez a rendszerkép tartalmazza a virtuális gép, beleértve az operációs rendszer és a az adatlemezek társított összes felügyelt lemezeket. A felügyelt egyéni lemezképek létrehozása több száz virtuális gépeket kellene az egyéni rendszerkép használatával másolja, vagy minden olyan storage-fiókok kezelése lehetővé teszi.

Lemezképek létrehozásával kapcsolatos információkért tekintse meg a következő cikkeket:

* [Az Azure-beli általánosított virtuális gép felügyelt rendszerképének rögzítése](../articles/virtual-machines/windows/capture-image-resource.md)
* [Linux rendszerű virtuális gép általánosítása és rögzítése Azure CLI használatával](../articles/virtual-machines/linux/capture-image.md)

#### <a name="images-versus-snapshots"></a>Képek és pillanatképek

Képek és a pillanatképek közötti különbségek megértése fontos. A felügyelt lemezekkel rendelkező általánosított virtuális gép, amely fel lettek szabadítva képe is igénybe vehet. Ez a rendszerkép tartalmazza a virtuális Géphez csatlakoztatott lemezeket. Ez a rendszerkép használatával hozzon létre egy virtuális Gépet, és tartalmazza a lemezeket.

Egy pillanatképet a pillanatkép elkészítésének ideje azon a ponton a lemez egy példányát. Ez csak egy lemezt vonatkozik. Ha egy virtuális Gépet, amely rendelkezik egy lemezt (operációsrendszer-lemez), egy pillanatkép vagy a kép azt, és hozzon létre egy virtuális Gépet a pillanatkép vagy a kép.

Pillanatkép tudatosság az kivételével tartalmaz minden olyan lemez nem rendelkezik. Így több lemez, például a csíkozást összehangolását igénylő forgatókönyvekben használandó problémás lehet. A pillanatképek kell tudni koordinálnak egymással, és ez jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések

További információ az egyes lemeztípusok Azure kínál, és melyik típus jó megoldás lehet az igényeinek, a lemez-típusok című cikkben.
