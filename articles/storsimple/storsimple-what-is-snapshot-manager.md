---
title: Mi az a StorSimple Snapshot Manager? | Microsoft Docs
description: A StorSimple Snapshot Managerben, architektúrájának és funkcióit ismerteti.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: v-sharos
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f7436bb63f52c9c2b697c8e7031922ce89d786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789614"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>A StorSimple Snapshot Manager bemutatása

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja, amely leegyszerűsíti az adatok védelme és a biztonságimásolat-felügyelet a Microsoft Azure StorSimple-környezetben. A StorSimple Snapshot Managerrel kezelheti az adatközpontban és a felhőben a Microsoft Azure StorSimple data egyetlen integrált tárolási megoldás, így leegyszerűsíti a biztonsági mentési folyamatokat és költségeit.

Ez az Áttekintés vezet be a StorSimple Snapshot Managerben, annak szolgáltatásait ismerteti, és ismerteti a szerepét a Microsoft Azure StorSimple. 

A teljes Microsoft Azure StorSimple rendszerbe, amely többek között a StorSimple-eszköz, a StorSimple Manager szolgáltatás, a StorSimple Snapshot Manager és a StorSimple Adapter for SharePoint, az áttekintést lásd: [a StorSimple 8000 sorozat: hibrid felhő tárolási megoldás](storsimple-overview.md). 

> [!NOTE]
> * A StorSimple Snapshot Manager kezelése a Microsoft Azure StorSimple Virtual Arrayt (más néven a StorSimple helyszíni virtuális eszköz) nem használható.
> * Ha azt tervezi, a StorSimple eszköz StorSimple Update 2 telepítése, ügyeljen arra, hogy a StorSimple Snapshot Manager legújabb verzió letöltése és telepítése azt **2. frissítés telepítése előtt**. A legújabb verzióra a StorSimple Snapshot Manager visszamenőlegesen kompatibilis és együttműködik a Microsoft Azure StorSimple kiadott verzióihoz. A StorSimple Snapshot Manager korábbi verziót használ, ha (nem kell az új verzió telepítése előtt, távolítsa el az előző verzió) frissíteni kell.
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>A StorSimple Snapshot Manager célú és architektúra
A StorSimple Snapshot Manager biztosít egy központi kezelőkonzol segítségével létrehozhat egységes, időponthoz – biztonsági másolatok a helyi és felhőbeli adatok. Ha például a konzolt is használhatja:

* Konfigurálja, biztonsági mentése és kötetek törlése.
* Konfigurálja a kötet csoportokat, győződjön meg arról, hogy a biztonsági másolatba mentett adatok alkalmazáskonzisztens.
* Biztonsági mentési házirendek kezelése, így az adatok biztonsági mentésének előre meghatározott ütemezés szerint.
* Hozza létre a helyi és felhőalapú pillanatfelvételek, amely a felhőben tárolt, és a vészhelyreállításhoz használt.

A StorSimple Snapshot Manager beolvassa a VSS-szolgáltatót a gazdagép regisztrált alkalmazások listáját. Ezután alkalmazásfüggő biztonsági másolatok létrehozásához, a köteteket, az alkalmazás ellenőrzi, és javaslatot tesz a kötet csoportjait a konfiguráláshoz. A StorSimple Snapshot Manager ezek kötet csoportokat használ, amelyek alkalmazáskonzisztens biztonsági másolatok készítése. (Alkalmazáskonzisztencia létezik, amikor az összes kapcsolódó fájlokat, és adatbázisok szinkronizálása, és az alkalmazás egy adott időpontban időben igaz állapotát.) 

A StorSimple Snapshot Manager biztonsági másolatokat, növekményes pillanatkép, amely csak a változások rögzítésére a legutóbbi biztonsági mentés óta formájában. Ennek eredményeképpen biztonsági mentések kisebb tárolási szükséges, és létrehozhatók és gyorsan vissza. A StorSimple Snapshot Manager a Windows kötet árnyékmásolata szolgáltatás (VSS) használja annak érdekében, hogy a pillanatképek alkalmazáskonzisztens adatok rögzítéséhez. (További információért keresse fel az integráció a Windows a kötet árnyékmásolata szolgáltatás szakaszban.) A StorSimple Snapshot Managerrel, létrehozhat biztonsági mentési ütemezéseket, vagy azonnali biztonsági mentés időpontjának, igény szerint. Ha egy biztonsági mentési és a StorSimple Snapshot Manager lehetővé teszi az adatokat vissza kell állítania egy katalógus, helyi vagy felhőbeli pillanatképek közül választhat. Az Azure StorSimple visszaállítása csak a adatokhoz, szükséges, amely megakadályozza az késleltetések az adatok rendelkezésre állását visszaállítási műveletek során.)

![A StorSimple Snapshot Manager-architektúra](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**A StorSimple Snapshot Manager-architektúra** 

## <a name="support-for-multiple-volume-types"></a>Több kötet típusok támogatása
A StorSimple Snapshot Manager használatával konfigurálhatja, és készítsen biztonsági másolatot a következő típusú kötetek: 

* **Alapszintű kötetek** – alaplemezek egy partíció egy alaplemezen. 
* **Egyszerű kötetekkel** – egy egyszerű kötet dinamikus kötet, amely tartalmaz egyetlen dinamikus lemez szabad lemezterület. Egyszerű kötet áll egy adott régióban lemezen vagy több régióban, amelyek össze vannak kapcsolva azon a lemezen. (Hozhat létre egyszerű kötet dinamikus lemezeken.) Egyszerű kötet nem, hibatűrő.
* **A dinamikus köteteket** – egy dinamikus kötet olyan kötet dinamikus lemezen létrehozni. A dinamikus lemezek egy adatbázis használatával nyomon követheti a köteteket, amelyek tartalmazzák a számítógépben található dinamikus lemezeken. 
* **A dinamikus köteteket a tükrözés** – tükrözés a dinamikus köteteket a RAID 1 architektúra épül. RAID-1 azonos adatokat két vagy több lemez, egy tükrözött set előállító van megírva. Minden olyan lemezt, amely tartalmazza a kért adatok alapján lehet kezelni, egy olvasási kérést.
* **Megosztott fürtkötetek-** – a fürt megosztott köteteinek (CSV), a feladatátvevő fürt is egyszerre olvasása vagy ugyanarra a lemezre írási több csomópont. Feladatátvétel egyik csomópontjáról egy másik csomópontra fordulhat elő, gyorsan, anélkül, hogy a meghajtó tulajdonosát vagy csatlakoztatási, leválasztásához és eltávolítása a kötet változása. 

> [!IMPORTANT]
> Ne keverje a CSV-k és a CSV a pillanatképeket. CSV-k és CSV-pillanatkép nem támogatott. 
> 
> 

A StorSimple Snapshot Manager használatával állítsa vissza a teljes kötet csoportok vagy az egyes kötetek klónozása és az egyes fájlok helyreállítása.

* [Kötetek és a kötet csoportok](#volumes-and-volume-groups) 
* [Biztonsági mentési típusok és a biztonsági mentési szabályzatok](#backup-types-and-backup-policies) 

A StorSimple Snapshot Manager funkciók és a használatukkal kapcsolatos további információkért lásd: [StorSimple Snapshot Manager felhasználói felületén](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Kötetek és a kötet csoportok
A StorSimple Snapshot Managerrel, akkor hozzon létre köteteket, majd válassza az őket kötet csoportokba. 

A StorSimple Snapshot Manager kötet csoportokat használ, amelyek alkalmazáskonzisztens biztonsági másolatot készíteni. Alkalmazáskonzisztencia létezik, amikor az összes kapcsolódó fájlokat, és adatbázisok szinkronizálása, és a egy alkalmazás egy adott időpontban időben igaz állapotát. Kötet csoportok (más néven minősülő *konzisztenciacsoportok*) alapját a biztonsági mentési vagy visszaállítási feladat.

Kötet csoportok nem azonosak, kötettárolók. A kötettároló tartalmaz egy vagy több kötet, amely egy felhős társzolgáltatás fiókjába, és más jellemzőket, például a titkosítás és a sávszélesség-felhasználás. Egyetlen kötettároló legfeljebb 256 dinamikusan kiosztott StorSimple-köteteket is tartalmazhat. Kötettárolók kapcsolatos további információkért látogasson el [a kötettárolók kezelése](storsimple-manage-volume-containers.md). Kötet csoportok kötetek konfigurált biztonsági mentési műveletek megkönnyítésére gyűjteményei. Különböző kötettárolókhoz tartozó, egy kötetet csoportba helyezi át őket, és majd hozzon létre egy biztonsági mentési szabályzatot, az adott kötet csoporthoz két kötet választása esetén minden olyan kötetre a megfelelő kötetet tárolóban, a megfelelő tárolási fiók használatával kell készíteni.

> [!NOTE]
> Egy kötet csoportban található összes kötetet egyetlen felhőszolgáltató kell származnia.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integráció a Windows a kötet árnyékmásolata szolgáltatás
A StorSimple Snapshot Manager alkalmazáskonzisztens adatok rögzítése a Windows kötet árnyékmásolata szolgáltatás (VSS) használja. VSS segíti az alkalmazáskonzisztencia kommunikálva Kötetárnyékmásolat-felismerésre képes alkalmazások koordinálása a növekményes pillanatképek létrehozása. VSS biztosítja, hogy az alkalmazások ideiglenesen inaktív vagy videokártyának, amikor a pillanatképeket készít. 

A StorSimple Snapshot Manager megvalósítási VSS együttműködik az SQL Server és az általános NTFS-kötetekről. A folyamat a következőképpen történik: 

1. A kérelmező, amely általában egy adatkezelés és adatvédelmi megoldás (például a StorSimple Snapshot Manager) vagy biztonsági mentési alkalmazást, VSS hív meg, és megkérdezi, hogy információkat gyűjtsön a célalkalmazás író szoftverek.
2. VSS kapcsolatba lép az író összetevővel, egy leírást az adatok lekéréséhez. Az író az adatok biztonsági mentése a leírását adja eredményül. 
3. VSS az alkalmazás biztonsági mentésének előkészítése az író jelzi. Az író, az adatok biztonsági mentésének előkészíti a nyitott tranzakciók végrehajtásával, frissítése, a tranzakciós naplókhoz, és így tovább, és értesíti a VSS
4. VSS arra utasítja, hogy az író ideiglenesen állítsa le az alkalmazás adattárak, és győződjön meg arról, hogy nincs adatok írása a kötet árnyékmásolata hozza létre. Ez a lépés biztosítja az adatok konzisztenciáját, és legfeljebb 60 másodpercet vesz igénybe.
5. VSS arra utasítja a szolgáltatót, hogy az árnyékmásolat létrehozása. Szolgáltatók, amely lehet a szoftver vagy hardver-alapú, a köteteket, azokat az árnyékmásolatokat aktuálisan futó és igény szerinti kezelése. A szolgáltató hoz létre az árnyékmásolatot, és a VSS értesíti, ha elkészült.
6. VSS kapcsolatba lép az író értesíti az alkalmazást, amelyet i/o folytathatja, valamint győződjön meg arról, hogy i/o felfüggesztése sikerült árnyékmásolat létrehozása során. 
7. Ha a másolási sikeres volt, a VSS hely a másolása a kérelmező adja vissza. 
8. Ha az adatok írták, amíg az árnyékmásolat létrejött, a biztonsági mentés inkonzisztens lesz. VSS törli az árnyékmásolatot, és értesítést küld a kérelmezőnek. A kérelmező ismételje meg a biztonsági mentési folyamat automatikusan vagy értesítse a rendszergazdát, hogy egy későbbi időpontban újra elindítani.

Tekintse meg az alábbi ábrán is látható.

![VSS-folyamat](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Kötet árnyékmásolata szolgáltatás Windows-folyamat** 

## <a name="backup-types-and-backup-policies"></a>Biztonsági mentési típusok és a biztonsági mentési szabályzatok
A StorSimple Snapshot Managerrel biztonsági másolatokat és tárolására is használható, helyben és a felhőben. A StorSimple Snapshot Managert használhatja a biztonsági másolatokat közvetlenül, vagy egy biztonsági mentési házirend használatával ütemezheti az automatikus biztonsági másolatok készítése. Biztonsági mentési szabályzatok is engedélyezheti, hogy adja meg, hány pillanatfelvételek megmaradnak. 

### <a name="backup-types"></a>Biztonsági mentési típusok
A StorSimple Snapshot Manager segítségével a következő típusú biztonsági másolatok létrehozása:

* **A helyi pillanatképeket** – helyi pillanatképei a StorSimple eszközön tárolt adatmennyiség időponthoz példányban. Általában ilyen típusú biztonsági mentés létrehozott is, és gyorsan vissza. Helyi pillanatképet is használhat, mint egy helyi biztonsági másolat.
* **Felhőalapú pillanatfelvételek** – Felhőbeli pillanatképek a felhőben tárolt adatmennyiség időponthoz példányban. Felhőbeli pillanatkép megegyezik egy másik, külső helyszínen lévő tárolórendszer replikált pillanatképet. Felhőbeli pillanatképek különösen hasznosak a vész-helyreállítási helyzetekben.

### <a name="on-demand-and-scheduled-backups"></a>Igény szerinti és ütemezett biztonsági mentések tiltása
A StorSimple Snapshot Managerrel egy egyszeri biztonsági mentést hozhatók létre azonnal is kezdeményezhető, vagy ütemezhet rendszeres biztonsági mentési műveletek használhatja a biztonsági mentési szabályzat.

Biztonsági mentési szabályzat egy automatikus szabályokat, amelyek segítségével rendszeres biztonsági mentések ütemezéséhez. Biztonsági mentési szabályzat lehetővé teszi a gyakoriság és pillanatképeket készít egy adott kötet csoport paramétereinek megadása. Házirendek segítségével adja meg a kezdő és lejárati dátumok, időpontok, gyakoriságot és adatmegőrzési követelmények, mind a helyi és felhőbeli pillanatképeket. Definiálja azt követően azonnal egy szabályzat érvényes. 

A StorSimple Snapshot Manager segítségével konfigurálhatja, vagy konfigurálja újra a biztonsági mentési házirendeket, amikor szükséges. 

Konfigurálja az egyes biztonsági mentési házirendet hoz létre a következő információkat:

* **Név** – a kiválasztott biztonsági mentési szabályzat egyedi nevét.
* **Típus** – biztonsági mentési szabályzat; helyi pillanatfelvétel és felhőbeli pillanatkép típusa.
* **Kötetcsoport** – a kötet csoport, amely a kijelölt biztonsági mentési szabályzat hozzá van rendelve.
* **Adatmegőrzési** – biztonsági másolatok megőrzése száma. Ha bejelöli a **összes** mezőben minden biztonsági másolatok megmaradnak, amíg a biztonsági másolatok kötetenkénti maximális számának elérésekor, ekkor a szabályzat meghiúsul, és hozzon létre egy hibaüzenet. Másik lehetőségként megadhatja a biztonsági másolatok megőrzése (1 – 64) között számos.
* **Dátum** – a biztonsági mentési szabályzat létrehozásának dátumát.

Biztonsági mentési szabályzatok konfigurálásával kapcsolatos további információkért látogasson el [használata a StorSimple Snapshot Manager hozhat létre, és a biztonsági mentési házirendek kezelése](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Biztonsági mentési feladat figyelése és kezelése
A StorSimple Snapshot Managerrel segítségével figyelheti és közelgő, ütemezett és befejeződött a biztonsági mentési feladatok kezelése. A StorSimple Snapshot Manager emellett egy katalógus, akár 64 befejezett biztonsági mentéseket. A katalógus segítségével megkeresését és helyreállítását a kötetek vagy csak egyes fájlokat. 

Biztonsági mentési feladatok figyelésével kapcsolatos további információkért lépjen [használata a StorSimple Snapshot Manager biztonsági mentési feladatok megtekintése és kezelése a](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Töltse le [a StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

