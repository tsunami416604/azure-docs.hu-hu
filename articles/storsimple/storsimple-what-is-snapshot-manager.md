---
title: Mi az a StorSimple Snapshot Manager? | Microsoft Docs
description: A StorSimple Snapshot Manager, annak architektúrája és szolgáltatásaiismertet.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: twooley
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e58e2d929dd1e4db16ce495ad54045e9dc3a6fb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267416"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Bevezetés a StorSimple Snapshot Manager bemutatása

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely leegyszerűsíti az adatvédelmet és a biztonsági mentés kezelését egy Microsoft Azure StorSimple környezetben. A StorSimple Snapshot Manager segítségével egyetlen integrált tárolási megoldásként kezelheti a Microsoft Azure StorSimple-adatokat az adatközpontban és a felhőben, így egyszerűsítve a biztonsági mentési folyamatokat és csökkentve a költségeket.

Ez az áttekintés bemutatja a StorSimple Snapshot Manager, ismerteti a funkciókat, és elmagyarázza a szerepét a Microsoft Azure StorSimple. 

A teljes Microsoft Azure StorSimple rendszer áttekintését, beleértve a StorSimple-eszközt, a StorSimple Manager szolgáltatást, a StorSimple Snapshot Managert és a SharePoint-alapú StorSimple adaptert, lásd: [StorSimple 8000 sorozat: hibrid felhőalapú tárolási megoldás.](storsimple-overview.md) 

> [!NOTE]
> * A StorSimple Snapshot Manager nem használható a Microsoft Azure StorSimple virtuális tömbök (más néven helyszíni StorSimple virtuális eszközök) kezelésére.
> * Ha a StorSimple Update 2-t a StorSimple eszközre kívánja telepíteni, a StorSimple Snapshot Manager legújabb verzióját töltse le, és telepítse azt **a StorSimple Update 2 telepítése előtt.** A StorSimple Snapshot Manager legújabb verziója visszamenőlegesen kompatibilis, és a Microsoft Azure StorSimple összes kiadott verziójával működik. Ha a StorSimple Snapshot Manager korábbi verzióját használja, frissítenie kell azt (az új verzió telepítése előtt nem kell eltávolítania az előző verziót).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>StorSimple Snapshot Manager célja és architektúrája
A StorSimple Snapshot Manager egy központi felügyeleti konzolt biztosít, amely segítségével konzisztens, időponthoz közvetlenül megfelelő biztonsági másolatokat hozhat létre a helyi és a felhőbeli adatokról. A konzol segítségével például a következőkre használhatja:

* Kötetek konfigurálása, biztonsági és törlése.
* A kötetcsoportok konfigurálásával győződjön meg arról, hogy a biztonsági másolatok adatai alkalmazáskonzisztensek.
* A biztonsági mentési házirendek kezelése, hogy az adatokról egy előre meghatározott ütemezés szerint készüljön biztonsági másolat.
* Hozzon létre helyi és felhőbeli pillanatképeket, amelyek a felhőben tárolhatók, és vész-helyreállítási célokra használhatók.

A StorSimple Snapshot Manager lekéri a gazdagép VSS-szolgáltatójánál regisztrált alkalmazások listáját. Ezután alkalmazáskonzisztens biztonsági mentések létrehozásához ellenőrzi az alkalmazás által használt köteteket, és javasolja a kötetcsoportok konfigurálását. A StorSimple Snapshot Manager ezeket a kötetcsoportokat használja az alkalmazáskonzisztens biztonsági másolatok létrehozásához. (Az alkalmazáskonzisztencia akkor áll fenn, ha az összes kapcsolódó fájl és adatbázis szinkronizálva van, és az alkalmazás valódi állapotát jelöli egy adott időpontban.) 

StorSimple Snapshot Manager biztonsági mentések formájában növekményes pillanatképek, amelyek rögzítik csak a változásokat, mivel az utolsó biztonsági mentés. Ennek eredményeképpen a biztonsági mentések kevesebb tárhelyet igényelnek, és gyorsan létrehozhatók és visszaállíthatók. A StorSimple Snapshot Manager a Windows kötet árnyékmásolata szolgáltatás (VSS) használatával biztosítja, hogy a pillanatképek alkalmazáskonzisztens adatokat rögzítsenek. (További információt az Integráció a Windows kötet árnyékmásolata szolgáltatással című szakaszban talál.) A StorSimple Snapshot Manager segítségével biztonsági mentési ütemezéseket hozhat létre, vagy szükség szerint azonnali biztonsági mentéseket készíthet. Ha biztonsági másolatból kell visszaállítania az adatokat, a StorSimple Snapshot Manager lehetővé teszi a helyi vagy felhőbeli pillanatképek katalógusából való kiválasztást. Az Azure StorSimple csak azokat az adatokat állítja vissza, amelyekre szükség van, ami megakadályozza az adatok rendelkezésre állásának késleltetését a visszaállítási műveletek során.)

![StorSimple Snapshot Manager architektúra](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager architektúra** 

## <a name="support-for-multiple-volume-types"></a>Több kötettípus támogatása
A StorSimple Snapshot Manager segítségével konfigurálhatja és biztonsági másolatot készíthet a következő típusú kötetekről: 

* **Alapkötetek** – Az alapkötet egyetlen partíció az alaplemezen. 
* **Egyszerű kötetek** – Az egyszerű kötet olyan dinamikus kötet, amely egyetlen dinamikus lemezlemezlemezterületét tartalmazza. Egy egyszerű kötet egyetlen területből áll egy lemezen, vagy több olyan területből, amelyek ugyanazon a lemezen vannak összekötve. (Egyszerű köteteket csak dinamikus lemezeken hozhat létre.) Az egyszerű kötetek nem hibatűrők.
* **Dinamikus kötetek** – A dinamikus kötet dinamikus lemezen létrehozott kötet. A dinamikus lemezek adatbázis segítségével követik nyomon a számítógép dinamikus lemezein található kötetek adatait. 
* **Dinamikus kötetek tükrözéssel** – A dinamikus kötetek tükrözéssel a RAID 1 architektúrára épülnek. A RAID 1 esetében azonos adatok at írnak két vagy több lemezre, tükrözött készletet hozva létre. Az olvasási kérelmet ezután bármely olyan lemez kezelni tudja, amely a kért adatokat tartalmazza.
* **Fürtmegosztott kötetek** – Fürt megosztott kötetek (CSV-k) esetén a feladatátvevő fürt több csomópontja egyszerre olvashat vagy írhat ugyanarra a lemezre. Az egyik csomópontról a másikra történő feladatátvétel gyorsan bekövetkezhet anélkül, hogy a meghajtó tulajdonjogának vagy csatlakoztatásának, leválasztásának és eltávolításának megkellene változtatnia. 

> [!IMPORTANT]
> Ne keverje a csv-ket és a nem csv-ket ugyanabban a pillanatképben. A csvv-k és a nem csv-k keverése egy pillanatképben nem támogatott. 
> 
> 

A StorSimple Snapshot Manager segítségével teljes kötetcsoportokat állíthat vissza, vagy klónozhat egyes köteteket, és helyreállíthatja az egyes fájlokat.

* [Kötetek és kötetcsoportok](#volumes-and-volume-groups) 
* [Biztonsági mentési típusok és biztonsági mentési házirendek](#backup-types-and-backup-policies) 

A StorSimple Snapshot Manager szolgáltatásairól és azok használatáról a [StorSimple Snapshot Manager felhasználói felülete](storsimple-use-snapshot-manager.md)című témakörben olvashat bővebben.

## <a name="volumes-and-volume-groups"></a>Kötetek és kötetcsoportok
A StorSimple Snapshot Manager segítségével köteteket hozhat létre, majd konfigurálhatja őket kötetcsoportokká. 

A StorSimple Snapshot Manager kötetcsoportokat használ az alkalmazáskonzisztens biztonsági másolatok létrehozásához. Az alkalmazáskonzisztencia akkor áll fenn, ha az összes kapcsolódó fájl és adatbázis szinkronizálva van, és egy alkalmazás valódi állapotát jelöli egy adott időpontban. A kötetcsoportok (más néven *konzisztenciacsoportok)* egy biztonsági mentési vagy visszaállítási feladat alapját képezik.

A kötetcsoportok nem egyeznek meg a kötettárolókéval. A kötettároló egy vagy több kötetet tartalmaz, amelyek felhőalapú tárfiókot és más attribútumokat, például titkosítást és sávszélesség-felhasználást tartalmaznak. Egy kötettároló legfeljebb 256 vékonyan kiépített StorSimple-kötetet tartalmazhat. A kötettárolókról a [Kötettárolók kezelése című](storsimple-manage-volume-containers.md)részről talál további információt. A kötetcsoportok olyan kötetek gyűjteményei, amelyeket a biztonsági mentési műveletek megkönnyítése érdekében konfigurál. Ha két kötetet választ ki, amelyek különböző kötettárolókhoz tartoznak, helyezze őket egyetlen kötetcsoportba, majd hozzon létre egy biztonsági mentési házirendet az adott kötetcsoporthoz, minden kötetről a megfelelő kötettárolóban készül a megfelelő tárfiók használatával.

> [!NOTE]
> A kötetcsoport ban lévő összes kötetnek egyetlen felhőszolgáltatótól kell származnia.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integráció a Windows kötet árnyékmásolat-szolgáltatásával
A StorSimple Snapshot Manager a Windows kötet árnyékmásolata szolgáltatás (VSS) segítségével rögzíti az alkalmazáskonzisztens adatokat. A VSS megkönnyíti az alkalmazások konzisztenciáját azáltal, hogy kommunikál a VSS-t támogató alkalmazásokkal a növekményes pillanatképek létrehozásának koordinálása érdekében. A VSS biztosítja, hogy az alkalmazások ideiglenesen inaktívak vagy nyugalmi állapotban legyenek, amikor pillanatképek készülnek. 

A VSS StorSimple Snapshot Manager implementációja sql server és általános NTFS-kötetekkel működik. A folyamat a következő: 

1. A kérelmező, amely általában egy adatkezelési és védelmi megoldás (például StorSimple Snapshot Manager) vagy egy biztonsági másolat készítő alkalmazás, meghívja a VSS-t, és arra kéri, hogy gyűjtsön információkat az író szoftvertől a célalkalmazásban.
2. A VSS kapcsolatba lép az író összetevővel az adatok leírásának lekéréséhez. Az író visszaadja a biztonsági másolatot készítő adatok leírását. 
3. A VSS jelzi az írónak, hogy készítse elő az alkalmazást a biztonsági mentésre. Az író előkészíti az adatokat a biztonsági mentésre a nyitott tranzakciók kitöltésével, a tranzakciónaplók frissítésével és így tovább, majd értesíti a VSS-t.
4. A VSS arra utasítja az írót, hogy ideiglenesen állítsa le az alkalmazás adattárait, és győződjön meg arról, hogy az árnyékmásolat létrehozása közben nem ír adatokat a kötetre. Ez a lépés biztosítja az adatok konzisztenciáját, és legfeljebb 60 másodpercet vesz igénybe.
5. A VSS arra utasítja a szolgáltatót, hogy hozza létre az árnyékmásolatot. A szolgáltatók, amelyek lehetnek szoftver- vagy hardveralapúak, kezelik a jelenleg futó köteteket, és igény szerint hoznak létre árnyékmásolatokat róluk. A szolgáltató létrehozza az árnyékmásolatot, és értesíti a VSS-t, ha befejeződött.
6. A VSS kapcsolatba lép az íróval, hogy értesítse az alkalmazást, hogy az I/O folytatható, és ellenőrizze, hogy az I/O sikeresen szünetelt-e az árnyékmásolat létrehozása során. 
7. Ha a másolat sikeres volt, a VSS visszaadja a másolat helyét a kérelmezőnek. 
8. Ha az adatok az árnyékmásolat létrehozása közben készültek, akkor a biztonsági másolat inkonzisztens lesz. A VSS törli az árnyékmásolatot, és értesíti a kérelmezőt. A kérelmező vagy automatikusan megismétli a biztonsági mentési folyamatot, vagy értesíti a rendszergazdát, hogy később próbálja meg újra.

Lásd az alábbi ábrát.

![VSS folyamat](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Windows kötet árnyékmásolata szolgáltatás folyamata** 

## <a name="backup-types-and-backup-policies"></a>Biztonsági mentési típusok és biztonsági mentési házirendek
A StorSimple Snapshot Manager segítségével biztonsági másolatot készíthet az adatokról, és tárolhatja azokat helyileg és a felhőben. A StorSimple Snapshot Manager segítségével azonnal biztonsági másolatot készíthet az adatokról, vagy biztonsági mentési szabályzattal létrehozhat egy ütemezést a biztonsági mentések automatikus készítéséhez. A biztonsági mentési házirendek azt is lehetővé teszik, hogy adja meg, hogy hány pillanatkép marad meg. 

### <a name="backup-types"></a>Biztonsági másolat típusai
A StorSimple Snapshot Manager segítségével a következő típusú biztonsági mentések hozhat létre:

* **Helyi pillanatképek** – A helyi pillanatképek a StorSimple-eszközön tárolt kötetadatok időponthoz fértői. Az ilyen típusú biztonsági mentés általában gyorsan létrehozható és visszaállítható. A helyi pillanatképet úgy is használhatja, mint egy helyi biztonsági másolat.
* **Felhőbeli pillanatképek** – A felhőbeli pillanatképek a felhőben tárolt kötetadatok időponthoz fért őrei. A felhőbeli pillanatkép egy másik, telephelyen kívüli tárolórendszeren replikált pillanatképnek felel meg. A felhőbeli pillanatképek különösen hasznosak a vész-helyreállítási forgatókönyvekben.

### <a name="on-demand-and-scheduled-backups"></a>Igény szerinti és ütemezett biztonsági mentések
A StorSimple Snapshot Manager, kezdeményezhet egy egyszeri biztonsági mentést kell létrehozni azonnal, vagy használhatja a biztonsági mentési szabályzat ütemezésére ismétlődő biztonsági mentési műveleteket.

A biztonsági mentési szabályzat automatikus szabályok készlete, amely rendszeres biztonsági mentések ütemezéséhez használható. A biztonsági mentési szabályzat lehetővé teszi, hogy meghatározza a gyakoriságát és paramétereit egy adott kötetcsoport pillanatképeinek készítéséhez. Szabályzatok segítségével megadhatja a kezdő és lejárati dátumok, időpontok, gyakoriságok és megőrzési követelmények, mind a helyi és a felhőbeli pillanatképek. A házirend et a definiálás után azonnal alkalmazza a rendszer. 

A StorSimple Snapshot Manager segítségével szükség esetén konfigurálhatja vagy újrakonfigurálhatja a biztonsági mentési házirendeket. 

A következő információkat konfigurálhatja minden egyes létrehozott biztonságimentési házirendhez:

* **Név** – A kijelölt biztonsági mentési házirend egyedi neve.
* **Típus** – a biztonsági mentési házirend típusa; helyi pillanatkép vagy felhőbeli pillanatkép.
* **Kötetcsoport** – az a kötetcsoport, amelyhez a kijelölt biztonsági mentési házirend hozzá van rendelve.
* **Megőrzés** – A megőrzésre kerülő biztonsági másolatok száma. Ha bejelöli a **Mind** jelölőnégyzetet, az összes biztonsági másolat megmarad, amíg el nem éri a kötetenkénti biztonsági másolatok maximális számát, ekkor a házirend sikertelen lesz, és hibaüzenetet generál. Azt is megteheti, hogy megad néhány megőrzésre váró biztonsági mentést (1 és 64 között).
* **Dátum** – A biztonsági mentési házirend létrehozásának dátuma.

A biztonsági mentési házirendek konfigurálásáról a [StorSimple Snapshot Manager használata biztonsági mentési házirendek létrehozásához és kezeléséhez](storsimple-snapshot-manager-manage-backup-policies.md)című témakörben olvashat.

### <a name="backup-job-monitoring-and-management"></a>Biztonsági mentési feladat figyelése és kezelése
A StorSimple Snapshot Manager segítségével figyelheti és kezelheti a közelgő, ütemezett és befejezett biztonsági mentési feladatokat. Emellett a StorSimple Snapshot Manager legfeljebb 64 befejezett biztonsági mentésből álló katalógust biztosít. A katalógus segítségével köteteket vagy egyes fájlokat kereshet és állíthat vissza. 

A biztonsági mentési feladatok figyeléséről a [StorSimple Snapshot Manager használata a biztonsági mentési feladatok megtekintéséhez és kezeléséhez](storsimple-snapshot-manager-manage-backup-jobs.md)című információért.

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple Snapshot Manager használatával a StorSimple megoldás felügyeletéről.](storsimple-snapshot-manager-admin.md)
* Letöltés [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

