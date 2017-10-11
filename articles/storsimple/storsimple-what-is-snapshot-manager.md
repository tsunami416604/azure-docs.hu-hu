---
title: Mi az a StorSimple Snapshot Manager? | Microsoft Docs
description: "A StorSimple Snapshot Manager, az architektúra és annak szolgáltatásait ismerteti."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 38c197c7bc57110b29b1d8cb789d5b7310823da2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager bemutatása

## <a name="overview"></a>Áttekintés
StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja, amely leegyszerűsíti az adatvédelem és biztonsági mentési kezelése a Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével kezelheti az adatközpontban és a felhőben található Microsoft Azure StorSimple adatok egyetlen integrált tárolási megoldásként, így biztonsági mentési folyamat egyszerűsítése, illetve csökkenti a költségeket.

Ez az áttekintés bemutatja a StorSimple Snapshot Manager, annak szolgáltatásait ismerteti, és szerepét a Microsoft Azure StorSimple ismerteti. 

A teljes Microsoft Azure StorSimple rendszer, a SharePoint, a StorSimple eszköz, a StorSimple Manager szolgáltatás, a StorSimple Snapshot Manager és a StorSimple Adapter beleértve áttekintését lásd: [StorSimple 8000 series: hibrid felhő tárolási megoldás](storsimple-overview.md). 

> [!NOTE]
> * StorSimple Snapshot Manager kezelése a Microsoft Azure StorSimple virtuális tömbök (más néven StorSimple a helyszíni virtuális eszköz) nem használható.
> * Ha a StorSimple eszköz StorSimple 2. frissítés telepítését tervezi, ügyeljen arra, hogy a StorSimple Snapshot Manager legújabb verziójának letöltéséhez és telepítéséhez **StorSimple 2. frissítés telepítése előtt**. A StorSimple Snapshot Manager legújabb verziójára visszamenőlegesen kompatibilis, és együttműködik a Microsoft Azure StorSimple kiadott verziói. A StorSimple Snapshot Manager korábbi verzióját használja, ha szüksége lesz a frissítést (nem kell eltávolítani a korábbi verzióját, az új verzió telepítése előtt).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager célját és architektúra
StorSimple Snapshot Manager biztosít egy hozhat létre egységes, központi kezelőkonzol időpontban a biztonsági másolatok a helyi és felhőbeli adatát. Például a konzolt is használhatja:

* Konfigurálhatja, készítsen biztonsági másolatot, és törölje köteteket.
* Kötet csoportokat győződjön meg arról, hogy a biztonsági másolatba mentett adatok alkalmazáskonzisztens.
* Biztonsági mentési házirendek kezelése, így az adatok biztonsági másolatát egy előre meghatározott ütemezés szerint.
* Hozzon létre a helyi és felhőalapú pillanatfelvételek, amely a felhőben tárolt, és a katasztrófa utáni helyreállítás.

A StorSimple Snapshot Manager beolvassa a VSS-szolgáltató a gazdagép regisztrált alkalmazások listáját. Ezután alkalmazáskonzisztens biztonsági másolatok készítéséhez ellenőrzi a egy alkalmazás által használt köteteket, és kötet csoportjait a konfiguráláshoz javasol. StorSimple Snapshot Manager ezek kötet csoportokat használ, amelyek alkalmazáskonzisztens biztonsági másolatok készítése. (Alkalmazás konzisztencia beszélünk, amikor az összes kapcsolódó fájlokat és adatbázisok szinkronizálása, és az alkalmazás adott időben igaz állapotát.) 

StorSimple Snapshot Manager biztonsági másolatok formájában növekményes pillanatképet, rögzítheti a módosítások csak az utolsó biztonsági mentés óta. Emiatt biztonsági mentések kevesebb tárolási rendszerre van szüksége, és hozható létre és gyorsan vissza. StorSimple Snapshot Manager a Windows kötet árnyékmásolata szolgáltatás (VSS) segítségével győződjön meg arról, hogy a pillanatképek alkalmazáskonzisztens adatok rögzítéséhez. (További információért keresse fel az integráció a Windows a kötet árnyékmásolata szolgáltatás szakasz.) A StorSimple Snapshot Manager létrehozhat biztonsági mentési ütemterv vagy azonnali biztonsági másolatok készítése, igény szerint. Ha vissza kell állítania az adatok a biztonsági másolat, a StorSimple Snapshot Manager lehetővé teszi a helyi katalógus vagy a felhőalapú pillanatfelvételek közül választhat. Az Azure StorSimple visszaállítása csak a adatokhoz, szükséges, amely megakadályozza a késlelteti az adatok rendelkezésre állását visszaállítási műveletek során.)

![StorSimple Snapshot Manager architektúrája](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager architektúrája** 

## <a name="support-for-multiple-volume-types"></a>Több kötet típusok támogatása
A StorSimple Snapshot Manager segítségével konfigurálhatja, és készítsen biztonsági másolatot a következő típusú kötetek: 

* **Alapszintű kötetek** – egyszerű kötet egy olyan egyetlen partíció egy alaplemezen. 
* **Egyszerű kötet** – egyszerű kötet dinamikus kötet, amely tartalmaz egyetlen dinamikus lemez szabad lemezterület. Egyszerű kötet egy egyetlen területet a lemezen, vagy több azon a lemezen, egymáshoz kapcsolódó áll. (Kötet is kialakítható egyszerű csak dinamikus lemezeken.) Egyszerű kötetekkel nincsenek hibatűrő.
* **A dinamikus kötetek** – A dinamikus kötet olyan kötet dinamikus lemezen létrehozni. A dinamikus lemezek adatbázis segítségével követheti nyomon a dinamikus lemezek a számítógépben található kötetek adatait. 
* **A dinamikus kötetek tükrözés** – tükrözés dinamikus kötetek a RAID 1 architektúra épül. RAID 1 két vagy több lemez, és egy tükrözött készlet megegyezik adatot ír. Egy olvasási kérést majd bármely lemezt, amely tartalmazza a kért adatokat kell kezelnie.
* **Fürt megosztott kötetei** – a fürt megosztott kötetei (CSV), egy feladatátvevő fürt is egyszerre olvasni vagy ugyanarra a lemezre írás több csomópontja. Feladatátvétel egyik csomópontjáról egy másik csomópontra akkor fordulhat elő, gyorsan, anélkül, hogy a meghajtó tulajdonosa vagy csatlakoztatni, leválasztása és eltávolítása a kötet módosítják. 

> [!IMPORTANT]
> Ne keverje a CSV-k és a CSV a azonos pillanatkép. A CSV-k és CSV pillanatképbe keverése nem támogatott. 
> 
> 

StorSimple Snapshot Manager használatával állítsa vissza a teljes kötet csoportok vagy egyéni kötetek klónozása és az egyes fájlok helyreállítása.

* [Kötetek és a kötet csoportok](#volumes-and-volume-groups) 
* [Biztonsági mentési típusok és a biztonsági mentési házirendek](#backup-types-and-backup-policies) 

További információ a StorSimple Snapshot Manager szolgáltatások és a használatukat: [StorSimple Snapshot Manager felhasználói felület](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Kötetek és a kötet csoportok
A StorSimple Snapshot Manager, akkor hozzon létre köteteket, és majd beállíthatja azokat kötet csoportokba. 

StorSimple Snapshot Manager kötet csoportokat használ, amelyek alkalmazáskonzisztens biztonsági másolatot készíteni. Alkalmazás konzisztencia beszélünk, amikor az összes kapcsolódó fájlokat és adatbázisok szinkronizálása, és az alkalmazások egy adott időpontban időben igaz állapotát. Kötet csoportok (más néven ezek *konzisztencia csoportok*) alapját a biztonsági mentési vagy visszaállítási feladat.

Kötet csoportok nincsenek kötettárolók azonos. A kötettároló tartalmaz egy vagy több kötet egy felhőbeli tárfiókot és egyéb attribútumait, például a titkosítás és a sávszélesség-használatát. Egyetlen kötettároló tartalmazhat 256 dinamikusan kiosztott StorSimple-köteteket. Kötettárolók kapcsolatos további információkért látogasson el [kezelése a kötettárolók](storsimple-manage-volume-containers.md). Kötet csoportok biztonsági mentési műveletek megkönnyítésére konfigurált kötetek gyűjteményei. Ha két kötetek, amelyek a tartoznak különböző kötettárolók, helyezze el őket egy kötet csoportban, majd hozza létre a kötetet csoport biztonsági mentési házirend, minden olyan kötetre készül biztonsági másolat a megfelelő kötetet a tárolóban, a megfelelő tárolási fiók használatával.

> [!NOTE]
> Egy kötet csoportban található összes kötetet egy felhőszolgáltató kell származnia.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integráció a Windows a kötet árnyékmásolata szolgáltatás
StorSimple Snapshot Manager alkalmazáskonzisztens adatok rögzítéséhez a Windows kötet árnyékmásolata szolgáltatás (VSS) használja. VSS alkalmazás konzisztencia elősegíti a Kötetárnyékmásolat-felismerésre képes alkalmazások növekményes pillanatképek létrehozásának koordinálására kapcsolatba lép. VSS biztosítja, hogy az alkalmazások ideiglenesen inaktív vagy videokártyának, amikor a pillanatfelvételeket készít. 

A StorSimple Snapshot Manager végrehajtása VSS együttműködik a SQL Server és a általános NTFS-köteteket. A folyamat a következőképpen történik: 

1. A kérelmező, amely általában egy adatok kezelése és adatvédelmi megoldás (például a StorSimple Snapshot Manager) vagy a biztonságimásolat-készítő alkalmazás, VSS hív, és megkérdezi, hogy információkat gyűjtsön a író szoftver a célalkalmazásban.
2. VSS kapcsolatba lép a író összetevője egy leírást az adatok beolvasása. Az író az adatok biztonsági mentése a leírását adja eredményül. 
3. VSS jelzi az író készíti elő az alkalmazás a biztonsági mentéshez. Az író az adatok biztonsági mentés előkészíti a nyitott tranzakciók végrehajtásával, tranzakciós naplókat, és így tovább frissítése, majd értesíti a VSS-t.
4. VSS arra utasítja, hogy az író ideiglenesen állítsa le az alkalmazás adatokat tárolja, és győződjön meg arról, hogy nincs adatot ír a kötet árnyékmásolata elkészítése közben. Ez a lépés biztosítja az adatok konzisztenciájának, és legfeljebb 60 másodpercet vesz igénybe.
5. VSS arra utasítja a szolgáltatót, amelyet az árnyékmásolat létrehozása. Szolgáltatók, amely lehet a szoftver - és hardver-alapú, az aktuálisan futó és az igény szerinti árnyékmásolatait hozza őket létre kötetek kezelése. A szolgáltató hoz létre az árnyékmásolatot, és a VSS értesíti, amikor elkészült.
6. VSS kapcsolatba lép az író értesíteni i/o folytathatja az alkalmazás és annak ellenőrzéséhez, hogy i/o felfüggesztése sikerült árnyékmásolat létrehozása során. 
7. Ha a másolása sikeres volt, a VSS a Másolás helye a kérelmező adja vissza. 
8. Ha az adatok írása megtörtént, amíg az árnyékmásolat létrejött, a biztonsági mentés konzisztens lesz. VSS az árnyékmásolat törlése, és értesíti az kérelmezőnek. A kérelmező ismételje meg a biztonsági mentési folyamat automatikus vagy értesíti a rendszergazdát, hogy próbálja meg újra később.

Tekintse meg az alábbi ábrán látható.

![VSS-folyamat](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**A Windows a kötet árnyékmásolata szolgáltatás folyamat** 

## <a name="backup-types-and-backup-policies"></a>Biztonsági mentési típusok és a biztonsági mentési házirendek
A StorSimple Snapshot Manager, az adatok biztonsági másolatát és tárolására is használható, helyi és a felhőben. StorSimple Snapshot Manager segítségével az adatok biztonsági másolatát azonnal, vagy a biztonsági mentési házirend segítségével hozzon létre egy ütemezést az automatikus biztonsági másolatok fogadására. Biztonsági mentési házirendek lehetővé teszik adja meg, hány pillanatfelvételek megmaradnak. 

### <a name="backup-types"></a>Biztonsági mentési típusok
StorSimple Snapshot Manager hozhat létre a következő típusú biztonsági mentések:

* **A pillanatképek helyi** – helyi pillanatképeket másolatai időpontban kötetet a StorSimple eszközön tárolt adatokat. Általában ilyen típusú biztonsági mentést hozható létre és gyorsan visszaállítva. Egy helyi pillanatfelvétel a helyi biztonsági másolat ugyanúgy használhatja.
* **Felhőalapú pillanatfelvételek** – felhőalapú pillanatfelvételek másolatai időpontban kötet adatait, amely a felhőben vannak tárolva. Egy felhőalapú pillanatfelvétel megegyezik egy másik, a telephelytől távoli tárolórendszer replikálva pillanatképet. Felhőalapú pillanatfelvételek különösen hasznosak a vész-helyreállítási eljárással.

### <a name="on-demand-and-scheduled-backups"></a>Igény szerinti és ütemezett biztonsági mentések tiltása
A StorSimple Snapshot Manager, a egyszeri biztonsági jön létre azonnal is kezdeményezhető, vagy használhatja a biztonsági mentési házirend ismétlődő biztonsági mentési műveletek ütemezése.

A biztonsági mentési házirend olyan automatikus szabályok, amelyek segítségével rendszeres biztonsági mentéseket. A biztonsági mentési házirend megadhatja a gyakoriságát és a pillanatképek készítése egy adott kötet csoport a paramétereket. Házirendek segítségével adja meg a érvényességének kezdő és záró dátum, idő, gyakoriságot és megőrzési követelményei, mind a helyi és felhőalapú pillanatfelvételek. A házirend érvényben van, megadhatja azt követően azonnal. 

StorSimple Snapshot Manager segítségével konfigurálhatja, vagy konfigurálja újra a biztonsági mentési házirendek, amikor szükséges. 

Konfigurálhatja a biztonsági mentési házirendet hoz létre a következő információkat:

* **Név** – a kijelölt biztonsági mentési házirend egyedi nevét.
* **Típus** – a biztonsági mentési házirend; helyi pillanatfelvétel és felhőbeli pillanatfelvétel típusú.
* **Kötet csoport** – a kötet csoport, amelyhez hozzá van rendelve a kijelölt biztonsági mentési házirend.
* **Megőrzési** – a biztonsági másolatot a megőrizni kívánt száma. Ha bejelöli a **összes** be, minden egyes biztonsági másolatok csak a biztonsági másolatok kötetenkénti maximális számának elérésekor, amikor a házirend meghiúsul, és létre hibaüzenetet. Azt is megteheti megadhat egy biztonsági másolatok megőrzése (közötti 1 és 64) száma.
* **Dátum** – a biztonsági mentési házirend létrehozásának dátumát.

A biztonsági mentési házirendek konfigurálásával kapcsolatos információkért lásd [használja a StorSimple Snapshot Manager létrehozásához és kezeléséhez a biztonsági mentési házirendek](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Biztonsági mentési feladat ellenőrzése és kezelése
A StorSimple Snapshot Manager segítségével figyelheti és kezelheti a jövőbeli, ütemezett és befejezett biztonsági mentési feladatok. StorSimple Snapshot Manager emellett legfeljebb 64 befejezett biztonsági mentés katalógusa. A katalógus segítségével keresse meg és kötetek vagy fájlok visszaállítása. 

A biztonsági mentési feladatok figyelésével kapcsolatos tudnivalókat lásd [használja a StorSimple Snapshot Manager megtekintéséhez és kezeléséhez a biztonsági mentési feladatok](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Töltse le [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

