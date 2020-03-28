---
title: Bemutató a telepítéshez - Kicsomagolás, rack, kábel Azure Data Box Edge fizikai eszköz | Microsoft dokumentumok
description: Az Azure Data Box Edge telepítésével kapcsolatos második oktatóanyag a fizikai eszköz kicsomagolását, állványát és kábelezését foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263948"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Oktatóanyag: Az Azure Data Box Edge telepítése

Ez az oktatóanyag a Data Box Edge fizikai eszköz üzembehelyezési módját ismerteti. A beszerelési eljárás magában foglalja a készülék kicsomagolását, állványfelszerelését és kábelezését. 

A telepítés körülbelül két órát is igénybe vehet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Rack szerelje fel a készüléket
> * Az eszköz bekábelezése

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszköz telepítésének előfeltételei az alábbiak szerint:

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az Azure Data Box [Edge üzembe helyezésének előkészítése](data-box-edge-deploy-prep.md)című lépés összes lépése.
    * Létrehozott egy Data Box Edge erőforrást az eszköz üzembe helyezéséhez.
    * Létrehozta az eszköz a Data Box Edge-erőforrással való aktiválásához szükséges aktiválási kulcsot.

 
### <a name="for-the-data-box-edge-physical-device"></a>Data Box Edge fizikai eszköz esetén

Az eszköz üzembe helyezése előtt:

- Győződjön meg arról, hogy a készülék biztonságosan egy sima, stabil és vízszintes munkafelületen nyugszik.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - Standard hálózati teljesítmény független forrásból

        -VAGY-
    - Rack-áramelosztó egység (PDU) szünetmentes áramforrással (UPS)
    - Egy rendelkezésre álló 1U foglalat azállványon, amelyre a készüléket csatlakoztatni kívánja

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át a Data Box Edge üzembe helyezéséhez vonatkozó hálózati követelményeket, és konfigurálja az adatközpont-hálózatot a követelmények szerint. További információkért lásd [a Data Box Edge hálózati követelményeit](data-box-edge-system-requirements.md#networking-port-requirements) ismertető szakaszt.

- Győződjön meg arról, hogy a minimális internetes sávszélesség 20 Mbps az eszköz optimális működéséhez.


## <a name="unpack-the-device"></a>Az eszköz kicsomagolása

Az eszköz egyetlen dobozban érkezik. Az eszköz kibontásához hajtsa végre a következő lépéseket. 

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a doboz vagy a csomagolás súlyosan megsérült, ne nyissa ki. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.
3. Bontsa ki a dobozt. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
    - Egyetlen ház Data Box Edge eszköz
    - Két tápkábel
    - Egy sínkészlet szerelvény
    - Biztonsági, környezetvédelmi és szabályozási tájékoztató füzet

Ha nem kapta meg az összes itt felsorolt elemet, forduljon a Data Box Edge támogatási szolgálatához. A következő lépés az, hogy rack csatlakoztatni a készüléket.


## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az eszközt szabványos, 19 hüvelykes állványra kell telepíteni. Az alábbi eljárással állványra szerelheti a készüléket egy szabványos, 19 hüvelykes állványra.

> [!IMPORTANT]
> A Data Box Edge-eszközöket a megfelelő működéshez kiszolgálószekrénybe kell szerelni.


### <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, olvassa el a biztonsági utasításokat a Biztonsági, Környezetvédelmi és Szabályozási Információk füzetben. Ezt a füzetet az eszközzel együtt szállították.
- Kezdje el telepíteni a síneket a kijelölt térben, amely a legközelebb van az állványház aljához.
- A szerszámos sín szerelési konfigurációjához:
    -  Nyolc csavart kell szállítania: #10-32, #12-24, #M5 vagy #M6. A csavarok fejátmérőjének 10 mm-nél (0,4")-nél kisebbnek kell lennie.
    -  Szükséged van egy lapos végű csavarhúzóra.

### <a name="identify-the-rail-kit-contents"></a>A sínkészlet tartalmának azonosítása

Keresse meg a sínkészlet szerelvény felszereléséhez szükséges alkatrészeket:
1. Két A7 Dell ReadyRails II csúszó sínszerelvény
2. Két horog- és hurokszíj

    ![A sínkészlet tartalmának azonosítása](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Szerszám nélküli sínek felszerelése és eltávolítása (négyzetes furat- vagy kerek furattartók)

> [!TIP]
> Ez az opció szerszám nélküli, mert nem igényel eszközöket telepíteni, és távolítsa el a sínek a menetnélküli négyzet vagy kerek lyukak az állványok.

1. Helyezze a bal és a jobb sín végdarabokat **FRONT** felirattal befelé nézőre, és irányítsa a többi végét a függőleges rack karimák elülső oldalán lévő lyukakba.
2. Igazítsa a kívánt U terek alsó és felső lyukait.
3. Kapcsolja be a sín hátsó végét, amíg teljesen a függőleges rack karimára nem ül, és a retesz a helyére nem kattan. Ismételje meg ezeket a lépéseket, hogy helyezze el és ülesse el az elülső darabot a függőleges rack karimán.
4. A sínek eltávolításához húzza meg a záródarab felezőpontjának reteszkioldó gombját, és húzza le az egyes síneket.

    ![Szerszám nélküli sínek telepítése és eltávolítása](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Szerszámozott sínek felszerelése és eltávolítása (menetes furattartók)

> [!TIP]
> Ez az opció van szerszám, mert szükség van egy szerszám (_egy lapos végű csavarhúzó)_ telepíteni, és távolítsa el a sínek a menetes kerek lyukak az állványok.

1. Egy lapos végű csavarhúzóval távolítsa el a csapokat az első és a hátsó rögzítőkonzolról.
2. Húzza meg és forgassa el a sínreteszrészeket, hogy eltávolítsák őket a szerelőkonzolokról.
3. Csatlakoztassa a bal és jobb oldali rögzítősíneket az első függőleges rack karimákhoz két pár csavarral.
4. Csúsztassa előre a bal és jobb hátsó hátsó konzolokat a hátsó függőleges rack karimákhoz, és csatlakoztassa őket két pár csavarral.

    ![Szerszámokat szerelhet fel és távolíthat el](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Telepítse a rendszert egy állványra

1. Húzza ki a belső csúszósíneket az állványból, amíg a helyükre nem rögzülnek.
2. Keresse meg a hátsó sín patthelyzetet a rendszer mindkét oldalán, és engedje le őket a csúszda szerelvények hátsó J-nyílásaiba. Forgassa lefelé a rendszert, amíg az összes sínpatthelyzet a J-hornyokban nem található.
3. Nyomja befelé a rendszert, amíg a rögzítőkarok a helyükre nem kattannak.
4. Nyomja meg mindkét sínen a kioldózár gombjait, és csúsztassa be a rendszert az állványba.

    ![A rendszer telepítése állványra](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítása az állványról

1. Keresse meg a lakatkarokat a belső sínek oldalán.
2. Oldja fel az egyes karok kioldási helyzetébe történő elforgatásával.
3. Fogja meg erősen a rendszer oldalait, és húzza előre, amíg a sín patthelyzeta j-hornyok elején van. Emelje fel a rendszert, és távol a rack, és tegyük vízszintes felületre.

    ![A rendszer eltávolítása az állványról](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Vegye be és engedje el a csapzárat

> [!NOTE]
> A nem csapzárakkal nem rendelkező rendszerek esetében rögzítse a rendszert csavarokkal, az eljárás 3.

1. Az elülső oldalra nézve keresse meg a csapzárat a rendszer mindkét oldalán.
2. A reteszek automatikusan bekapcsolnak, ahogy a rendszer benyomódik az állványba, és a reteszfelhúzással szabadulnak fel.
3. A rackbe történő szállításhoz vagy más instabil környezethez a keményre szerelhető csavart minden retesz alá kell helyezni, és húzza meg az egyes csavarokat egy #2 Phillips csavarhúzóval.

    ![Csapjon be és engedje ki a csapzárat](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Az eszköz bekábelezése

Irányítsa át a kábeleket, majd kábelezze a készüléket. Az alábbi eljárások bemutatják, hogyan kábelezheti a Data Box Edge eszközt a tápellátáshoz és a hálózathoz.

A készülék kábelezésének megkezdése előtt a következőkre van szükség:

- A Data Box Edge fizikai eszköz, kicsomagolva és rack csatlakoztatva.
- Két tápkábel.
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 25-GbE SFP+ rézkábel minden konfigurálni kívánt adathálózati adapterhez. Legalább egy adathálózati felületet a PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 között legalább egy hálózati kapcsolatnak kell csatlakoznia az internethez (az Azure-hoz való kapcsolódással).  
- Hozzáférés két energiaelosztó egységhez (ajánlott).

> [!NOTE]
> - Ha csak egy adathálózati adaptert csatlakoztat, azt javasoljuk, hogy a 25/10 GbE hálózati adaptert ( PORT 3, PORT 4, PORT 5 vagy PORT 6) használja az Azure-ba történő adatküldéshez. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - A Data Box Edge eszközt az adatközpont-hálózathoz kell csatlakoztatni, hogy képes legyen adatokat beadni az adatforrás-kiszolgálókról.

A Data Box Edge eszközön:

- Az előlapon lemezmeghajtók és bekapcsológomb található.

    - A készülék elején 10 lemezhely található.
    - A 0-ás foglalat 240 GB-os SATA meghajtóval rendelkezik, amelyet operációs rendszer lemezeként használnak. Az 1-es bővítőhely üres, a 2-9 bővítőhely pedig az adatlemezként használt NVMe SSD-k.
- A hátsó sík redundáns tápegységeket (PSUs) tartalmaz.
- A hátsó sík hat hálózati kapcsolattal rendelkezik:

    - Két 1 Gbit/s-os kapcsolat.
    - Négy 25 Gbit/s-os felület, amelyek 10 Gbit/s-os felületként is szolgálhatnak.
    - Alaplapi vezérlő (BMC).

- A hátsó sík két hálózati kártyával rendelkezik, amelyek a 6 portnak felelnek meg:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

A hálózati kártyák támogatott kábeleinek, kapcsolóinak és adó-vevőinek teljes listáját a [Cavium FastlinQ 41000 sorozatos együttműködési mátrix ban talál.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)
 
Az alábbi lépésekkel kábelezheti a készüléket a tápellátáshoz és a hálózathoz.

1. Azonosítsa a készülék hátsó síkján lévő különböző portokat.

    ![Kábeles eszköz hátsó síkja](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Keresse meg a lemeznyílásokat és a bekapcsológombot a készülék elején.

    ![Az eszköz elülső síkja](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Csatlakoztassa a tápkábeleket a burkolat egy-egy tápegységéhez. A magas rendelkezésre állás biztosításához az egyes tápegységeket különböző áramforrásokhoz csatlakoztassa.
4. Csatlakoztassa a tápkábeleket a kiszolgálószekrény áramelosztó egységeihez (PDU-k). Ügyeljen arra, hogy a két tápegység külön áramforrást használjon.
5. A készülék bekapcsolásához nyomja meg a bekapcsológombot.
6. Csatlakoztassa az 1 GbE hálózati adapter 1-es portját a fizikai eszköz konfigurálásához használt számítógéphez. A PORT 1 a dedikált felügyeleti felület.
7. A PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egyet az adatközponti hálózathoz/internethez kell csatlakoztatni.

    - A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt.
    - A 10/25-GbE hálózati interfészekhez használja az SFP+ rézkábeleket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan témakörökről szerzett tudomást, mint például a következők:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

A következő oktatóanyag az eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

> [!div class="nextstepaction"]
> [A Data Box Edge csatlakoztatása és beállítása](./data-box-edge-deploy-connect-setup-activate.md)
