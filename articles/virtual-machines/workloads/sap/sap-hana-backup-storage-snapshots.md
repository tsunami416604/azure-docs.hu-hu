---
title: SAP HANA Azure Backup tárolási Pillanatképek alapján | Microsoft Docs
description: Az Azure Virtual Machines szolgáltatásban SAP HANA két fő biztonsági mentési lehetőség áll rendelkezésre, ez a cikk a tárolási Pillanatképek alapján történő SAP HANA biztonsági mentést ismerteti
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: c977bc7db5608e5718e98a26ed594e5ebf2be998
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617414"
---
# <a name="sap-hana-backup-based-on-storage-snapshots"></a>SAP HANA biztonsági mentés tárolási Pillanatképek alapján

## <a name="introduction"></a>Introduction (Bevezetés)

Ez egy, a SAP HANA biztonsági mentéssel kapcsolatos cikkek három részből álló sorozatának részét képezi. Az [Azure Virtual Machines SAP HANA biztonsági mentési útmutatója](sap-hana-backup-guide.md) áttekintést nyújt az első lépésekről, és a fájlok [szintjén SAP HANA Azure Backup](sap-hana-backup-file-level.md) a fájl alapú biztonsági mentési lehetőségre is kiterjed.

Ha egy virtuális gép biztonsági mentési funkcióját használja egy egypéldányos, all-in-One bemutató rendszerhez, az egyiknek érdemes megfontolnia a virtuális gépek biztonsági mentését a HANA biztonsági mentések operációs rendszer szintjén történő kezelése helyett. Egy másik lehetőség, hogy az Azure Blob-pillanatképeket hozzon létre egy virtuális géphez csatolt és a HANA-adatfájlokat tároló egyedi virtuális lemezek másolatait. A kritikus pont azonban az alkalmazások konzisztenciája a virtuális gépek biztonsági mentésének vagy a lemez pillanatképének létrehozásakor a rendszer működése közben. A [SAP HANA Virtual Machines Azure-beli biztonsági mentési útmutatójában](sap-hana-backup-guide.md)tekintse meg az _SAP HANA adatkonzisztencia a tárolási Pillanatképek_ készítésekor című cikket. SAP HANA tartalmaz egy funkciót, amely támogatja az ilyen típusú tárolási pillanatképeket.

## <a name="sap-hana-snapshots-as-central-part-of-application-consistent-backups"></a>Pillanatképek SAP HANA az alkalmazás konzisztens biztonsági mentéseinak központi részeként

A SAP HANA olyan funkciója van, amely támogatja a tárolási Pillanatképek készítését. Az egytárolós rendszerekre korlátozás vonatkozik. Az olyan forgatókönyvek, SAP HANA MCS több Bérlővel való használata nem támogatja ezt a fajta SAP HANA adatbázis-pillanatképet (lásd: [tárolási pillanatkép létrehozása (SAP HANA Studio)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm)).

A következőképpen működik:

- Felkészülés a tárolási pillanatképre a SAP HANA pillanatképének elindításával
- A tárolási pillanatkép futtatása (például az Azure Blob pillanatképe)
- SAP HANA pillanatkép megerősítése

![Ez a képernyőkép azt mutatja, hogy egy SAP HANA adatpillanatkép hozható létre egy SQL-utasítás segítségével.](media/sap-hana-backup-storage-snapshots/image011.png)

Ez a képernyőkép azt mutatja, hogy egy SAP HANA adatpillanatkép hozható létre egy SQL-utasítás segítségével.

![A pillanatkép ezután a SAP HANA Studio biztonsági mentési katalógusában is megjelenik](media/sap-hana-backup-storage-snapshots/image012.png)

A pillanatkép ekkor is megjelenik a SAP HANA Studio biztonsági mentési katalógusában.

![A lemezen a pillanatkép megjelenik a SAP HANA adatkönyvtárban.](media/sap-hana-backup-storage-snapshots/image013.png)

A lemezen a pillanatkép megjelenik a SAP HANA adatkönyvtárban.

Az egyiknek biztosítania kell, hogy a fájlrendszer konzisztenciája is garantált legyen a tárolási pillanatkép futtatása előtt, miközben SAP HANA a pillanatkép-előkészítési módban van. A [SAP HANA Virtual Machines Azure-beli biztonsági mentési útmutatójában](sap-hana-backup-guide.md)tekintse meg az _SAP HANA adatkonzisztencia a tárolási Pillanatképek_ készítésekor című cikket.

A tárolási pillanatkép elvégzése után elengedhetetlen a SAP HANA pillanatkép megerősítése. Van egy megfelelő SQL-utasítás a futtatásához: biztonsági másolati adatok BEZÁRÁSának PILLANATKÉPe (lásd: [biztonsági mentési adatok bezárásának pillanatkép-utasítása (biztonsági mentés és helyreállítás)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/9739966f7f4bd5818769ad4ce6a7f8/content.htm)).

> [!IMPORTANT]
> Erősítse meg a HANA-pillanatképet. A &quot;írásos másolás miatt előfordulhat, hogy&quot; SAP HANA további lemezterületre van szükség a pillanatkép-előkészítési módban, és nem lehet új biztonsági másolatokat elindítani, amíg meg nem történik a SAP HANA pillanatképének megerősítése.

## <a name="hana-vm-backup-via-azure-backup-service"></a>HANA virtuális gép biztonsági mentése Azure Backup szolgáltatáson keresztül

A Azure Backup szolgáltatás biztonsági mentési ügynöke Linux rendszerű virtuális gépek esetén nem érhető el. Emellett a Linux nem rendelkezik hasonló funkciókkal, mint a Windows a VSS-vel.  Ha az Azure Backup szolgáltatást szeretné használni a fájl/könyvtár szintjén, az egyik a Windows rendszerű virtuális gépekre másol SAP HANA biztonsági másolatokat, majd a biztonsági mentési ügynököt használja. 

Ellenkező esetben csak a teljes linuxos virtuális gép biztonsági mentése lehetséges a Azure Backup szolgáltatáson keresztül. További információkért tekintse meg [a Azure Backup funkcióinak áttekintését](../../../backup/backup-introduction-to-azure-backup.md) .

A Azure Backup szolgáltatás lehetőséget kínál a virtuális gép biztonsági mentésére és visszaállítására. További információ erről a szolgáltatásról és annak működéséről a [virtuális gép biztonsági mentési infrastruktúrájának megtervezése az Azure-ban](../../../backup/backup-azure-vms-introduction.md)című cikkben található.

A cikk alapján két fontos szempontot kell figyelembe venni:

_a Linux rendszerű virtuális gépek &quot;csak a fájlokkal konzisztens biztonsági mentések lehetségesek, mivel a Linux nem rendelkezik megfelelő platformmal a VSS-hez.&quot;_

_&quot;alkalmazásoknak a visszaállított adat&quot;i&quot; mechanizmust kell alkalmazniuk.&quot;_

Ezért az egyiknek gondoskodnia kell arról, hogy SAP HANA konzisztens állapotban legyen a lemezen a biztonsági mentés indításakor. Lásd a dokumentumban korábban ismertetett _SAP HANA-pillanatképeket_ . Ha azonban SAP HANA marad ebben a pillanatkép-előkészítési módban, lehetséges probléma van. További információt a [tárolási pillanatkép (SAP HANA Studio) létrehozása](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a0/3f8f08501e44d89115db3c5aa08e3f/content.htm) című témakörben talál.

A cikk állapota:

_&quot;javasoljuk, hogy a létrehozása után a lehető leghamarabb erősítse meg vagy hagyjon le egy tárolási pillanatképet. Amíg a rendszer előkészíti vagy létrehozza a tárolási pillanatképet, a pillanatképhez kapcsolódó adatok zárolva vannak. Amíg a pillanatképhez kapcsolódó adatok megmaradnak, a változtatások továbbra is elérhetők lesznek az adatbázisban. Ezek a változások nem eredményezik a rögzített pillanatkép-releváns adatok módosítását. Ehelyett a módosításokat a rendszer az adatterületen lévő, a tárolási pillanatképtől eltérő pozíciókba írja. A módosításokat a rendszer a naplóba is írja. A pillanatképhez kapcsolódó adatokat azonban továbbra is zárolják, annál nagyobb mennyiségű adat növekszik.&quot;_

Azure Backup az Azure virtuálisgép-bővítmények használatával gondoskodik a fájlrendszer konzisztenciájáról. Ezek a bővítmények nem érhetők el különállóként, és csak Azure Backup szolgáltatással együtt működnek. Azonban továbbra is követelmény, hogy parancsfájlokat hozzon létre és töröljön egy SAP HANA pillanatképet az alkalmazások konzisztenciájának garantálása érdekében.

Azure Backup négy fő fázisa van:

- Előkészítési parancsfájl végrehajtása – a parancsfájlnak SAP HANA pillanatképet kell létrehoznia
- Pillanatkép készítése
- Pillanatkép utáni parancsfájl végrehajtása – a parancsfájlnak törölnie kell az előkészítési parancsfájl által létrehozott SAP HANA
- Adatok átvitele a tárba

A parancsfájlok másolásának és a Azure Backup pontos működésének részleteiről a következő cikkekben tájékozódhat:

- [Virtuális gép biztonsági infrastruktúrájának megtervezése az Azure-ban](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)
- [Az Azure Linux rendszerű virtuális gépek alkalmazásának konzisztens biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)



Ebben az időpontban a Microsoft még nem tette közzé a parancsfájlok készítését és a pillanatképek utáni parancsfájlokat a SAP HANA. Az ügyfélnek vagy a rendszerintegrátornak létre kell hoznia ezeket a parancsfájlokat, és konfigurálnia kell az eljárást a fent hivatkozott dokumentáció alapján.


## <a name="restore-from-application-consistent-backup-against-a-vm"></a>Visszaállítás az alkalmazások konzisztens biztonsági másolatából egy virtuális gépre
Az Azure Backup által készített alkalmazás-konzisztens biztonsági másolat visszaállítási folyamata a következő cikkben található: [fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm). 

> [!IMPORTANT]
> Az Azure-beli [virtuális gépek biztonsági mentésének fájljainak helyreállítása](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm) című cikkben a lemezes szalagok használatakor felsorolt kivételek és lépések listája látható. A csíkozott lemezek valószínűleg a SAP HANA normál virtuálisgép-konfigurációja. Ezért fontos, hogy olvassa el a cikket, és tesztelje a visszaállítási folyamatot a cikkben felsorolt esetekben. 



## <a name="hana-license-key-and-vm-restore-via-azure-backup-service"></a>HANA-licenckulcs és virtuális gép visszaállítása Azure Backup szolgáltatáson keresztül

A Azure Backup szolgáltatás úgy lett kialakítva, hogy a visszaállítás során hozzon létre egy új virtuális gépet. Egy meglévő Azure-beli virtuális gép visszaállításának &quot;helyben&quot; helyreállítására nincs terv.

![Ez az ábra az Azure-szolgáltatás visszaállítási lehetőségét mutatja Azure Portal](media/sap-hana-backup-storage-snapshots/image019.png)

Ez az ábra a Azure Portal Azure-szolgáltatás visszaállítási lehetőségét mutatja. A virtuális gép létrehozása és a lemezek visszaállítása között választhat. A lemezek visszaállítása után továbbra is létre kell hoznia egy új virtuális gépet. Amikor új virtuális gépet hoz létre az Azure-ban, az egyedi virtuálisgép-azonosító módosul (lásd: az [Azure virtuális gép egyedi azonosítójának elérése és használata](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)).

![Ez az ábra az Azure virtuális gép egyedi AZONOSÍTÓját mutatja a visszaállítás előtt és után Azure Backup szolgáltatáson keresztül](media/sap-hana-backup-storage-snapshots/image020.png)

Ez az ábra az Azure virtuális gép egyedi AZONOSÍTÓját mutatja a visszaállítás előtt és után Azure Backup szolgáltatáson keresztül. Az SAP licenceléshez használt SAP-hardver kulcsa ezt az egyedi virtuálisgép-azonosítót használja. Ennek következményeként új SAP-licencet kell telepíteni a virtuális gép visszaállítása után.

A biztonsági mentési útmutató létrehozása során új Azure Backup funkció jelenik meg előzetes módban. Lehetővé teszi a fájlok szintjének visszaállítását a virtuális gép biztonsági mentéséhez készült pillanatkép alapján. Ezzel elkerülhető az új virtuális gép üzembe helyezésének szükségessége, ezért az egyedi virtuálisgép-azonosító ugyanaz marad, és nincs szükség új SAP HANA licenckulcs megszerzésére. A szolgáltatással kapcsolatos további dokumentációt a teljes körű tesztelés után kapja meg.

Azure Backup végül lehetővé teszi az egyes Azure-beli virtuális lemezek, valamint a virtuális gépeken belüli fájlok és könyvtárak biztonsági mentését. A Azure Backup egyik legfőbb előnye, hogy az összes biztonsági mentést felhasználja, így az ügyfélnek kell elvégeznie. Ha a visszaállítás szükséges lesz, Azure Backup kiválasztja a megfelelő biztonsági mentést.

## <a name="sap-hana-vm-backup-via-manual-disk-snapshot"></a>VIRTUÁLIS gépek biztonsági mentésének SAP HANA manuális lemezes pillanatkép használatával

A Azure Backup szolgáltatás használata helyett egy egyéni biztonsági mentési megoldás is konfigurálható, ha manuálisan hozza létre az Azure VHD-k blob-pillanatképeit a PowerShell segítségével. Lásd: a [blob-Pillanatképek használata a PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/) -lel a lépések leírásához.

Nagyobb rugalmasságot biztosít, de nem oldja meg a dokumentum korábbi részében ismertetett problémákat:

- Továbbra is meg kell győződnie arról, hogy a SAP HANA konzisztens állapotban van SAP HANA pillanatkép létrehozásával
- Az operációsrendszer-lemez nem írható felül, még akkor is, ha a virtuális gép fel van foglalva egy olyan hiba miatt, amely egy bérlet létezését jelzi. Ez csak a virtuális gép törlését követően működik, ami egy új, egyedi virtuálisgép-AZONOSÍTÓhoz vezetne, és új SAP-licencet kell telepítenie.

![Csak egy Azure-beli virtuális gép adatlemezeit lehet visszaállítani](media/sap-hana-backup-storage-snapshots/image021.png)

Csak az Azure-beli virtuális gépek adatlemezei állíthatók vissza, így elkerülhető az új egyedi virtuálisgép-azonosító beszerzésének problémája, és ezért érvénytelenítve lett az SAP-licenc:

- A teszthez két Azure-adatlemez lett csatolva egy virtuális géphez, és a szoftveres RAID-t a rendszer ezen felül definiálta 
- Megerősítettük, hogy SAP HANA konzisztens állapotban volt SAP HANA pillanatkép-szolgáltatással
- A fájlrendszer befagyasztása (lásd: _SAP HANA adatkonzisztencia a tárolási Pillanatképek_ készítéséhez az Azure-beli [SAP HANA vonatkozó biztonsági mentési útmutatóban Virtual Machines](sap-hana-backup-guide.md))
- A blob-Pillanatképek mindkét adatlemezből származnak.
- Fájlrendszer feloldása
- SAP HANA pillanatkép megerősítése
- Az adatlemezek visszaállításához a virtuális gép leállt, és mindkét lemez leválasztva
- A lemezek leválasztása után felülírták őket a korábbi blob-pillanatképekkel
- Ezután a visszaállított virtuális lemezeket ismét csatolták a virtuális GÉPHEZ
- A virtuális gép elindítása után minden a szoftveres RAID jól működött, és vissza lett állítva a blob pillanatképének időpontjára
- HANA a HANA-pillanatképre lett beállítva

Ha a blob-Pillanatképek előtt SAP HANA leállítani, az eljárás kevésbé összetett. Ebben az esetben az egyik kihagyhatja a HANA-pillanatképet, és ha semmi más nem történik a rendszeren, hagyja ki a fájlrendszert is. A hozzáadott komplexitás a képbe kerül, amikor a pillanatképek elvégzése minden online állapotban van. A [SAP HANA Virtual Machines Azure-beli biztonsági mentési útmutatójában](sap-hana-backup-guide.md)tekintse meg az _SAP HANA adatkonzisztencia a tárolási Pillanatképek_ készítésekor című cikket.

## <a name="next-steps"></a>Következő lépések
* [Az Azure Virtual Machines SAP HANA biztonsági mentési útmutatója](sap-hana-backup-guide.md) áttekintést nyújt az első lépésekről.
* [SAP HANA a biztonsági mentés fájl szintjén](sap-hana-backup-file-level.md) a fájl alapú biztonsági mentés lehetőségre.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
