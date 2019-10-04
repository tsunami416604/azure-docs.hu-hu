---
title: A feladatátvétel a StorSimple 8000-es sorozatú eszközökön vész-helyreállítási |} A Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt magát, egy másik fizikai eszközt vagy a felhőalapú berendezés a StorSimple-eszköz.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60576372"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Feladatátvétel és vészhelyreállítás a StorSimple 8000 sorozatú eszköz helyreállítása

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a StorSimple 8000 sorozatú eszközöket, és hogyan Ez a funkció segítségével helyreállítja a StorSimple-eszközök, ha katasztrófa történik az eszköz feladatátvételi funkciót. A StorSimple eszköz feladatátvételi használ az adatok áttelepítését az adatközpontban a forráseszközről származó másik céleszközt. Ebben a cikkben leírtakat a StorSimple 8000 sorozatú fizikai eszközök és a felhőalapú berendezések szoftververziók Update 3 és újabb verzióit futtató vonatkozik.

StorSimple használja a **eszközök** panelen egy esetleges vészhelyzet esetén az eszköz feladatátvételi szolgáltatás elindításához. A panel felsorolja a StorSimple-Eszközkezelő szolgáltatáshoz csatlakozó összes StorSimple eszközt.

![Eszközök panelen](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Vészhelyreállítás (DR) és az eszköz feladatátvétele

A vész-helyreállítási helyzetekre az elsődleges eszköz leáll a működése. A StorSimple használja az elsődleges eszköz _forrás_ és a felhőbeli adatok áthelyezése egy másik _cél_ eszköz. Ezt a folyamatot nevezzük a *feladatátvételi*. A következő ábra szemlélteti a feladatátvételi folyamat.

![Mi történik, az eszköz feladatátvételi?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

A céleszközön a feladatátvétel lehet fizikai eszközt vagy akár a felhőalapú berendezés. A céleszköz azonos, vagy egy másik földrajzi helyen található, mint a forráseszközt található.

A feladatátvétel során kiválaszthatja áttelepítésre kötettárolók. A StorSimple majd változik ezen kötettárolók tulajdonjogát a forráseszközről származó a céleszközön. Kötettárolók tulajdonjogai módosulnak, miután a StorSimple tárolók törlése a forráseszközről származó. A Törlés befejezése után visszaadhatja a céleszközön. _Feladat-visszavétel_ vissza az eredeti eszköz tulajdonjogát továbbítja.

### <a name="cloud-snapshot-used-during-device-failover"></a>Eszköz-feladatátvétel során használt felhőbeli pillanatkép

A Vészhelyreállítás, az alábbi a legutóbbi felhőbeli biztonsági mentés segítségével állítsa vissza az adatokat a céleszköznek. A felhőbeli pillanatképekkel további információkért lásd: [manuális biztonsági mentés készítése a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

A StorSimple 8000-es sorozat, a biztonsági mentési szabályzatok társítva a biztonsági mentések. Ha több biztonsági mentési szabályzatok ugyanazt a kötetet, StorSimple kiválasztja a legnagyobb számú kötetet a biztonsági mentési szabályzat. A StorSimple a kiválasztott biztonsági mentési házirend a legfrissebb biztonsági másolat használja fel az adatok a céleszközön.

Tegyük fel, hogy két biztonsági mentési házirend, *defaultPol* és *customPol*:

* *defaultPol*: Egy kötet *vol1*, 10:30 = 1997031213 napi díjért futtatja.
* *customPol*: Négy kötetek *vol1*, *vol2*, *vol3*, *vol4*, 10:00 Órakor napi díjért futtatja.

Ebben az esetben a StorSimple priorizálja az összeomlás-konzisztens, és használja *customPol* , mert több köteten. A legutóbbi biztonsági mentés a szabályzat szolgál az adatok helyreállítását. Hogyan hozhat létre, és a biztonsági mentési házirendek kezelése további információkért látogasson el [biztonsági mentési házirendek kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Az eszköz feladatátvételéhez általános szempontok

Mielőtt átadja a feladatokat egy eszközt, tekintse át a következő információkat:

* Egy eszköz-feladatátvétel megkezdése előtt kötettárolók belül minden kötet offline állapotban kell lennie. Egy nem tervezett feladatátvétel StotSimple kötetek automatikusan megnyitja offline állapotban van. Azonban ha (a tesztelje a DR) egy tervezett feladatátvételt végez, végre kell hajtania minden kötet offline.
* Csak kötettárolók, tartozik felhőbeli pillanatkép DR fel vannak sorolva. Adatok helyreállítása egy felhőbeli pillanatképpel legalább egy kötettárolót kell lennie.
* Ha több kötettárolóba fedik felhőbeli pillanatképekkel, StorSimple átadja a feladatokat a kötettárolók készletként. Ritka esetben, ha vannak a helyi pillanatképet, amelyekre ívelhet át több több kötettárolóba, de a felhőbeli pillanatképek nem, StorSimple átadja a feladatokat a helyi pillanatképek és a helyi adatok elvesznek DR után.
* A rendelkezésre álló Céleszközök vészhelyreállítás olyan eszközöket, amelyek áll rendelkezésre elegendő lemezterület a kijelölt kötettárolók befogadásához. Céleszközök nem szerepel minden olyan eszközök, amelyek nem rendelkeznek elegendő lemezterület.
* A DR-(esetében korlátozott időtartamra) után az adatelérési teljesítményét érintheti, jelentősen, az eszköznek kell elérni az adatokat a felhőben és helyileg tárolja.

#### <a name="device-failover-across-software-versions"></a>Eszköz feladatátvételi szoftver verziója között

Előfordulhat, hogy a központi telepítés egy StorSimple-Eszközkezelő szolgáltatás több eszközön, mindkét fizikai és a felhőben, az összes futó eltérő szoftververzió.

A következő táblázat segítségével határozza meg, ha átadja a feladatokat, vagy a feladat-visszavételhez egy másik szoftver verziója és a Vészhelyreállítás során a kötettípusok működése fut egy másik eszközt.

#### <a name="failover-and-failback-across-software-versions"></a>Feladatátvétel és feladat-visszavétel szoftver verziója között

| Feladatátvétel / feladat-visszavétel ból | Fizikai eszköz | Felhőalapú készülék |
| --- | --- | --- |
| Update 3-4-es frissítés |Rétegzett kötetek feladatátvételét a rétegzett sikertelen. <br></br>A gyors helyi kötetek feladatátvételét a helyileg rögzített. <br></br> Készítsen pillanatképet az Update 4 eszközre, amikor a következő feladatátvétel [intenzitástérkép-alapú követési](storsimple-update4-release-notes.md#whats-new-in-update-4) lép működésbe. |Helyileg rögzítve, a rétegzett kötetek feladatait. |
| A 3-as frissítés 4-es frissítés |Rétegzett kötetek feladatátvételét a rétegzett sikertelen. <br></br>A gyors helyi kötetek feladatátvételét a helyileg rögzített. <br></br> Visszaállítható biztonsági másolatok megőrzése intenzitástérkép metaadatait. <br></br>A következő feladat-visszavétel Update 3 Intenzitástérkép-alapú követési nem érhető el. |Helyileg rögzítve, a rétegzett kötetek feladatait. |


## <a name="device-failover-scenarios"></a>Eszközök feladatátvétel esetén

Katasztrófa esetén dönthet úgy, hogy átadja a feladatokat a StorSimple-eszköz:

* [Egy fizikai eszközre történő](storsimple-8000-device-failover-physical-device.md).
* [Saját maga](storsimple-8000-device-failover-same-device.md).
* [A felhőalapú berendezés](storsimple-8000-device-failover-cloud-appliance.md).

A fenti cikk ismerteti részletesen a fenti feladatátvételi esetek mindegyike esetében.


## <a name="failback"></a>Feladat-visszavétel

Az Update 3 és újabb verziókban a StorSimple is támogatja a feladat-visszavétel. Feladat-visszavétel csak feladatátvételi fordítva, a cél válik az adatforrás és az eredeti forrás most már a feladatátvétel során válik a céleszközön. 

A feladat-visszavétel során a StorSimple újra szinkronizálja az adatokat az elsődleges helyen, akad az i/o és alkalmazás-tevékenységet, és átmenetek biztonsági másolatot az eredeti helyre.

A feladatátvétel befejezése után a StorSimple a következő műveleteket hajtja végre:

* A StorSimple, melyek feladatai át a forráseszközről származó kötettárolók törlése.
* StorSimple kezdeményezi a háttérfeladat (sikertelen keresztül) a forrás-eszközön kötetet tárolónként. Ha megpróbálja újra, amíg folyamatban van a feladat sikertelen lesz, erről értesítést kap. Várjon, amíg a feladat befejeződött, indítsa el a feladat-visszavétel.
* A kötettároló törlésének befejezéséhez szükséges idő számos tényező befolyásolja, például az adatok mennyiségét, a művelet az adatokat, biztonsági másolatok számát és a rendelkezésre álló hálózati sávszélesség életkor függ.

Ha tervezi, feladatátvételi teszteket, illetve visszavételek tesztelése, azt javasoljuk, akkor tesztelje a kötettárolók a kevesebb adat (GB). Általában elindíthatja a feladat-visszavételi 24 órával a feladatátvétel befejezése után.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. **Mi történik, ha a DR meghibásodik vagy részben sikeres rendelkezik?**

A. Ha nem sikerül a DR, azt javasoljuk, hogy, próbálkozzon újra. A második eszköz feladatátvételi feladat felismeri az első feladat előrehaladását, és ettől kezdve elindul.

K. **Törölhetem egy eszközt, amíg folyamatban van az eszköz feladatátvételi?**

A. Egy eszköz nem törölhető, amíg folyamatban van egy DR. A DR befejeződése után az eszköz csak törölheti. Az eszköz feladatátvételi feladatok előrehaladásának figyelheti a **feladatok** panelen.

K. **Mikor nem a szemétgyűjtés először a forrás-eszközön, hogy a forrás helyi adatok törlődik?**

A. A szemétgyűjtés engedélyezve van a forráseszközt csak azt követően az eszköz teljes egészében törlődik. A karbantartás tartalmaz objektumokat, például kötetek, a biztonsági mentési objektumokat (adatokat nem), a kötettárolók és a szabályzatok a forráseszközről származó feladatátadása karbantartása.

K. **Mi történik, ha a társított eszköz a kötettárolók törlése feladat sikertelen volt?**

A.  Ha a törlési feladat sikertelen, akkor a kötettárolók manuálisan törölheti. Az a **eszközök** panelen válassza ki a forráseszközt, majd kattintson a **kötettárolók**. Válassza ki a kötettárolókat, amely felett, és a panel alján található nem sikerült, kattintson **törlése**. Összes törlése után a sikertelen keresztül az eszköz kötettárolók, indítsa el a feladat-visszavétel. További információért ugorjon [törölni a kötettárolót](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzleti folytonosság – vészhelyreállítás (BCDR)

Egy üzleti folytonossági vészhelyreállítási (BCDR) forgatókönyv akkor fordul elő, amikor a teljes Azure-adatközpont leáll a működése. Ebben a forgatókönyvben a StorSimple-Eszközkezelő szolgáltatás és az ahhoz tartozó StorSimple-berendezések hatással lehet.

Ha a StorSimple eszköz regisztrálva lett, csak egy esetleges bekövetkezése előtt, majd az eszköz kell végezni a gyári beállítások visszaállítását. A vészhelyzet után a StorSimple-eszköz megjelenik-e az Azure Portalon offline állapotúként. Ez az eszköz törlését a portálról. Az eszköz visszaállítása a gyári beállításokat, és regisztrálja újra a szolgáltatással.

## <a name="next-steps"></a>További lépések

Ha egy eszközön feladatátvétel végrehajtásához készen áll, részletes utasításokat az alábbi eseteket ajánlott közül választhat:

- [Átadja a feladatokat egy másik fizikai eszköz](storsimple-8000-device-failover-physical-device.md)
- [Feladatátvétel ugyanarra az eszközre](storsimple-8000-device-failover-same-device.md)
- [A StorSimple Cloud Appliance átadása](storsimple-8000-device-failover-cloud-appliance.md)

Ha az eszköz feladatátvétel az alábbi lehetőségek közül választhat:

* [A StorSimple-eszköz törölheti vagy inaktiválhatja](storsimple-8000-deactivate-and-delete-device.md).
* [A StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

