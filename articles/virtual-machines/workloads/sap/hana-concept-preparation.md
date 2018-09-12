---
title: Vész-helyreállítási alapelvek és az SAP HANA az Azure-ban (nagyméretű példányok) előkészítése |} A Microsoft Docs
description: Vész-helyreállítási alapelvek és az SAP HANA az Azure-ban (nagyméretű példányok) előkészítése
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff214460d919eff5c3c1a2e608958673867ddc55
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392243"
---
# <a name="disaster-recovery-principles"></a>Vész-helyreállítási alapelvek

Nagyméretű HANA-példányok ajánlat egy vészhelyreállítási funkciók nagyméretű HANA-példány bélyegekre különböző Azure-régiók között. Például ha nagyméretű HANA-példányt az USA nyugati régiójában, az Azure egységeket telepít, használhatja a nagyméretű HANA-példány egységek USA keleti, vész-helyreállítási egység. Ahogy korábban említettük, vész-helyreállítási nem történik meg automatikusan, mert a szükséges, hogy a DR régióban egy másik nagyméretű HANA-példány egység kell fizetnie. A vészhelyreállítás telepítője vertikális valamint horizontális felskálázás mátrixok esetében működik. 

A forgatókönyvekben, amennyiben a telepített ügyfelek használatával az egység a Vészhelyreállítás régióban található telepített HANA-példány nem éles rendszerek futtatása. A nagyméretű HANA-példány egységet kell lennie a ugyanazon termékváltozat, a Termékváltozat éles üzemi célokat szolgál. Az alábbi képen látható, hogy milyen a lemezkonfigurációt a kiszolgáló egységet az éles Azure-régió között, és a vészhelyreállítási régióban néz ki:

![DR-telepítés konfigurációs lemez szempontból](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Az áttekintése ábrán látható, akkor egy második együttesét lemezkötetek order kell. A célként megadott kötetek mérete megegyezik az éles üzemelő példányok a vész-helyreállítási egységekben éles kötetek. Ezek a lemez-kötetek társítva a nagyméretű HANA-példányt kiszolgáló egységet a vész-helyreállítási helyként. A következő kötetek éles régióban lesznek replikálva a DR-helyre:

- / hana/adatok
- / hana/logbackups 
- /Hana/Shared (a/usr/sap tartalmazza)

A /hana/log kötet nem replikálódnak, mivel az SAP HANA-tranzakciónapló nincs szükség a módon, amely ezekről a kötetekről a visszaállítás történik. 

A vészhelyreállítási funkciók alapjául szolgáló érhető el, a storage-replikáció funkciót a nagyméretű HANA-példány infrastruktúra által kínált. A használt tárolási funkciókat nem állandó adatfolyam bekövetkező változások replikálása aszinkron módon, a tárolási kötet változások történhetnek. Ehelyett érdemes olyan mechanizmus, amely a rendszeres időközönként pillanatképek a kötetek létrehozása a tény támaszkodik. Között egy már replikált pillanatkép és a egy új pillanatkép, amely még nem replikálta a különbözeti cél köteteket, majd átkerülnek a vész-helyreállítási helyként.  Ezek a pillanatképek a köteteken tárolja, és katasztrófa utáni helyreállítás feladatátvétel esetén van szükség, azokon a köteteken vissza kell állítani.  

A teljes kötet első adatátvitelt kell lennie, mielőtt adatok mennyisége kisebb, mint az-pillanatképek közötti eltéréseket válik. Ennek eredményeképpen a a kötetek a DR hely a kötet-pillanatképek végrehajtani az élesben használt helyet a egyes tartalmaznak. Végül a DR rendszer használatával elveszett adatok helyreállításához az éles rendszer visszaállítása egy korábbi állapotba beolvasása.

Egy több független SAP HANA-példányok egy nagyméretű HANA-példány egységen MCOD üzembe helyezés esetén várható, hogy minden SAP HANA-példányok kihozhatják a tároló replikálásának a DR oldalra.

Azokban az esetekben, ahol HANA Rendszerreplikáció használata magas rendelkezésre állású funkcióinak az éles webhelyét, és storage-alapú replikálás használata a DR-hely mind a csomópontok elsődleges helyről a DR-példányhoz a kötetek lesznek replikálva. Kiegészítő tárolás (azonos méretű kezdődően az elsődleges csomópont) DR hely az elsődleges és másodlagos a Vészhelyreállítási a replikáció megfelelően meg kell vásárolnia. 



>[!NOTE]
>A nagyméretű HANA-példány storage replikációs funkció tükrözés és a pillanatképek tárolási replikálásához. Ha megfelelésről a pillanatképek tárolási nem hajtható végre a [biztonsági mentési és visszaállítási](#backup-and-restore) szakaszban Ez a cikk nem lehetnek a vész-helyreállítási webhelyként bármely replikáció. Storage-pillanatkép-végrehajtás feltétele a storage-bA a vész-helyreállítási helyként.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>A vész-helyreállítási helyzetekre előkészítése
Ebben a forgatókönyvben egy éles rendszer HANA nagyméretű példányok az Azure-régió, éles környezetben futó rendelkezik. A következő lépések feltételezzük, hogy a biztonsági AZONOSÍTÓJÁT, a HANA system "PRD", és, hogy rendelkezik-e a HANA nagyméretű példányok az DR Azure régióban nem éles a rendszert. Az utóbbi tegyük fel, hogy a biztonsági azonosító-e a "Teszt". Ez a konfiguráció az alábbi képen látható:

![DR-telepítés kezdete](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Ha a kiszolgálópéldány nem már megrendelt együtt a további tárolási kötet, SAP HANA az Azure Service Management rendeli a további kötetek készlete, a nagyméretű HANA-példány egységet, amelyen a teszt futtatásakor az éles replika célként HANA-példány. Erre a célra meg kell adnia a biztonsági AZONOSÍTÓT az éles HANA-példány. SAP HANA az Azure Service Management megerősíti, hogy ezek a kötetek a mellékletet, miután kell csatlakoztatni a köteteket a nagyméretű HANA-példány egységhez.

![DR-telepítés következő lépésre](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A következő lépés a második az SAP HANA-példány telepítésére a nagyméretű HANA-példány egységen, a teszt HANA-példányt futtató Vészhelyreállítás Azure-régióban van. Az újonnan telepített SAP HANA-példányt kell rendelkeznie a SID AZONOSÍTÓVAL. A létrehozott felhasználók UID és a csoport azonosítója, amely rendelkezik az éles üzemelő példányok rendelkeznie kell. Olvasási [biztonsági mentési és visszaállítási](hana-backup-restore.md) részleteiről. Ha a telepítés sikeres volt, kell tennie:

- A pillanatkép előkészítése leírt 2. lépés végrehajtásához [biztonsági mentési és visszaállítási](hana-backup-restore.md).
- Ha még nem még meg, hozzon létre egy nyilvános kulcsot a DR egység nagyméretű HANA-példány egység. Tekintse meg a 3. lépésében ismertetett tárolási pillanatkép előkészítése [biztonsági mentési és visszaállítási](hana-backup-restore.md).
- Karbantartása a *HANABackupCustomerDetails.txt* az új HANA-példány és a teszt-e kapcsolat a tároló megfelelően működik-e.  
- Az újonnan telepített SAP HANA-példány leállítása a nagyméretű HANA-példány egységen a Vészhelyreállítás Azure-régióban.
- Válassza le ezeket a PRD köteteket, és forduljon az SAP HANA az Azure Service Management. A kötetek nem marad csatlakoztatott egységhez, mert storage replikációs célként, de azok nem lesznek elérhetőek.  

![DR-telepítési lépés replikációs kialakítása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Az üzemeltetési csapat létrehozza a replikációs kapcsolat az éles Azure-régióban lévő PRD kötetek és a Vészhelyreállítás Azure-régióban a PRD kötetek között.

>[!IMPORTANT]
>A /hana/log kötet nincs replikálva, mivel nem szükséges a replikált SAP HANA-adatbázis visszaállítása a vész-helyreállítási webhelyként a konzisztens állapotra.

Ezután állítsa be, vagy módosítsa a tárolási pillanatkép biztonsági mentési ütemezés abban a vészhelyreállítási esetben az RTO és RPO elérése. A helyreállításipont-célkitűzés minimalizálása érdekében állítsa be a következő replikálási időközök a nagyméretű HANA-példány szolgáltatásban:
- A a kombinált pillanatkép által szabályozott kötetek (pillanatkép típusa **hana**), 15 percenként, megfelelő tárolási kötet célelemein kerüljön a vész-helyreállítási webhelyként replikálásához beállítása.
- A tranzakciós napló biztonsági mentési kötet (pillanatkép típusa **naplók**), készlet replikálni a vész-helyreállítási webhelyként azonos tárolási kötet célelemein kerüljön át 3 percenként.

A helyreállításipont-célkitűzés minimalizálása érdekében állítsa be a következőket:
- Hajtsa végre egy **hana** típusú storage-pillanatkép (lásd: "7. lépés: hajtsa végre a pillanatképek") 30 percenként 1 óra.
- Hajtsa végre az SAP HANA tranzakciónapló biztonsági mentései 5 percenként.
- Hajtsa végre egy **naplók** írja be az 5 – 15 percenként pillanatkép-tárolás. Az intervallum idővel körülbelül 15-25 percig helyreállítási Időkorlátot érhet el.

Ez a beállítás, a tranzakciós naplók biztonsági mentését, a pillanatképek tárolási és a HANA-tranzakció a replikáció végrehajtása naplózhatja a biztonsági mentési mennyiségi és/hana/adatait, és (beleértve a/usr/sap) /hana/shared hasonló lehet az adatokat, az ábrán látható:

 ![A tranzakciós napló biztonsági mentési pillanatképet és a egy időtengelye beépülő tükör közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Egy katasztrófa utáni helyreállítás esetben még jobb RPO eléréséhez a más Azure-régió másolhatja a HANA tranzakciónaplók biztonsági mentése az Azure-ban (nagyméretű példányok) SAP HANA-ból. A további helyreállítási Időkorlát csökkentését eléréséhez hajtsa végre az alábbi lépéseket:

1. Készítsen biztonsági másolatot a HANA-tranzakció naplózása gyakorisággal a lehető /hana/logbackups.
1. Rsync használatával másolja a tranzakciónapló biztonsági mentései az NFS megosztás által üzemeltetett Azure virtuális gépeket. A virtuális gépek az Azure virtuális hálózatok az éles Azure-régióban, és a DR régióban találhatók. Mind az Azure virtuális hálózatok összekapcsolása az üzemi nagyméretű HANA-példányokhoz csatlakozhat Azure-kapcsolatcsoporthoz szeretne. Tekintse meg a grafikus elemek az [hálózati szempontok vész-helyreállítási HANA nagyméretű példányok az](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) szakaszban. 
1. Tartsa a tranzakciónapló biztonsági mentései a régióban, a virtuális gép csatlakozik-e az NFS tárolási exportált.
1. Vészhelyreállítási feladatátvétel esetben kiegészítik a tranzakciónapló biztonsági mentéseivel, keresse meg a /hana/logbackups köteten több nemrég végrehajtott tranzakciónapló biztonsági mentései az NFS megosztani a vész-helyreállítási helyként. 
1. Indítsa el a tranzakciónapló biztonsági mentését, állítsa vissza a legutóbbi biztonsági másolatra, amely a Vészhelyreállítási régióban keresztül lehet menteni.

Amikor hozzákezd a végrehajtási tárhely pillanatképes biztonsági mentései nagyméretű HANA-példány műveletek erősítse meg a replikációs kapcsolat beállítása, megkezdi az adatok replikációját.

![DR-telepítési lépés replikációs kialakítása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Mivel a replikáció különböző fázisokon halad, a Vészhelyreállítás Azure-régiókban PRD köteteken a pillanatképeket a rendszer nem állítja vissza. Csak tárolódnak. Ha a kötetek csatlakoztatva legyenek olyan állapotban, az állam, amelyben meg leválasztani a köteteket a kiszolgáló egységben, a Vészhelyreállítás Azure-régióban a PRD SAP HANA-példány telepítését követő jelölnek. A tároló biztonsági mentések, amelyek még nem állítja vissza is képviselik.

Feladatátvétel esetén is lehet váltani egy régebbi tárolási pillanatkép helyett a legújabb tárolási pillanatkép visszaállítása.

## <a name="next-steps"></a>További lépések

- Tekintse meg [vészhelyreállítási feladatátvétel eljárást](hana-failover-procedure.md).