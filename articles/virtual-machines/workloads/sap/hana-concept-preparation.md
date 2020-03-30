---
title: Vész-helyreállítási alapelvek és előkészítés az SAP HANA-n az Azure-on (nagy példányok) | Microsoft dokumentumok
description: Vész-helyreállítási alapelvek és előkészítés az SAP HANA-n az Azure-ban (nagy példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101263"
---
# <a name="disaster-recovery-principles"></a>Katasztrófa utáni helyreállítás elvei

Hana nagy példányok kínálnak vész-helyreállítási funkció hana nagy példánybélyegek között a különböző Azure-régiókban. Például ha hana nagy példány egységek az Azure usa-beli nyugati régiójában, használhatja a HANA nagy példány egységek az USA keleti régiójában vész-helyreállítási egységek. Mint korábban említettük, vész-helyreállítási nincs automatikusan konfigurálva, mert megköveteli, hogy egy másik HANA nagy példány egységét a VÉSZ-régióban. A vész-helyreállítási telepítő működik a horizontális felskálázás, valamint a kibővített beállításokat. 

Az eddig telepített forgatókönyvekben az ügyfelek a VÉSZ-régióban lévő egységet használják a telepített HANA-példányt használó nem éles rendszerek futtatásához. A HANA nagy példány egységkell az azonos termékváltozat, mint a termelési célokra használt termékváltozat. Az alábbi képen látható, hogy hogyan néz ki az Azure éles környezetében lévő kiszolgálóegység és a vész-helyreállítási régió közötti lemezkonfiguráció:

![A DR beállítási konfigurációja lemezszempontból](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Amint az az áttekintő ábrán látható, meg kell rendelnie egy második lemezkötet-készletet. A céllemez-kötetek mérete megegyezik a vész-helyreállítási egységek termelési példányának termelési köteteivel. Ezek a lemezkötetek a vész-helyreállítási helyen található HANA nagypéldány-kiszolgálóegységhez vannak társítva. A következő kötetek replikálódnak a termelési régióból a VÉSZ-helyre:

- /hana/adatok
- /hana/logbackups 
- /hana/shared (beleértve a /usr/sap kapcsolót)

A /hana/log kötet nem replikálódik, mert az SAP HANA tranzakciós napló nem szükséges, ahogy a kötetek visszaállítása történik. 

A felajánlott vész-helyreállítási funkció alapja a HANA nagypéldány-infrastruktúra által kínált tárolási replikációs funkció. A tárolási oldalon használt funkció nem a folyamatosan replikáló változások állandó folyama, mivel a tárolókötet változásai történnek. Ehelyett ez egy olyan mechanizmus, amely azon a tényen alapul, hogy ezeka kötetek pillanatképei rendszeresen jönnek létre. A már replikált pillanatkép és a még nem replikált új pillanatkép közötti különbözetet a rendszer átviszi a vész-helyreállítási helyre a céllemez-kötetekbe.  Ezek a pillanatképek a köteteken tárolódnak, és ha vész-helyreállítási feladatátvétel történik, vissza kell állítani ezeket a köteteket.  

A kötet teljes adatainak első átvitelének kell lennie, mielőtt az adatmennyiség kisebb lesz, mint a pillanatképek közötti különbözetek. Ennek eredményeképpen a DR-hely kötetei tartalmazzák az éles helyen végrehajtott kötet-pillanatképek mindegyikét. Végül használhatja, hogy a DR-rendszer, hogy egy korábbi állapot az elveszett adatok helyreállítása, anélkül, hogy a termelési rendszer visszaállítása.

Ha egy MCOD-telepítés több független SAP HANA-példányok egy HANA nagy példány egység, várható, hogy az összes SAP HANA-példányok kapnak tároló replikált a VÉSZ-oldalon.

Azokban az esetekben, amikor a HANA rendszerreplikációt magas rendelkezésre állású funkcióként használja az éles helyen, és a VÉSZ-hely tárolóalapú replikációját használja, az elsődleges helyről a VÉSZ-példányra lévő csomópontok kötetei replikálódnak. A DR-helyen további (az elsődleges csomópontmérettel megegyező méretű) tárolót kell vásárolnia, hogy az elsődleges és a másodlagos rendszerből származó replikáció t lehetővé tegye. 



>[!NOTE]
>A HANA nagy példány tárolási replikációs funkció a tárolási pillanatképek tükrözése és replikálása. Ha nem hajt végre tárolási pillanatképeket a cikk Biztonsági másolat és visszaállítás szakaszában bemutatottak szerint, nem lehet replikáció a vész-helyreállítási helyre. A tárolási pillanatkép végrehajtása a vész-helyreállítási hely tárolási replikációjának előfeltétele.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>A vész-helyreállítási forgatókönyv előkészítése
Ebben a forgatókönyvben egy éles rendszer fut hana nagy példányok az éles Azure-régióban. Az alábbi lépésekért tegyük fel, hogy a HANA-rendszer biztonsági azonosítója "PRD", és hogy rendelkezik egy nem éles rendszer hana nagy példányok a DR Azure-régióban. Az utóbbi, tegyük fel, hogy a SID "TST." Az alábbi képen ez a konfiguráció látható:

![A vészkiásás-beállítások kezdete](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Ha a kiszolgálópéldány még nem lett megrendezve a további tárolókötet-készlettel, az SAP HANA az Azure Service Management szolgáltatásban a további kötetkészletet az éles replikához rendeli ahhoz a HANA nagypéldány-egységhez, amelyen a TST-t futtatja. HANA-példány. Erre a célra meg kell adnia az éles HANA-példány sid-jét. Miután az SAP HANA az Azure Service Management megerősíti a mellékletet a kötetek, csatlakoztatnia kell ezeket a köteteket a HANA nagy példány egység.

![Dr beállítás következő lépés](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A következő lépés a második SAP HANA-példány telepítése a HANA nagy példány egységét a DR Azure-régióban, ahol a TST HANA-példány futtatásához. Az újonnan telepített SAP HANA-példánynak ugyanazzal a biztonsági azonosítóval kell rendelkeznie. A létrehozott felhasználóknak ugyanazt az UID- és csoportazonosítót kell rendelkezniük, mint a termelési példánynak. A részletekért olvassa el a [Biztonsági mentés és visszaállítás](hana-backup-restore.md) segédprogramot. Ha a telepítés sikeres volt, a következőket kell a következőkre:

- A [Biztonsági másolat és visszaállítás](hana-backup-restore.md)című részben ismertetett tárolási pillanatkép-előkészítés 2.
- Hozzon létre egy nyilvános kulcsot a HANA nagy példány egység vészegységéhez, ha még nem tette meg. Lásd a biztonsági mentés és [visszaállítás](hana-backup-restore.md)című témakörben ismertetett tárolási pillanatkép-előkészítés 3.
- A *HANABackupCustomerDetails.txt* fájl karbantartása az új HANA-példányokkal, és ellenőrizze, hogy a tárolóba való kapcsolódás megfelelően működik-e.  
- Állítsa le az újonnan telepített SAP HANA-példányt a HANA nagy példány egységen a DR Azure régióban.
- Leválasztani ezeket a PRD-kötetek, és lépjen kapcsolatba az SAP HANA az Azure Service Management. A kötetek nem maradhatnak csatlakoztatva az egységhez, mert nem érhetők el, miközben tárolóreplikációs célként működnek.  

![Vész-kiszolgáló rendszer beállítása a replikáció létrehozása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

A műveleti csapat létrehozza a replikációs kapcsolatot a PRD-kötetek az éles Azure-régióban és a DR Azure régióban a PRD-kötetek között.

>[!IMPORTANT]
>A /hana/log kötet nem replikálódik, mert nem szükséges visszaállítani a replikált SAP HANA-adatbázist egy konzisztens állapotba a vész-helyreállítási helyen.

Ezután állítsa be, vagy állítsa be a tárolási pillanatkép biztonsági mentési ütemezését, hogy az RTO és az RPO a katasztrófa esetén. A helyreállításipont-célkitűzés minimalizálása érdekében állítsa be a következő replikációs időközöket a HANA nagypéldány-szolgáltatásban:
- A kombinált pillanatkép által lefedett kötetek (pillanatkép típusa **hana),** állítsa replikálni 15 percenként a megfelelő tárolási kötetek céljaiak a vész-helyreállítási hely.
- A tranzakciós napló biztonsági mentési kötet (pillanatkép típusú **naplók),** állítsa replikálni 3 percenként a megfelelő tárolási kötet célokat a vész-helyreállítási helyen.

A helyreállítási pont célkitűzésének minimalizálása érdekében állítsa be a következőket:
- Hana **hana** típusú tárolási pillanatkép végrehajtása (lásd: "7. lépés: Pillanatképek végrehajtása") 30 percenként 1 óránként.
- SAP HANA tranzakciós napló biztonsági mentések 5 percenként.
- 5–15 percenként naplók típusú tárolási pillanatképet **hajtson** végre. Ezzel az intervallummal körülbelül 15-25 perces RPO-t érhet el.

Ezzel a beállítással a tranzakciónapló biztonsági másolatainak, a tárolási pillanatképeknek a sorozata, valamint a HANA tranzakciónapló biztonsági mentési kötetének és a /hana/data, valamint a /hana/shared (beleértve a /usr/sap) replikációja az ezen az ábrán látható adatokhoz hasonlóan nézhet ki:

 ![A tranzakciónapló biztonsági mentési pillanatképe és az időtengelyen lévő illesztőtükör közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

A vész-helyreállítási esetben egy még jobb RPO elérése érdekében a HANA tranzakciós napló biztonsági mentések az SAP HANA az Azure-ban (nagy példányok) a másik Azure-régióban. A további RPO-csökkentés eléréséhez hajtsa végre a következő lépéseket:

1. Készítsen biztonsági másolatot a HANA tranzakciós naplóról a lehető leggyakrabban a /hana/logbackups kapcsolóra.
1. Az rsync használatával másolja a tranzakciónapló biztonsági másolatait az NFS-megosztásáltal üzemeltetett Azure virtuális gépekre. A virtuális gépek az Azure éles környezetében és a VÉSZ-régiókban található virtuális Azure-hálózatokban találhatók. Mindkét Azure virtuális hálózatot csatlakoztatnia kell az éles HANA nagypéldányokat az Azure-hoz összekötő áramkörhöz. Tekintse meg a grafikus a [hálózati szempontok a vész-helyreállítási HANA nagy példányok](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) szakaszban. 
1. Tartsa a tranzakciós napló biztonsági mentések a régióban a virtuális gép csatlakozik az NFS exportált tároló.
1. Egy katasztrófa feladatátvételi esetben egészítse ki a tranzakciós napló biztonsági mentések megtalálható a /hana/logbackups kötet újabban vett tranzakciónapló biztonsági mentések az NFS-megosztás a vész-helyreállítási helyen. 
1. Indítsa el a tranzakciónapló biztonsági mentését a legutóbbi biztonsági mentés visszaállításához, amely a VÉSZ-régióba menthető.

Amikor a HANA nagypéldány-műveletek megerősítik a replikációs kapcsolat beállítását, és elindítja a végrehajtási tároló pillanatképbiztonsági biztonsági mentéseit, megkezdődik az adatreplikáció.

![Vész-kiszolgáló rendszer beállítása a replikáció létrehozása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

A replikáció előrehaladtával a DR Azure-régiók BAN a PRD-kötetek pillanatképei nem lesznek visszaállítva. Csak tárolják őket. Ha a kötetek ilyen állapotban vannak csatlakoztatva, akkor azt az állapotot jelölik, amelyben leszerelte ezeket a köteteket, miután a PRD SAP HANA-példány telepítve volt a kiszolgálóegységben a DR Azure-régióban. Ők is képviselik a tárolási biztonsági mentések, amelyek még nem állították vissza.

Ha van egy feladatátvétel, azt is választhatja, hogy a legújabb tárolási pillanatkép helyett egy régebbi tárolási pillanatkép visszaállítása.

## <a name="next-steps"></a>További lépések

- Tekintse át [a vész-helyreállítási feladatátvételi eljárást.](hana-failover-procedure.md)
