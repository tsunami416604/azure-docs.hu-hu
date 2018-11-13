---
title: Oktatóanyag az Azure Data Box Edge fizikai eszköz üzembe helyezéséhez | Microsoft Docs
description: Az Azure Data Box üzembe helyezését ismertető második oktatóanyagban a fizikai eszköz kicsomagolását, állványra szerelését és kábelekkel történő csatlakoztatását ismertetjük.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 6bd3c1b2cdbd83673a181ee7e088adb39749036e
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963846"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Oktatóanyag: Az Azure Data Box Edge (előzetes verzió) üzembe helyezése

Ez az oktatóanyag a Data Box Edge fizikai eszköz üzembehelyezési módját ismerteti. Az üzembehelyezési folyamatnak része az eszköz kicsomagolása, állványra szerelése és kábelekkel történő csatlakoztatása is. 

A telepítés körülbelül 2 órát vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszköz üzembe helyezéséhez szükséges előfeltételek az alábbiak.

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* [Az Azure Portal a Data Box Edge-hez való előkészítését](data-box-edge-deploy-prep.md) ismertető szakaszban foglalt összes lépést végrehajtotta.
    * Létrehozott egy Data Box Edge-erőforrást az eszköz üzembe helyezéséhez.
    * Létrehozta az eszköz a Data Box Edge-erőforrással való aktiválásához szükséges aktiválási kulcsot.

 
### <a name="for-the-data-box-edge-physical-device"></a>Data Box Edge fizikai eszköz esetén

Az eszköz üzembe helyezése előtt:

- Győződjön meg arról, hogy az eszköz egy sima, stabil és vízszintes munkafelületen (vagy ahhoz hasonlón) helyezkedik el.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - standard, független forrású áramforrás, vagy
    - egy kiszolgálószekrényhez való áramelosztó egység (PDU) szünetmentes tápegységgel (UPS).
- Győződjön meg arról, hogy a kiszolgálószekrényen szabadon elérhető egy 1U méretű hely, ahová be tudja szerelni az eszközt.

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át a Data Box Edge üzembe helyezésére vonatkozó hálózati követelményeket, és azoknak megfelelően konfigurálja az adatközponti hálózatot. További információkért lásd [a Data Box Edge hálózati követelményeit](data-box-gateway-system-requirements.md#networking-requirements) ismertető szakaszt.
- Az eszköz optimális működéséhez gondoskodjon róla, hogy legalább 20 Mb/s sebességű internetes sávszélesség rendelkezésre álljon.


## <a name="unpack-the-device"></a>Az eszköz kicsomagolása

Az eszköz egyetlen dobozban érkezik. Az eszköz kibontásához hajtsa végre a következő lépéseket. 

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a csomagon jelentős sérülések láthatók, ne nyissa ki. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.
3. Bontsa ki a dobozt. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
    - Egy darab Edge-eszköz
    - Két tápkábel
    - Egy csúsztatható, szerszámot nem igénylő állványba szerelési készlet (ennek tartozéka két oldalsín és a rögzítőeszközök)
4. Ha a fenti elemek bármelyikét nem kapta meg, forduljon a Data Box Edge támogatásához. A következő lépés az eszköz állványra szerelése.


## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az eszközt standard 19 hüvelykes kiszolgálószekrénybe lehet beszerelni. Az alábbi eljárást követve szerelje az állványt egy standard 19 hüvelykes elülső és hátsó rögzítőoszlopokkal rendelkező kiszolgálószekrénybe.

> [!IMPORTANT]
> A Data Box Edge-eszközöket a megfelelő működéshez kiszolgálószekrénybe kell szerelni.


1. Az elülső kioldót meghúzva oldja ki a belső sínt a csúsztató szerelvényből. Nyissa ki a reteszelőt, és nyomja befelé a középső sínt a behúzásához. A belső és külső sín most elvált egymástól.

    ![Állványra szerelhető sínek felszerelése](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Most szerelje fel a külső síneket a szekrény függőleges részére. A tájolás segítéséhez a sín csúsztatható elemein található egy Front (Elöl) jelölés, és azt a véget kell a burkolat elülső része felé rögzíteni. 
    
    1. Keresse meg a sín csapjait a sínszerelvény elülső és hátsó végénél. Húzza ki úgy a sínt, hogy illeszkedjen a szekrény oszlopai közé. Először a külső sínt illessze a szekrény hátuljához. Helyezze el a hátsó rögzítőkeretet a szekrényen lévő hátsó rögzítőnyílásokhoz igazítva.   
    2. Nyomja be és tartsa lenyomva a hátsó keret kioldóját, hogy láthatók legyenek a fémkampók. Illessze őket a rögzítőnyílásokhoz, majd engedje fel a kioldót.
    3. Illessze az elülső keretet a rögzítőnyíláshoz.
    4. Az elülső keret ekkor rögzül a szekrényhez. A sínek igény szerint M5 X 10L csavarokkal rögzíthetők az oszlopokhoz. 

    ![Állványra szerelhető sínek felszerelése](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. A belső sín a burkolatra illesztéséhez gondoskodjon arról, hogy a belső sínen lévő nyílások igazodjanak a burkolaton található rögzítőcsapokhoz. Ügyeljen arra, hogy a burkolat rögzítőcsapjainak feje kitüremkedjen a belső sín nyílásain keresztül. Húzza a sínt a burkolat eleje felé, amíg a sín hallható kattanással nem rögzül a helyén. Ezt ismételje meg a másik belső sínnél is. Nyomja a burkolatot a belső sínnel együtt a szekrénybe az állványra szerelés befejezéséhez.

    ![Állványra szerelhető sínek felszerelése](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Az eszköz bekábelezése

Az alábbi eljárás ismerteti az Edge-eszköz tápkábellel és hálózati kábellel való bekábelezését.

## <a name="prerequisites"></a>Előfeltételek

Az eszköz kábelezésének megkezdéséhez az alábbiakra lesz szüksége:

- Kicsomagolt és kiszolgálószekrénybe szerelt állapotú Edge fizikai eszköz.
- Két tápkábel. 
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 25-GbE SFP+ rézkábel minden konfigurálni kívánt adathálózati adapterhez. Legalább egy adathálózati adaptert (PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6) az internethez kell csatlakoztatni (csatlakozás az Azure-hoz).
- Hozzáférés két áramelosztó egységhez (ajánlott).

> [!NOTE]
> - Ha csak egyetlen adathálózati adaptert csatlakoztat, az adatok Azure-ra való küldéséhez egy 25-GbE hálózati adapter használatát (pl. PORT 3, PORT 4, PORT 5 vagy PORT 6) javasoljuk. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - Az Edge-eszköznek csatlakoznia kell az adatközponti hálózathoz, hogy fel tudja dolgozni az adatforrás-kiszolgálókról érkező adatokat. 

Az Edge-eszköz 8 NVMe SSD-vel rendelkezik. Az előlapon találhatók az állapotjelző LED-ek és a bekapcsológomb. Az eszközön a redundáns tápegységek hátul találhatók. Az eszköznek hat hálózati adaptere van: két 1 Gbps-adapter és négy 25 Gbps-adapter. Az eszköz rendelkezik alaplapi felügyeleti vezérlővel. Keresse meg a különböző portokat az eszköz hátlapján.
 
  ![Egy bekábelezett eszköz hátlapja](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Kövesse az alábbi lépéseket az eszköz tápkábellel és hálózati kábellel való bekábelezéséhez.

1. Csatlakoztassa a tápkábeleket a burkolat egy-egy tápegységéhez. A magas rendelkezésre állás biztosításához az egyes tápegységeket különböző áramforrásokhoz csatlakoztassa.
2. Csatlakoztassa a tápkábeleket a kiszolgálószekrény áramelosztó egységeihez (PDU-k). Ügyeljen arra, hogy a két tápegység külön áramforrást használjon.
3. Csatlakoztassa a PORT 1 jelölésű 1-GbE hálózati adaptert a fizikai eszköz konfigurálásához használt számítógéphez. A PORT 1 a dedikált felügyeleti felület.
4. A PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egyet az adatközponti hálózathoz/internethez kell csatlakoztatni. A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt. A 25-GbE hálózati adapterek esetén használja az SFP+ rézkábeleket.  


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-dzsel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

A következő oktatóanyag az eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

> [!div class="nextstepaction"]
> [A Data Box Edge csatlakoztatása és beállítása](./data-box-edge-deploy-connect-setup-activate.md)


