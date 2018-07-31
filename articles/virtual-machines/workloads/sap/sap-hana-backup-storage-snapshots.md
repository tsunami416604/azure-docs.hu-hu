---
title: Az SAP HANA az Azure backup tárolási pillanatképeken alapuló |} A Microsoft Docs
description: Két lehetőség közül választhat jelentős biztonsági mentés SAP Hana Azure-beli virtuális gépeken, ez a cikk ismerteti az SAP HANA biztonsági mentés tárolási pillanatképeken alapuló
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: c5066d23705ca84febaa0ba527a01259134146c0
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358981"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>Tárolási pillanatképeken alapuló biztonsági mentés SAP HANA-hoz

## <a name="introduction"></a>Bevezetés

Ez egy háromrészes sorozat kapcsolódó cikkekben az SAP HANA biztonsági másolat része. [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](sap-hana-backup-guide.md) áttekintése és információt nyújt az első lépések, és [SAP HANA az Azure Backup a fájlok szintjére](sap-hana-backup-file-level.md) magában foglalja a biztonsági mentési fájl alapú lehetőséget.

Egy virtuális gép biztonsági mentési funkcióval egy példányban – teljes körű bemutatót rendszer használatakor kell figyelembe venni a HANA biztonsági mentés az operációs rendszer szintjén ahelyett, hogy egy virtuális gép biztonsági mentéshez. A másik lehetőség az Azure blob-pillanatképek másolatokat meg az egyes virtuális lemezek, virtuális gép csatlakozik, és mindig a HANA-adatok fájlokat. Azonban a kritikus pontok létrehozása egy virtuális gép biztonsági mentési vagy a lemez pillanatképét, amíg a rendszer működik és fut alkalmazás konzisztencia. Lásd: _SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló_ a kapcsolódó cikkben [biztonsági mentési útmutató az SAP Hana az Azure Virtual Machinesben](sap-hana-backup-guide.md). Az SAP HANA rendelkezik egy szolgáltatás, amely támogatja az ilyen típusú storage-pillanatképeket.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Alkalmazáskonzisztens biztonsági másolatok központi részeként az SAP HANA-pillanatképek

SAP HANA, amely támogatja a storage-pillanatkép készítése a funkció szerepel. Nincs korlátozás egy tároló-rendszerekkel. Forgatókönyvek Sign egynél több bérlőhöz az SAP HANA MCS nem támogatják az SAP HANA-adatbázis-pillanatkép az ilyen típusú (lásd: [hozzon létre egy Storage-pillanatkép (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

Ez a következőképpen működik:

- Az SAP HANA pillanatképe kezdeményezi egy storage-pillanatkép előkészítése
- Futtassa a storage-pillanatkép (az Azure blob pillanatkép, például)
- Erősítse meg az SAP HANA pillanatképe

![Ezen a képernyőfelvételen látható, hogy egy SAP HANA-adatok pillanatkép hozható létre egy SQL-utasítás használatával](media/sap-hana-backup-storage-snapshots/image011.png)

Ezen a képernyőfelvételen látható, hogy egy SAP HANA-adatok pillanatkép hozható létre egy SQL-utasítás segítségével.

![A pillanatkép ezután is megjelenik a biztonságimásolat-katalógus az SAP HANA Studio](media/sap-hana-backup-storage-snapshots/image012.png)

A pillanatkép ezután is megjelenik a biztonságimásolat-katalógus az SAP HANA Studio.

![A lemezen a pillanatkép megjelenik-e a SAP HANA adatkönyvtárat](media/sap-hana-backup-storage-snapshots/image013.png)

A lemezen a pillanatkép megjelenik-e a SAP HANA adatkönyvtárat.

Egy rendelkezik, győződjön meg arról, hogy a fájlrendszer-konzisztencia is garantáltan futtatása a storage-pillanatkép, amíg a pillanatkép-előkészítés módja van az SAP HANA előtt. Lásd: _SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló_ a kapcsolódó cikkben [biztonsági mentési útmutató az SAP Hana az Azure Virtual Machinesben](sap-hana-backup-guide.md).

Miután megtörtént a storage-pillanatkép, rendkívül fontos erősítse meg az SAP HANA pillanatképe. Nincs a megfelelő SQL-utasítás futtatása: biztonsági MENTÉSI adatok Bezárás PILLANATKÉP (lásd: [biztonsági MENTÉSI adatok Bezárás SNAPSHOT utasítás (biztonsági mentés és helyreállítás)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Erősítse meg a HANA pillanatképe. Miatt &quot;másolási Módosításkori,&quot; SAP HANA szükség lehet további lemezterületet a pillanatkép-előkészítése módban, és nem kezdődhet új biztonsági mentések mindaddig, amíg az SAP HANA pillanatképe ellenőrzése.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA virtuális gép biztonsági mentése Azure Backup szolgáltatáson keresztül

A Backup szolgáltatás ügynöke, az Azure Backup szolgáltatás nem érhető el Linux rendszerű virtuális gépekhez. Továbbá Linux nincs hasonló funkciókat, a Windows VSS-rendelkezik  Győződjön meg arról, hogy az Azure backup a fájl vagy könyvtár szintjén, az egyik volna az SAP HANA biztonsági mentési fájlok másolása egy Windows virtuális Gépre, majd a Backup szolgáltatás ügynöke. 

Ellenkező esetben csak a teljes Linux rendszerű virtuális gép biztonsági mentésre lehetőség az Azure Backup szolgáltatás használatával. Lásd: [az Azure Backup szolgáltatásainak áttekintése](../../../backup/backup-introduction-to-azure-backup.md) további.

Az Azure Backup szolgáltatás biztonsági mentése és visszaállítása egy virtuális gép lehetőséget kínál. Ezt a szolgáltatást és annak működéséről további információt a cikkben található [az Azure-beli virtuális gép biztonsági infrastruktúrájának megtervezése](../../../backup/backup-azure-vms-introduction.md).

Két szempontot fontos, hogy a cikk megfelelően:

_&quot;Linux rendszerű virtuális gépek csak a fájlkonzisztens biztonsági mentés lehetséges, mivel, Linux nem rendelkezik egy megfelelő platform VSS&quot;_

_&quot;Alkalmazások kell megvalósítani a saját &quot;javítása&quot; a visszaállított adatok mechanizmust.&quot;_

Ezért egy is ellenőrizze, hogy az SAP HANA lemezen konzisztens állapotban van, amikor elindul a biztonsági mentés. Lásd: _SAP HANA-pillanatképek_ a dokumentum a korábban ismertetett. Azonban van egy potenciális problémát, amikor az SAP HANA marad a pillanatkép-előkészítés módja. Lásd: [hozzon létre egy Storage-pillanatkép (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) további információt.

A cikk állapotok:

_&quot;Erősen ajánlott, győződjön meg róla, vagy egy tároló pillanatképe minél hamarabb elveti a létrehozásuk után. Amíg folyamatban van, a storage-pillanatképet készített, vagy létrehozott, a pillanatkép-vonatkozó adatok lefagytak. A pillanatkép-vonatkozó adatok kifejlesztésén is marad, miközben továbbra is módosíthatók az adatbázisban. A módosítások nem okoz a módosítandó kifejlesztésén pillanatkép-vonatkozó adatokat. Ehelyett a módosításokat ír pozíciók az adatterületen, amelyek külön, a storage-pillanatkép. Módosítások is írja a naplót. Azonban minél hosszabb a pillanatkép-vonatkozó adatok másolatok kifejlesztésén, annál adatmennyiség növekedéséhez.&quot;_

Azure Virtuálisgép-bővítmények használatával a fájlrendszer-konzisztencia az Azure Backup gondoskodik. Ezek a bővítmények nem érhető el önálló, és csak az Azure Backup szolgáltatással együtt működik. Azonban, akkor továbbra is szükséges szkriptek létrehozása és törlése alkalmazás konzisztencia biztosításához egy SAP HANA pillanatképe biztosít.

Az Azure Backup négy fő fázisból áll:

- Hajtsa végre készítse elő a parancsfájl - parancsfájl létre kell hoznia egy SAP HANA pillanatképe
- Pillanatkép készítése
- Pillanatkép utáni szkript végrehajtása – az SAP HANA, az előkészítés szkript által létrehozott törölnie kell a parancsfájl
- Adatok átvitele a tárba

Hol másolja ezeket a parancsfájlokat a részleteket, és az Azure Backup működéséről pontosan tekintse meg a következő cikkeket:

- [Virtuális gép biztonsági infrastruktúrájának megtervezése az Azure-ban](https://docs.microsoft.com/en-us/azure/backup/backup-azure-vms-introduction)
- [Az Azure Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentés](https://docs.microsoft.com/en-us/azure/backup/backup-azure-linux-app-consistent)



Ezen a ponton az időben, a Microsoft nem tett közzé az SAP Hana-hoz és a pillanatkép utáni parancsfájlok előkészítése. Ügyfél vagy a rendszer integráló kell ezeket a szkripteket létrehozni és konfigurálni az eljárást a fentiekben említett dokumentáció alapján.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Alkalmazás virtuális gépek elleni alkalmazáskonzisztens biztonsági másolat visszaállítása
A cikk ismerteti a visszaállítási folyamat, az alkalmazás konzisztens biztonsági mentését az Azure backup által készített [fájlok helyreállítása Azure virtuális gépek biztonsági mentésének](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> A cikk [fájlok helyreállítása Azure virtuális gépek biztonsági mentésének](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) azon kivételek listáját, és paritásos lemezkészletek használatakor felsorolt lépéseket. A csíkozott lemez amelyek valószínűleg a normál virtuális gép konfigurációjához az SAP Hana-hoz. Ezért elengedhetetlen, olvassa el a cikket, és az ezekben az esetekben a cikkben felsorolt a visszaállítási folyamat teszteléséhez. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA licenckulcsot, és a virtuális gép visszaállítása az Azure Backup szolgáltatáson keresztül

Az Azure Backup szolgáltatás célja egy új virtuális gép létrehozása a visszaállítás során. Most ehhez terv van egy &quot;helyben&quot; egy meglévő Azure virtuális gép visszaállítása.

![Ezen az ábrán a helyreállítás az Azure szolgáltatás az Azure Portalon](media/sap-hana-backup-storage-snapshots/image019.png)

Ezen az ábrán a helyreállítás az Azure szolgáltatás az Azure Portalon. Egyet választhat egy virtuális gép létrehozása a visszaállítás során, vagy a lemezek visszaállítását. A lemezek visszaállítását, követően, hozzon létre egy új virtuális gép felett, továbbra is szükség. Minden alkalommal, amikor új virtuális gép jön létre az Azure-ban az egyedi változik-e a virtuális gép azonosítója (lásd: [elérése és használata Azure virtuális gép egyedi Azonosítóját](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Ez az ábra bemutatja az Azure virtuális gép egyedi azonosítója előtt és után a visszaállítás Azure Backup szolgáltatáson keresztül](media/sap-hana-backup-storage-snapshots/image020.png)

Ez az ábra bemutatja az Azure virtuális gép egyedi azonosítója, előtt és után a visszaállítás Azure Backup szolgáltatáson keresztül. Az SAP hardverkulcsra váltásával, amely SAP szoftverlicencelési használható, használja az egyedi azonosítót a virtuális gép. Ennek következményeképpen új SAP licencet telepítve van egy virtuális gép visszaállítása után.

Egy új Azure Backup szolgáltatás biztonsági mentési útmutató létrehozása során bemutatott jelenleg előzetes módban. Egy fájl a virtuális gép pillanatképét, amelyet az volt a virtuális gép biztonsági mentési alapján visszaállítás lehetővé teszi. Ez kiküszöböli, hogy új virtuális gép üzembe helyezése, és ezért a virtuális gép egyedi Azonosítóját változatlan marad, és nincs új SAP HANA-licenc kulcs nem szükséges. Ez a szolgáltatás további dokumentációiért biztosítjuk, miután azt teljes körűen tesztelve.

Az Azure Backup végül engedélyezése az egyes Azure virtuális lemezeket, és fájlokat és könyvtárakat az a virtuális gép biztonsági mentése. Az Azure Backup egyik jelentős előnye az összes biztonsági mentés, az ügyfél mentése nem kell ezt a felügyeleti. A visszaállítás akkor van szükség, ha Azure Backup fogja kiválasztani a helyes biztonsági mentés használandó.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>SAP HANA virtuális gép biztonsági másolat a lemezek manuális pillanatkép-n keresztül

Az Azure Backup szolgáltatás helyett egy beállíthat egy egyedi biztonsági mentési megoldás blobpillanatképeket manuálisan a Powershellen keresztül Azure VHD-k létrehozásával. Lásd: [tárolóblob-pillanatképek használata a PowerShell-lel](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) lépések leírását.

Ez nagyobb rugalmasságot nyújt, de nem oldja meg a problémákat a jelen dokumentum korábbi ismertetése:

- Ön továbbra is biztosítania kell, hogy az SAP HANA konzisztens állapotban hozzon létre egy SAP HANA pillanatképe
- Az operációsrendszer-lemez nem írható felül, akkor is, ha a virtuális gép fel van szabadítva, arról, hogy létezik-e a címbérlet hiba miatt. Ez csak akkor működik a virtuális gép, amely egy új, egyedi virtuális gép azonosítója és kell telepítenie az új SAP licencet vezetne törlését követően.

![Lehetséges, csak az adatlemezeket az Azure virtuális gépek visszaállítása](media/sap-hana-backup-storage-snapshots/image021.png)

Lemezek csak az adatok egy Azure virtuális gépek, az első virtuális gép egy új egyedi azonosító a probléma elkerülésére a lehető, és ezért érvénytelenítette az SAP-licenc:

- A tesztelési virtuális Géphez csatlakoztatott két Azure-adatlemezek is, és szoftveres RAID platformképességeivel lett definiálva 
- SAP HANA-pillanatkép funkció, hogy az SAP HANA konzisztens állapotban volt megerősítették
- Befagyasztani a fájlrendszer (lásd: _SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló_ a kapcsolódó cikkben [biztonsági mentési útmutató az SAP Hana az Azure Virtual Machinesben](sap-hana-backup-guide.md))
- Mindkét adatlemezek származnak tárolóblob-pillanatképek
- Fájlrendszer rögzítésének feloldása
- SAP HANA-pillanatkép megerősítése
- Az adatlemezek visszaállításához a virtuális gép le lett állítva, és a két lemez leválasztása
- Miután a lemezek leválasztása, azok felül lett írva a korábbi blob-pillanatképekkel
- Ezután a visszaállított virtuális lemezek is újra a virtuális Géphez csatolt
- Után a virtuális gép indításával, minden, a szoftverfrissítési RAID működtek, és vissza lett beállítva a blob-pillanatkép idő
- HANA vissza lett-e beállítva, a HANA pillanatképe

Ha volt lehetséges a blobpillanatképeket előtt állítsa le az SAP HANA, az eljárás kevésbé összetett lehet. Ebben az esetben egy sikerült kihagyhatja a HANA pillanatképe és, semmi más történik a rendszeren, ha ki is hagyhatja a fájl rendszer rögzíteni. Bonyolultság ebbe a képbe származnak, amikor szükséges közben mindent online állapotban, a pillanatképek végezhet. Lásd: _SAP HANA adatkonzisztencia, amikor a pillanatképeket tároló_ a kapcsolódó cikkben [biztonsági mentési útmutató az SAP Hana az Azure Virtual Machinesben](sap-hana-backup-guide.md).

## <a name="next-steps"></a>További lépések
* [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](sap-hana-backup-guide.md) áttekintése és első lépések információkat biztosít.
* [SAP HANA biztonsági mentés alapján a fájlok szintjére](sap-hana-backup-file-level.md) magában foglalja a biztonsági mentési fájl alapú lehetőséget.
* Magas rendelkezésre állást és az Azure-ban (nagyméretű példányok) SAP Hana vész-helyreállítási terv létrehozásához, lásd: [SAP HANA (nagyméretű példányok) magas rendelkezésre állás és vészhelyreállítás recovery az Azure-ban](hana-overview-high-availability-disaster-recovery.md).
