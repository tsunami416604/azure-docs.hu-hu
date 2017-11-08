---
title: "StorSimple feladatátvétel és vész-helyreállítási |} Microsoft Docs"
description: "Útmutató a StorSimple eszköz magát, egy másik fizikai eszköz vagy a virtuális eszköz feladatátvételt."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5751598e-49c8-42b3-8121-fea5857a7d83
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 02373633f2a30c6cd50e30b77a9faac2922926e6
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-device"></a>A StorSimple eszköz feladatátvétel és a katasztrófa utáni helyreállítás
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a StorSimple eszköz feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-8000-device-failover-disaster-recovery.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag azt ismerteti, hogyan legyen katasztrófahelyzet esetén a StorSimple eszköz feladatátvételt. A feladatátvétel lehetővé teszi egy forrás eszközt az adatközpontban az adatok áttelepíthetők egy másik fizikai vagy akár ugyanabban vagy egy különböző földrajzi helyen található virtuális eszköz. 

Vészhelyreállítás (DR) van összehangolva. az eszköz feladatátvételi szolgáltatás segítségével, és kezdeményezi a **eszközök** lap. Ezen a lapon minden a StorSimple-eszköz csatlakozik a StorSimple Manager szolgáltatás vannak. Az egyes eszközök rövid nevét, állapotát, kiépített és a maximális kapacitás, típusa és modell jelenik meg.

![Eszközök oldal](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

Az útmutató az oktatóanyag összes szoftver verziója között a StorSimple fizikai és virtuális eszközökre vonatkozik.

## <a name="disaster-recovery-dr-and-device-failover"></a>Vészhelyreállítás (DR) és az eszköz feladatátvételi
Egy vész-helyreállítási forgatókönyv esetén az elsődleges eszköz nem működik. Ebben a helyzetben helyezze át a sikertelen eszköz egy másik eszközre, az elsődleges eszköz használatával kapcsolódó felhő adatokat a *forrás* megadásával, mint egy másik eszköz a *cél*. Kiválaszthatja a céleszközt át egy vagy több kötet tárolóban. Ezt a folyamatot nevezzük a *feladatátvételi*. 

A feladatátvétel során a forráseszközről származó kötettárolók tulajdonjogai módosulnak, és átkerülnek a céleszközt. Amikor a kötettárolók tulajdonosának módosítására, ezeket a rendszer törli a forráseszközről származó. Miután a törlés befejeződött, a céleszközt is majd sikerült vissza.

Általában követően egy vész-Helyreállítási legfrissebb biztonsági másolat szolgál az adatok helyreállítását a cél-eszközre. Azonban ha ugyanazon a köteten több biztonsági mentési házirendeket, majd a biztonsági mentési házirend kötetek legnagyobb számú kivételezett lekérdezi és, hogy a házirend a legfrissebb biztonsági másolat használatával állítsa vissza az adatokat a céleszközön.

Tegyük fel, ha két biztonsági mentési házirendek (egy alapértelmezett és egy egyéni) *defaultPol*, *customPol* az alábbi adatokkal:

* *defaultPol* : egy kötet *vol1*, napi kezdő pozíció: 10:30 PM futtatja.
* *customPol* : négy kötetek *vol1*, *vol2*, *vol3*, *vol4*, napi kezdő pozíció: 10:00 PM futtatja.

Ebben az esetben *customPol* több köteten van, és összeomlásbiztos rangsoroljuk használható. Ez a házirend a legfrissebb biztonsági másolat adatok visszaállítására használható.

## <a name="considerations-for-device-failover"></a>Eszköz feladatátvételi szempontjai
Legyen katasztrófahelyzet esetén kiválaszthatja, hogy áthelyezze a feladatokat a StorSimple eszköz:

* A fizikai eszköz 
* Saját magára
* A virtuális eszközhöz

Minden eszköz feladatátvételi vegye figyelembe a következőket:

* DR az előfeltételeket, hogy a kötettárolók belül a kötetek offline módban, és kötettárolók rendelkeznek a hozzárendelt felhő-pillanatfelvételt. 
* Az elérhető célkiszolgálók Dr tartoznak eszközökre, amelyeken elegendő hely a lemezen a kijelölt kötettárolók tárolására. 
* Az eszközöket, amelyek a szolgáltatáshoz való kapcsolódás, de nem felel meg elegendő lemezterület nem lesz elérhető cél eszközként.
* A vész-Helyreállítási követően egy korlátozott ideig a adatelérési teljesítményét érintheti, jelentősen, az eszköz kell az adatokat a felhőből, és helyileg tárolja.

#### <a name="device-failover-across-software-versions"></a>Eszköz feladatátvételi szoftver verziója között
Előfordulhat, hogy a StorSimple Manager szolgáltatás a központi telepítésben lévő több eszközön, mind a fizikai, mind a virtuális, az összes futó különböző szoftver verziója. Attól függően, a szoftver verziója, a kötet típusok, az eszközök is lehetnek. Például egy alkalmazást futtató eszköznek az Update 2-es vagy magasabb volna helyileg rögzített és rétegzett kötet (az archiválás alatt álló részhalmazának Szintezett). A frissítés előtti 2 eszköz viszont előfordulhat, hogy rendelkezik rétegzett és archiválási kötetek. 

A következő táblázat segítségével határozza meg, ha a feladatokat átveheti egy másik szoftver verziója és a kötet típusa a Vészhelyreállítás során fut egy másik eszközre.

| A feladatátvételt | Fizikai eszköz számára engedélyezett | Virtuális eszköz számára engedélyezett |
| --- | --- | --- |
| A 2. frissítés előtti 1 (kiadás, 0,1, 0,2, 0,3) frissítése |Nem |Nem |
| Update 2 1 (1, 1.1-es, 1.2-es) frissítése |Igen <br></br>Ha helyileg rögzített vagy rétegzett kötetek vagy vegyesen két, a kötetek mindig feladatátvétel történt, rétegzett. |Igen<br></br>Ha használatával helyileg rögzített kötetek, ezek rétegzett feletti sikertelen. |
| Update 2 2. frissítést (újabb verzió) |Igen<br></br>Ha helyileg rögzített vagy rétegzett kötetek vagy vegyesen két használ, a kötetek mindig feladatátvétel történt a kiindulási kötet típusként; rétegzett rétegzett és helyileg rögzített, a helyileg rögzített. |Igen<br></br>Ha használatával helyileg rögzített kötetek, ezek rétegzett feletti sikertelen. |

#### <a name="partial-failover-across-software-versions"></a>Részleges feladatátvételi szoftver verziója között
Hajtsa végre ezt az útmutatást, ha azt tervezi, a frissítés előtti 1 egy cél frissítés-1-es vagy újabb rendszert futtató StorSimple forrás eszközt részleges feladatátvétel végrehajtásához. 

| A részleges feladatátvétel | Fizikai eszköz számára engedélyezett | Virtuális eszköz számára engedélyezett |
| --- | --- | --- |
| 1. frissítés vagy újabb frissítés előtti 1 (kiadás, 0,1, 0,2, 0,3) |Igen, tekintse meg alább a bevált gyakorlat tipp. |Igen, tekintse meg alább a bevált gyakorlat tipp. |

> [!TIP]
> Megváltozott egy felhőalapú metaadatokat és az adatokat formátum az 1. frissítés vagy újabb verzió használható. Ezért nem javasoljuk a frissítés előtti 1 részleges feladatátvételt az 1. frissítés vagy újabb verzió. Ha egy részleges feladatátvételre van szüksége, ajánlott Update 1 vagy újabb mindkét az eszközök (a forrás és a cél), és folytathatja a feladatátvételi. 
> 
> 

## <a name="fail-over-to-another-physical-device"></a>Feladatok átadása egy másik fizikai eszköz
A következő lépésekkel állítsa vissza az eszköz a fizikai céleszközön.

1. Győződjön meg arról, hogy a feladatátvétel kívánt kötettároló felhőalapú pillanatfelvételek vannak társítva.
2. Az a **eszközök** lapján kattintson a **Kötettárolók** fülre.
3. Válassza ki a kötettároló szeretné, hogy áthelyezze a feladatokat egy másik eszközre. Kattintson a kötettároló található kötetek listájának megjelenítéséhez. Jelöljön ki egy kötetet, majd **Offline állapotba** a kötet offline állapotba. Ismételje meg ezt a folyamatot a a kötettároló összes kötetet.
4. Ismételje meg az előző lépésben szeretné, hogy áthelyezze a feladatokat egy másik eszközre kötettárolók.
5. Az a **eszközök** kattintson **feladatátvételi**.
6. A megnyíló, a varázsló **válasszon kötettároló feladatátvételt**:
   
   1. A kötettárolók, jelölje ki a kötettárolók szeretné, hogy áthelyezze a feladatokat.
      **Csak a hozzárendelt felhő pillanatképek és a kapcsolat nélküli kötetekkel kötettárolók jelennek meg.**
   2. A **válassza ki a céleszközön** a kijelölt tárolókban lévő kötetekhez, jelöljön ki egy cél eszközt a legördülő listából válassza ki az elérhető eszközök. Csak azokat az eszközöket, amelyek a rendelkezésre álló kapacitásból a legördülő listában jelennek meg.
   3. Végül tekintse át a feladatátvételi beállítások **megerősítéséhez feladatátvétel**. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
7. Feladatátvételi feladat jön létre, amelyek segítségével figyelhető a **feladatok** lap. Az, hogy átadja a kötettároló helyi köteteken, majd látják minden helyi kötet (nem a rétegzett kötetek) a tárolóban levő egyes visszaállítási feladat. A visszaállítási feladatok befejezéséhez meglehetősen hosszabb időt vehet igénybe. Valószínű, hogy a feladatátvételi feladatban korábbi lehet végrehajtani. Vegye figyelembe, hogy az ezeken a köteteken helyi garanciákat kell, csak a visszaállítási feladatok végrehajtását követően. Miután a feladatátvétel befejeződött, lépjen a **eszközök** lap.                                            
   
   1. Válassza ki az eszközt, hogy a céleszközt, a feladatátvételi folyamat volt megadva.
   2. Lépjen a **Kötettárolók** lap. Minden kötettárolók, a köteteket a régi eszközről együtt kell szerepelnie.

## <a name="failover-using-a-single-device"></a>Egyetlen eszköz használatával a feladatátvételi
Hajtsa végre a következő lépéseket, ha csak olyan egyetlen eszközzel rendelkezik, és végezzen el egy feladatátvételt kell.

1. A kötetek felhőalapú pillanatfelvételek az eszközt a hálózatról.
2. Az eszköz visszaállítása a gyári beállításokat. Részletes utasításait [a StorSimple eszköz visszaállítása a gyári alapértelmezett beállításokra](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Állítsa be az eszközt, és regisztrálja újra a StorSimple Manager szolgáltatásban.
4. Az a **eszközök** lapon jelennek meg a régi eszköz **Offline**. Az újonnan regisztrált eszközre kell megjeleníteni **Online**.
5. Az új eszköz minimális konfigurációjának befejezéséhez az eszköz első. 
   
   > [!IMPORTANT]
   > **Ha a minimális konfigurációs először nem fejeződött be, a vész-Helyreállítási aktuális végrehajtása egy hiba miatt sikertelen lesz. Ez a viselkedés egy későbbi kiadásban lesz kijavítva.**
   > 
   > 
6. Válassza ki azt a régi (kapcsolat nélküli állapota) eszközt, és kattintson a **feladatátvételi**. A varázslóban megjelenő átadja ezt az eszközt, és adja meg a céleszközt az újonnan regisztrált eszközként. Részletes útmutatásért tekintse meg [átveheti egy másik fizikai eszköz](#fail-over-to-another-physical-device).
7. Egy eszköz-visszaállítási feladat jön létre, hogy a figyelheti a **feladatok** lap.
8. Miután a feladat sikeresen befejeződött, az új eszközhöz való hozzáféréshez, és keresse meg a **Kötettárolók** lap. A régi eszközről kötettárolók most már az új eszközre telepíthető át.

## <a name="fail-over-to-a-storsimple-virtual-device"></a>Feladatok átadása a StorSimple virtuális eszköz
Rendelkeznie kell egy StorSimple virtuális eszköz létrehozása, és ezzel az eljárással futtatása előtt konfigurálása. Ha 2. frissítés fut, érdemes egy 8020-as modell virtuális eszköz az a vész-Helyreállítási 64 TB-os és prémium szintű tárolást használ. 

A következő lépésekkel visszaállíthatja az eszköz célja a StorSimple virtuális eszköz.

1. Győződjön meg arról, hogy a feladatátvétel kívánt kötettároló felhőalapú pillanatfelvételek vannak társítva.
2. Az a **eszközök** lapján kattintson a **Kötettárolók** fülre.
3. Válassza ki a kötettároló szeretné, hogy áthelyezze a feladatokat egy másik eszközre. Kattintson a kötettároló található kötetek listájának megjelenítéséhez. Jelöljön ki egy kötetet, majd **Offline állapotba** a kötet offline állapotba. Ismételje meg ezt a folyamatot a a kötettároló összes kötetet.
4. Ismételje meg az előző lépésben szeretné, hogy áthelyezze a feladatokat egy másik eszközre kötettárolók.
5. Az a **eszközök** kattintson **feladatátvételi**.
6. A megnyíló, a varázsló **válasszon kötettároló feladatátvételre**, adja meg a következőket:
   
    a. A kötettárolók, jelölje ki a kötettárolók szeretné, hogy áthelyezze a feladatokat.
   
    **Csak a hozzárendelt felhő pillanatképek és a kapcsolat nélküli kötetekkel kötettárolók jelennek meg.**
   
    b. A **kötetekhez a céleszközön válassza a kijelölt tárolókban lévő**, válassza ki a StorSimple virtuális eszköz a legördülő listából a rendelkezésre álló eszközök. **Csak azokat az eszközöket, amely rendelkezik elegendő kapacitással a legördülő listában jelennek meg.**  
7. Végül tekintse át a feladatátvételi beállítások **megerősítéséhez feladatátvétel**. Kattintson a pipa ikonra ![pipa ikon](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
8. Miután a feladatátvétel befejeződött, lépjen a **eszközök** lap.
   
    a. Válassza ki a StorSimple virtuális eszköz, amely a céleszközt, a feladatátvételi folyamat volt megadva.
   
    b. Lépjen a **Kötettárolók** lap. Most már minden kötettárolók, együtt a köteteket a régi eszközről szerepelnie kell.

![Videó elérhető](./media/storsimple-device-failover-disaster-recovery/Video_icon.png)**Videó elérhető**

Bemutató videó bemutatja, hogyan visszaállíthatja egy sikertelen a virtuális eszköz a felhőben fizikai eszköz keresztül, kattintson a [Itt](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## <a name="failback"></a>Feladat-visszavétel
Az Update 3 és újabb verziókban a StorSimple feladat-visszavétel is támogatja. A feladatátvétel befejezése után a következő műveletek történnek meg:

* A forráseszközről származó kötettárolók, amely a feladatátvétel történt megtisztítja.
* A forráseszközt / kötettároló (feladatátvételt) a háttérben futó feladat indítható. Ha úgy próbálja feladat-visszavétel, amíg a feladat van folyamatban, erről értesítést fog kapni. Várjon, amíg a folyamat befejeződik, a feladat-visszavétel elindításához kell. 
  
    Az idő kötettárolók törlésének befejezéséhez az számos tényező befolyásolja, például az adatok mennyiségét, a művelet az adatok, a biztonsági másolatok számát és a rendelkezésre álló sávszélesség életkor függ. Ha a teszt feladatátvétel/visszavételek azt tervezi, azt javasoljuk, hogy tesztelje kötettárolók a kevesebb adat (GB). A legtöbb esetben megkezdheti a feladat-visszavétel 24 óra, a feladatátvétel befejezése után. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések
Q. **Mi történik, ha a vész-Helyreállítási meghibásodik, vagy részleges sikeres rendelkezik?**

A. A vész-Helyreállítási sikertelen lesz, azt javasoljuk, hogy újból megpróbálná. A második alkalommal körülbelül, vész-Helyreállítási tudja, hogy mit összes végezhető el, és ha a folyamat leállását először. A vész-Helyreállítási folyamat elindul ettől a ponttól kezdve. 

Q. **Törölhetek egy eszközt, amíg az eszköz feladatátvétel van folyamatban?**

A. Egy eszköz nem törölhető, amíg folyamatban van egy vész-Helyreállítási. Az eszköz csak akkor törölni, a vész-Helyreállítási befejeződése után.

Q.    **Ha nem a szemétgyűjtés indítsa el a forrás-eszközön, hogy a forrás-eszközökön a helyi adatok törlése?**

A. A szemétgyűjtés elérhetővé válik a forráseszközt csak azt követően az eszköz teljesen karbantartása. A karbantartás tartalmazza, amelyek nem tudták keresztül például kötetek, a biztonsági másolat objektumok (adatokat nem), a kötettárolók és a házirendek a forráseszközről származó objektumok törlése.

Q. **Mi történik, ha a forrás eszköz kötettárolók társított törlése feldolgozás sikertelen?**

A.  Ha a törlési feladat sikertelen, akkor kell manuálisan elindítani a kötettárolók törlését. Az a **eszközök** lapon válassza ki a forrás eszközt, majd kattintson **kötettárolók**. Jelölje ki azt a kötettárolók keresztül, és a lap alján található sikertelen **törlése**. Ha törölt minden a sikertelen keresztül kötettárolók a forrás-eszközön, indítsa el a feladat-visszavételre.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Üzleti folytonosság vészhelyreállítási (BCDR)
Üzleti folytonossági (BCDR) vészhelyreállítás akkor fordul elő, ha a teljes Azure-adatközpontban leáll a működése. Ez befolyásolhatja a StorSimple Manager szolgáltatás és a kapcsolódó StorSimple eszközökhöz.

Ha nincsenek StorSimple eszközökhöz regisztrált előtt történt egy olyan vészhelyzet esetén, majd a StorSimple eszköz kell végezni a gyári beállítások visszaállítása. A katasztrófa utáni a StorSimple eszköz jelenik meg offline állapotúként. A StorSimple eszköz törlését a portálról, és a gyári beállítások visszaállítása el kell végezni, egy friss regisztrációs követ.

## <a name="next-steps"></a>Következő lépések
* Miután elvégezte a feladatátvételt, esetleg [inaktiválja vagy törölje a StorSimple eszköz](storsimple-deactivate-and-delete-device.md).
* A StorSimple Manager szolgáltatással kapcsolatos további információkért látogasson el [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

