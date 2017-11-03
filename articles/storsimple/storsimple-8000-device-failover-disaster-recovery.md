---
title: "A feladatátvétel a StorSimple 8000 sorozat eszközeire vész-helyreállítási |} Microsoft Docs"
description: "Útmutató: a StorSimple eszköz magát, egy másik fizikai eszköz vagy egy felhőalapú készülék feladatátvételt."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>A StorSimple 8000 series eszköz a feladatátvételi és katasztrófa-helyreállítás

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple 8000 sorozat eszközeire, és hogyan Ez a szolgáltatás segítségével helyreállítja a StorSimple eszközökhöz, ha katasztrófa történik az eszköz feladatátvétel funkciója. A StorSimple eszköz feladatátvevő használ az adatok áttelepítését egy forrás eszközről az adatközponton belül egy másik figyelt eszköz. Az útmutató a StorSimple 8000 series fizikai eszközöket és a felhő készülékek 3 és újabb verzióit futtató a szoftver verziója frissítés vonatkozik.

StorSimple használja a **eszközök** panelt, és indítsa el az eszköz feladatátvételi szolgáltatás katasztrófa esetén. Ezen a panelen a StorSimple-eszközeinek felsorolását tartalmazza a StorSimple Device Manager szolgáltatáshoz való kapcsolódás.

![Eszközök panel](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Vészhelyreállítás (DR) és az eszköz feladatátvételi

Egy vész-helyreállítási forgatókönyv esetén az elsődleges eszköz nem működik. StorSimple használ, az elsődleges eszköz _forrás_ és a hozzárendelt felhő adatok áthelyezése egy másik _cél_ eszköz. Ezt a folyamatot nevezzük a *feladatátvételi*. Az alábbi ábra mutatja be, a feladatátvételi folyamat.

![Mi történik, az eszköz feladatátvételi?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

A céleszközön feladatátvevő egy fizikai eszköz vagy akár egy felhőalapú készülék lehet. A céleszközön azonos vagy a forráseszközt földrajzi helyen található.

A feladatátvétel során kiválaszthatja áttelepítésre kötettárolók. StorSimple átvált a kötettárolók tulajdonjogát a forráseszközről származó a céleszközt. Miután kötettárolók módosítja tulajdonjogot, StorSimple ezekhez a tárolókhoz a forráseszközről származó törli. A Törlés befejezése után, akkor is feladat-visszavételt a céleszközt. _Feladat-visszavétel_ továbbítja a tulajdonjoga az eredeti forrás eszközt.

### <a name="cloud-snapshot-used-during-device-failover"></a>Felhő-pillanatfelvételt eszköz feladatátvétel során

Követően a vész-Helyreállítási felhő legfrissebb biztonsági másolat szolgál az adatok helyreállítását a cél-eszközre. A felhőalapú pillanatfelvételek további információkért lásd: [manuális biztonsági mentés készítése a StorSimple Device Manager szolgáltatással](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

A StorSimple 8000 Series biztonsági mentési házirendek nincsenek társított biztonsági másolatok. Ha ugyanazon a köteten több biztonsági mentési házirendeket, a StorSimple kötetek legnagyobb száma a biztonsági mentési házirend választja ki. StorSimple a legutóbbi biztonsági mentés a kijelölt biztonsági mentési házirend, majd használja a céleszközön az adatok helyreállítását.

Tegyük fel, hogy két biztonsági mentési házirend, *defaultPol* és *customPol*:

* *defaultPol*: egy kötet *vol1*, napi kezdő pozíció: 10:30 PM futtatja.
* *customPol*: négy kötetek *vol1*, *vol2*, *vol3*, *vol4*, napi kezdő pozíció: 10:00 PM futtatja.

Ebben az esetben a StorSimple előtérbe helyezi az összeomlásbiztos, és használja *customPol* , mert több köteten. Ez a házirend a legfrissebb biztonsági másolat adatok visszaállítására használható. Hogyan hozhatja létre és kezelheti a biztonsági mentési házirendek további információkért látogasson el [a StorSimple Device Manager szolgáltatás segítségével kezelheti a biztonsági mentési házirendeket](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Eszköz feladatátvételi a gyakori szempontok

Egy eszköz átadása, áttekintheti a következőket:

* Egy eszköz feladatátvétel megkezdése előtt kötettárolók belül minden kötet offline állapotban kell lennie. Egy nem tervezett feladatátvétel StotSimple kötetek lesz automatikusan kapcsolat nélkül. De (DR vizsgálandó) egy tervezett feladatátvételt hajt végre, ha a kötetek offline állapotba kell végrehajtania.
* Csak a kötet-tárolókban, amelyek rendelkeznek a hozzárendelt felhő-pillanatfelvételt vész-Helyreállítási fel vannak sorolva. Legalább egy kötettárolót szeretne adatokat helyreállítani egy hozzárendelt felhő pillanatképpel kell lennie.
* Ha vannak, amelyek több kötettárolók eloszthatja felhőalapú pillanatfelvételek, StorSimple feladatátadás ezek kötettárolók készletként. A ritka esetben ha, amely több kötettárolók eloszthatja helyi pillanatképek vannak, de a hozzárendelt felhő pillanatképek nem, StorSimple átadja a helyi pillanatképeket, és a helyi adatok nem vesztek el vész-Helyreállítási után.
* Az elérhető célkiszolgálók Dr tartoznak eszközökre, amelyeken elegendő hely a lemezen a kijelölt kötettárolók tárolására. Azokat az eszközöket, amelyeken nincs elegendő lemezterület nem találhatók cél eszközként.
* Után egy DR (egy korlátozott ideig) a adatelérési teljesítményét hatással lehet jelentősen, mivel az adatokat a felhőből, és helyileg tárolja el azt újra az eszközt.

#### <a name="device-failover-across-software-versions"></a>Eszköz feladatátvételi szoftver verziója között

Előfordulhat, hogy egy központi telepítésben lévő StorSimple Device Manager szolgáltatás több eszközön, mindkét fizikai és a felhőben, az összes futó különböző szoftververziók.

Az alábbi táblázat segítségével határozza meg, ha a feladatátvétel, vagy vissza egy másik futtató eszközre, az egy másik szoftver verziója és a Vészhelyreállítás során a kötet típusok működése sikertelen lesz.

#### <a name="failover-and-failback-across-software-versions"></a>A feladatátvételi és a feladat-visszavétel szoftver verziója között

| Feladatok átadása a / ból | Fizikai eszköz | Felhőalapú készülék |
| --- | --- | --- |
| 4. frissítés 3 frissítése |Rétegzett kötetek rétegzett feletti sikertelen. <br></br>Helyileg rögzített kötetekhez átadja a helyileg rögzített. <br></br> Amikor pillanatképet készít az Update 4 eszközön, a következő feladatátvétel [heatmap-alapú nyomkövetési](storsimple-update4-release-notes.md#whats-new-in-update-4) lép működésbe. |Helyileg rögzített, rétegzett kötetek feladatait. |
| 4 3 frissítése. frissítés |Rétegzett kötetek rétegzett feletti sikertelen. <br></br>Helyileg rögzített kötetekhez átadja a helyileg rögzített. <br></br> Biztonsági mentések visszaállítására használt heatmap metaadatainak megőrzése mellett. <br></br>Nyomkövetési Heatmap-alapú frissítés 3. a feladat-visszavétel a következő nem érhető el. |Helyileg rögzített, rétegzett kötetek feladatait. |


## <a name="device-failover-scenarios"></a>Eszköz feladatátvételi forgatókönyvek

Ha egy olyan vészhelyzet esetén, kiválaszthatja, hogy áthelyezze a feladatokat a StorSimple eszköz:

* [A fizikai eszköz](storsimple-8000-device-failover-physical-device.md).
* [Saját magára](storsimple-8000-device-failover-same-device.md).
* [A felhő berendezésen](storsimple-8000-device-failover-cloud-appliance.md).

A fenti cikk ismerteti részletesen az egyes feladatátvevő esetben.


## <a name="failback"></a>Feladat-visszavétel

Az Update 3 és újabb verziókban a StorSimple feladat-visszavétel is támogatja. Feladat-visszavétel a feladatátvétel csak a névkeresési, a cél válik a forrás és az eredeti forrás most a feladatátvétel során válik a céleszközt. 

A feladat-visszavétel során StorSimple újra szinkronizálja az adatokat vissza az elsődleges hely, megállt az i/o és alkalmazás tevékenység, és átmenetek biztonsági másolatot az eredeti helyre.

A feladatátvétel befejeződött, a StorSimple a következő műveleteket hajtja végre:

* StorSimple feladatátvétel történt a forráseszközről származó kötettárolók törlése.
* StorSimple / kötettároló (keresztül) az eszközön nem sikerült a forrás egy háttérben futó feladatot indít. Ha úgy próbálja ismét, amíg folyamatban van a feladat sikertelen lesz, hogy figyelmet értesítést kap. Várjon, amíg a folyamat befejeződik, a feladat-visszavétel elindításához.
* Kötettárolók törlésének befejezéséhez szükséges idő számos tényező befolyásolja, például az adatok mennyiségét, a művelet az adatok, a biztonsági másolatok számát és a rendelkezésre álló sávszélesség életkor függ.

Ha tervezi, feladatátvételi teszteket, vagy visszavételek teszteli, azt javasoljuk, hogy tesztelje kötettárolók a kevesebb adat (GB). Általában megkezdheti a feladat-visszavétel 24 óra, a feladatátvétel befejezése után.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Q. **Mi történik, ha a vész-Helyreállítási meghibásodik, vagy részleges sikeres rendelkezik?**

A. A vész-Helyreállítási sikertelen lesz, azt javasoljuk, hogy újból megpróbálná. A második eszköz feladatátvételi feladatban tudomást az első feladat előrehaladását, és ettől a ponttól kezdve kezdődik.

Q. **Törölhetek egy eszközt, amíg az eszköz feladatátvétel van folyamatban?**

A. Egy eszköz nem törölhető, amíg folyamatban van egy vész-Helyreállítási. Az eszköz csak akkor törölni, a vész-Helyreállítási befejeződése után. Az eszköz feladatátvételi feladat előrehaladását a figyelheti a **feladatok** panelen.

Q. **Ha nem a szemétgyűjtés indítsa el a forrás-eszközön, hogy a forrás-eszközökön a helyi adatok törlése?**

A. A szemétgyűjtés a forrás eszközön engedélyezve van, csak azt követően az eszköz teljesen karbantartása. A karbantartás tartalmazza, amelyek nem tudták keresztül például kötetek, a biztonsági másolat objektumok (adatokat nem), a kötettárolók és a házirendek a forráseszközről származó objektumok törlése.

Q. **Mi történik, ha a forrás eszköz kötettárolók társított törlése feldolgozás sikertelen?**

A.  Ha a törlési feladat sikertelen lesz, majd manuálisan törölheti a kötettárolók. Az a **eszközök** panelen válassza ki a forrás eszközt, és kattintson a **kötettárolók**. Jelölje ki azt a kötettárolók keresztül, és a panel alján található sikertelen **törlése**. A törölt összes a sikertelen keresztül kötettárolók a forrás-eszközön, indítsa el a feladat-visszavételre. További információkért látogasson el [kötettároló törlése](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzleti folytonosság vészhelyreállítási (BCDR)

Üzleti folytonossági (BCDR) vészhelyreállítás akkor fordul elő, ha a teljes Azure-adatközpontban leáll a működése. Ebben a forgatókönyvben hatással lehet a StorSimple eszköz Manager szolgáltatás és a kapcsolódó StorSimple eszközökhöz.

Ha a StorSimple eszköz regisztrálták előtt történt egy olyan vészhelyzet esetén, majd ezt az eszközt kell végezni a gyári beállítások visszaállítása. A katasztrófa utáni a StorSimple eszköz megjelennek az Azure portálon offline állapotúként. Ez az eszköz törlését a portálról. A gyári beállításokat visszaállítani az eszközt, és regisztrálja újra a szolgáltatást.

## <a name="next-steps"></a>Következő lépések

Ha készen áll a eszköz feladatátvétel végrehajtásához, részletes utasításokat a következő forgatókönyvek közül választhat:

- [Feladatok átadása egy másik fizikai eszköz](storsimple-8000-device-failover-physical-device.md)
- [Feladatok átadása a ugyanarra az eszközre](storsimple-8000-device-failover-same-device.md)
- [Feladatok átadása a StorSimple felhő készülék](storsimple-8000-device-failover-cloud-appliance.md)

Ha az eszköz rendelkezik feladatátvételt, válasszon az alábbi lehetőségek közül:

* [Inaktiválja vagy törölje a StorSimple eszköz](storsimple-8000-deactivate-and-delete-device.md).
* [A StorSimple eszköz felügyelete a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

