---
title: Feladatátvétel és vészhelyreállítás a StorSimple 8000 sorozatú eszközökhöz
description: Ismerje meg, hogyan kell feladatátvételt a StorSimple-eszköz önmagának, egy másik fizikai eszköznek vagy egy felhőalapú berendezésnek.
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
ms.openlocfilehash: 179bc5cdf982792f41e0dec209341f346959a31a
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397521"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Feladatátvétel és vészhelyreállítás StorSimple 8000 sorozatú eszközök esetében

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti az eszköz feladatátvételi szolgáltatás a StorSimple 8000 sorozatú eszközök, és hogyan ez a szolgáltatás segítségével a StorSimple-eszközök helyreállítása, ha egy katasztrófa bekövetkezik. A StorSimple eszközfeladat-átvételt használ az adatok áttelepítéséhez az adatközpontban lévő forráseszközről egy másik céleszközre. Az ebben a cikkben található útmutatás a StorSimple 8000 sorozatú fizikai eszközökre és a szoftververziókat futtató 3.

A StorSimple az **Eszközök** panelsegítségével indítja el az eszköz feladatátvételi funkcióját katasztrófa esetén. Ez a panel felsorolja a StorSimple eszközhöz csatlakoztatott összes StorSimple eszköz.

![Eszközök penge](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Vész-helyreállítási (DR) és az eszköz feladatátvétel

Vész-helyreállítási (DR) forgatókönyv esetén az elsődleges eszköz leáll. A StorSimple az elsődleges eszközt használja _forrásként,_ és áthelyezi a társított felhőadatokat egy másik _céleszközre._ Ezt a folyamatot *feladatátvételnek*nevezzük. Az alábbi ábra a feladatátvétel folyamatát mutatja be.

![Mi történik az eszköz feladatátvétel?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

A feladatátvétel céleszköze lehet fizikai eszköz vagy akár egy felhőalapú berendezés. Előfordulhat, hogy a céleszköz a forráseszköztől azonos vagy eltérő földrajzi helyen található.

A feladatátvétel során kiválaszthatja az áttelepítéskötet-tárolókat. A StorSimple ezután módosítja ezeknek a kötettárolóknak a tulajdonjogát a forráseszközről a céleszközre. Miután a kötettárolók tulajdonosváltása, StorSimple törli ezeket a tárolókat a forráseszközről. A törlés befejezése után visszaveheti a céleszközt. _A feladat-visszavétel_ visszaviszi a tulajdonjogot az eredeti forráseszközre.

### <a name="cloud-snapshot-used-during-device-failover"></a>Az eszköz feladatátvétele során használt felhőbeli pillanatkép

A dr-t követően a legutóbbi felhőalapú biztonsági mentés az adatok visszaállítása a céleszközre. A felhőbeli pillanatképekről a [Kézi biztonsági mentés használata a StorSimple Eszközkezelő szolgáltatás használatával](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup)című témakörben olvashat bővebben.

A StorSimple 8000 sorozat biztonsági mentési szabályzatok vannak társítva biztonsági mentések. Ha több biztonsági mentési házirendek ugyanazon a köteten, majd StorSimple kiválasztja a biztonsági mentési szabályzat a legtöbb kötetek. A StorSimple ezután a kiválasztott biztonsági mentési házirend legutóbbi biztonsági mentési házirendjét használja az adatok visszaállításához a céleszközön.

Tegyük fel, hogy két biztonsági mentési házirend van, *a defaultPol* és *a customPol*:

* *defaultPol*: Egy kötet, *vol1*, fut naponta 22:30-tól kezdődően.
* *customPol*: Négy kötet, *vol1*, *vol2*, *vol3*, *vol4*, fut naponta 22:00-tól kezdődően.

Ebben az esetben a StorSimple prioritást ad az összeomlás-konzisztencia és *a customPol,* mivel több kötetet használ. A házirend legutóbbi biztonsági mentése az adatok visszaállítására szolgál. A biztonsági mentési házirendek létrehozásáról és kezeléséről [a StorSimple Eszközkezelő szolgáltatás használata a biztonsági mentési házirendek kezeléséhez](storsimple-8000-manage-backup-policies-u2.md)című területen talál további információt.

## <a name="common-considerations-for-device-failover"></a>Az eszközfeladat-átvétel általános szempontjai

Mielőtt átadna egy eszközt, tekintse át az alábbi információkat:

* Az eszköz feladatátvételmegkezdése előtt a kötettárolókon belüli összes kötetnek offline állapotban kell lennie. Nem tervezett feladatátvétel esetén a StotSimple kötetek automatikusan kapcsolat nélküli módba lépnek. De ha egy tervezett feladatátvételt hajt végre (a DR teszteléséhez), az összes kötetet offline állapotba kell helyeznie.
* Csak a kötettárolók, amelyek egy társított felhőbeli pillanatkép szerepel a DR. Az adatok helyreállításához legalább egy kötettárolónak rendelkeznie kell egy társított felhőbeli pillanatképpel.
* Ha vannak felhőbeli pillanatképek, amelyek több kötettárolók, StorSimple átadja ezeket a kötettárolókat egy készlet. Egy ritka esetben, ha vannak helyi pillanatképek, amelyek több kötettárolók, de a társított felhőbeli pillanatképek nem, StorSimple nem feladatátvétela a helyi pillanatképek és a helyi adatok elvesznek a VÉSZ UTÁN.
* A DR rendelkezésre álló céleszközei olyan eszközök, amelyek elegendő hellyel rendelkeznek a kiválasztott kötettárolók elhelyezéséhez. Azok az eszközök, amelyek nem rendelkeznek elegendő hellyel, nem szerepelnek céleszközként.
* A VÉSZ-kezelés után (korlátozott időtartamra) az adatelérési teljesítmény jelentősen befolyásolható, mivel az eszköznek hozzá kell férnie az adatokhoz a felhőből, és helyileg kell tárolnia azadatokat.

#### <a name="device-failover-across-software-versions"></a>Eszközfeladat-átvétel a szoftververziók között

A storSimple Eszközkezelő szolgáltatás egy központi telepítésben több eszközzel is rendelkezhet, mind fizikai, mind felhőalapú, különböző szoftververziókat futtatva.

Az alábbi táblázat segítségével megállapíthatja, hogy átveheti-e a feladatátvételt, vagy visszaveheti-e egy másik, másik szoftververziót futtató eszköznek, és hogyan viselkednek a kötettípusok a vészkezelés során.

#### <a name="failover-and-failback-across-software-versions"></a>Feladatátvétel és feladatátvétel a szoftververziók között

| Feladatátvétel/ Feladat-visszavétel | Fizikai eszköz | Felhőalapú készülék |
| --- | --- | --- |
| A 4. |Rétegzett kötetek feladatátvételréteg-zettként. <br></br>A helyileg rögzített kötetek helyileg rögzítettként adják át a feladatátvételt. <br></br> Miután egy feladatátvételt követően, amikor pillanatképet a 4-es frissítés eszköz, [heatmap-alapú követés](storsimple-update4-release-notes.md#whats-new-in-update-4) elindul. |Helyileg rögzített kötetek feladatátvétel rétegzettként. |
| A 3. |Rétegzett kötetek feladatátvételréteg-zettként. <br></br>A helyileg rögzített kötetek helyileg rögzítettként adják át a feladatátvételt. <br></br> A hőtérkép metaadatainak visszaállításához használt biztonsági mentések. <br></br>A heatmap-alapú követés nem érhető el a 3. |Helyileg rögzített kötetek feladatátvétel rétegzettként. |


## <a name="device-failover-scenarios"></a>Eszközfeladatátvételi forgatókönyvek

Katasztrófa esetén dönthet úgy, hogy a StorSimple-eszközt adja át:

* [Egy fizikai eszközre](storsimple-8000-device-failover-physical-device.md).
* [Saját magának.](storsimple-8000-device-failover-same-device.md)
* [Egy felhőalapú készülékhez](storsimple-8000-device-failover-cloud-appliance.md).

Az előző cikkek részletes lépéseket tartalmaznak a fenti feladatátvételi esetek mindegyikére vonatkozóan.


## <a name="failback"></a>Feladat-visszavétel

A 3- as és újabb verziók esetében a StorSimple támogatja a feladat-visszavételt is. A feladat-visszavétel csak a feladatátvétel ellentéte, a cél lesz a forrás, és az eredeti forráseszköz a feladatátvétel során most lesz a céleszköz. 

A feladat-visszavétel során a StorSimple újraszinkronizálja az adatokat az elsődleges helyre, leállítja az I/O- és alkalmazástevékenységet, és visszavált az eredeti helyre.

A feladatátvétel befejezése után a StorSimple a következő műveleteket hajtja végre:

* A StorSimple törli a forráseszközről a feladatátvételt.
* A StorSimple kötettárolónkénti (feladatátvételi) háttérfeladatot kezdeményez a forráseszközön. Ha a feladat közben megpróbál visszaadni, erről értesítést kap. Várjon, amíg a feladat befejeződik a feladat elindításához a feladat.
* A kötettárolók törlésének befejezéséhez szükséges idő különböző tényezőktől függ, például az adatok mennyiségétől, az adatok korától, a biztonsági mentések számától és a művelethez rendelkezésre álló hálózati sávszélességtől.

Ha tesztfeladat-átvételeket vagy tesztfeladat-felvételeket tervez, azt javasoljuk, hogy tesztelje a kötettárolók kevesebb adattal (Gbs). Általában a feladat-visszavétel 24 órával a feladatátvétel befejezése után is elindíthatja.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. **Mi történik, ha a DR sikertelen vagy részleges sikeres?**

A. Ha a vész-vész-és vész-, azt javasoljuk, hogy próbálja meg újra. A második eszköz feladatátvételi feladat tisztában van az első feladat előrehaladásával, és ettől a ponttól kezdve kezdődik.

K. **Törölhetek egy eszközt, amíg az eszköz feladatátvétel folyamatban van?**

A. Nem törölhet egy eszközt, amíg a vész-vész-vész-b. Az eszköz csak a vész-dr befejezése után törölhető. Figyelheti az eszköz feladatátvételi feladat előrehaladását a **Feladatok** panelen.

K. **Mikor kezdődik a szemétgyűjtés a forráseszközön, hogy a helyi adatok törlődnek a forráseszközön?**

A. A szemétgyűjtés csak az eszköz teljes megtisztítása után engedélyezett a forráseszközön. A karbantartás magában foglalja a forráseszközről feladatátvevő objektumok, például a kötetek, a biztonsági mentési objektumok (nem az adatok), a kötettárolók és a házirendek tisztítását.

K. **Mi történik, ha a forráseszköz kötettárolóihoz társított törlési feladat sikertelen?**

A.  Ha a törlési feladat sikertelen, manuálisan törölheti a kötettárolókat. Az **Eszközök** panelen válassza ki a forráseszközt, és kattintson **a Kötettárolók gombra**. Válassza ki a feladatátvételt a panel alján a **Törlés gombra.** Miután törölte az összes feladatátvételi kötettárolók a forráseszközön, elindíthatja a feladat-visszavétel. További információ: [Kötettároló törlése](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzletmenet-folytonossági vészhelyreállítás (BCDR)

Az üzletmenet-folytonossági vész-helyreállítási (BCDR) forgatókönyv akkor fordul elő, amikor a teljes Azure-adatközpont működése leáll. Ez a forgatókönyv hatással lehet a StorSimple Eszközkezelő szolgáltatás és a kapcsolódó StorSimple-eszközök.

Ha egy StorSimple-eszköz közvetlenül a katasztrófa bekövetkezése előtt lett regisztrálva, akkor előfordulhat, hogy az eszköznek gyári alaphelyzetbe kell állítania. A katasztrófa után a StorSimple-eszköz offline állapotban jelenik meg az Azure Portalon. Ezt az eszközt törölni kell a portálról. Állítsa vissza az eszközt a gyári alapértékekre, és regisztrálja újra a szolgáltatásban.

## <a name="next-steps"></a>További lépések

Ha készen áll egy eszköz feladatátvételvégrehajtására, a részletes utasításokért válasszon az alábbi esetek közül:

- [Feladatátvétel másik fizikai eszközre](storsimple-8000-device-failover-physical-device.md)
- [Feladatátvétel ugyanarra az eszközre](storsimple-8000-device-failover-same-device.md)
- [Feladatátvétel a StorSimple felhőalapú berendezésnek](storsimple-8000-device-failover-cloud-appliance.md)

Ha feladatátvételt végzett az eszközzel, válasszon az alábbi lehetőségek közül:

* [Kapcsolja ki vagy törölje a StorSimple eszközt.](storsimple-8000-deactivate-and-delete-device.md)
* [A StorSimple Eszközkezelő szolgáltatás segítségével felügyelheti a StorSimple eszközt.](storsimple-8000-manager-service-administration.md)

