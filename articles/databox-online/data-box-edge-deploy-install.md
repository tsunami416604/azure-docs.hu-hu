---
title: Oktatóanyag az Azure Data Box Edge fizikai eszköz telepítése |} A Microsoft Docs
description: A második oktatóanyagban az Azure Data Box Edge telepítésével kapcsolatos kicsomagolása, állványra és a fizikai eszköz bekábelezése foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 11/01/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 6776eeb3cfdef98084c36a9441acafb8de1ab5b2
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53720322"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Oktatóanyag: Telepítse az Azure Data Box Edge (előzetes verzió)

Ez az oktatóanyag a Data Box Edge fizikai eszköz üzembehelyezési módját ismerteti. A telepítési folyamat magában foglalja a kicsomagolása, állványra csatlakoztatása és az eszköz kábelezése. 

A telepítés befejezéséhez körülbelül két órát is eltarthat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz rack csatlakoztatási
> * Az eszköz bekábelezése

> [!IMPORTANT]
> A Data Box peremhálózati megoldás, az előzetes verzióban érhető el. Order, és a megoldás üzembe helyezése előtt tekintse át a [Azure villámnézethez szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszköz a következő telepítésének előfeltételei:

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Összes lépését végrehajtotta [üzembe helyezését az Azure Data Box Edge (előzetes verzió)](data-box-edge-deploy-prep.md).
    * Ezzel létrehozott egy Data Box Edge erőforrást az eszköz üzembe helyezése.
    * Létrehozta az eszköz a Data Box Edge-erőforrással való aktiválásához szükséges aktiválási kulcsot.

 
### <a name="for-the-data-box-edge-physical-device"></a>Data Box Edge fizikai eszköz esetén

Az eszköz üzembe helyezése előtt:

- Győződjön meg arról, hogy az eszköz biztonságos alapja egy egybesimított, stabil és szintű munkahelyi felületen.
- Győződjön meg arról, hogy az eszköz beállításához kijelölt helyen van:
    - Szabványos hálózati áramellátás Visszaállt független forrásból

        -VAGY-
    - Egy állvány power terjesztési egység (PDU) szünetmentes tápegység (UPS)
    - Az állványra szerelt, amelyen az eszközt csatlakoztatni szeretne a-1 elérhető u bővítőhelyen

### <a name="for-the-network-in-the-datacenter"></a>Az adatközpont hálózata esetén

Előkészületek:

- Tekintse át a Data Box Edge hálózati követelményeinek, és konfigurálja az adatközponti hálózathoz az igényeknek. További információkért lásd [a Data Box Edge hálózati követelményeit](data-box-gateway-system-requirements.md#networking-requirements) ismertető szakaszt.

- Győződjön meg arról, hogy a minimális internetes sávszélesség 20 MB/s az eszköz optimális működéséhez.


## <a name="unpack-the-device"></a>Az eszköz kicsomagolása

Az eszköz egyetlen dobozban érkezik. Az eszköz kibontásához hajtsa végre a következő lépéseket. 

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a lista vagy a csomagolási súlyosan sérült, nem nyithatja meg. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.
3. Bontsa ki a dobozt. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
    - Egy darab Edge-eszköz
    - Két tápkábel
    - Egy eszköz nélküli dia állvány-csatlakoztatási kit (két oldalán rails és a csatlakoztatáshoz szükséges hardver tartoznak)

Ha nem kapta meg összes itt felsorolt elem, forduljon az ügyfélszolgálathoz Data Box Edge. A következő lépés az, hogy rack csatlakoztassa az eszközt.


## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az eszköz egy standard szintű 19 hüvelyk rack kell telepíteni. Használja az alábbi eljárást az állvány az eszköz csatlakoztatása az első és hátsó bejegyzések standard 19 hüvelyk állványra.

> [!IMPORTANT]
> A Data Box Edge-eszközöket a megfelelő működéshez kiszolgálószekrénybe kell szerelni.


1. Az elülső kioldót meghúzva oldja ki a belső sínt a csúsztató szerelvényből. Nyissa ki a reteszelőt, és nyomja befelé a középső sínt a behúzásához.  
    A belső és külső sín most elvált egymástól.

    ![Állványra szerelhető sínek felszerelése](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Telepítse a külső rails rack kabinet függőleges tagokat. Annak érdekében, a tájolás, a sín diák lesznek megjelölve **előtér**, és ennek a ház előre fel.    
    1. Keresse meg a sín csapjait a sínszerelvény elülső és hátsó végénél. Húzza ki úgy a sínt, hogy illeszkedjen a szekrény oszlopai közé. Először a külső sínt illessze a szekrény hátuljához. Állítsa be a zárójelet, helyezze a hátsó rack csatlakoztatást lyuk csatlakoztatási hátsó.   

    2. Nyomja be és tartsa lenyomva a hátsó keret kioldóját, hogy láthatók legyenek a fémkampók. Igazítás, és a háttérrendszer zárójelet beszúrása a csatlakoztatáshoz szükséges lyukakba, majd engedje fel az eseményindító.

    3. Illessze az elülső keretet a rögzítőnyíláshoz.

    4. Az első zárójel most rögzíteni kell a az állványra szerelt. Szükség esetén X 10L csavart M5 biztonságossá tételéhez a rails bejegyzések és szükség esetén használható. 

    ![Állványra szerelhető sínek felszerelése](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Csatolja a belső sín a a váz, győződjön meg róla, hogy a keyhole nyílás, az a belső sín összhangban legyenek a váz oldalán az helyének meghatározásával PIN-kód. Ügyeljen arra, hogy a burkolat rögzítőcsapjainak feje kitüremkedjen a belső sín nyílásain keresztül. Húzza a sínt a burkolat eleje felé, amíg a sín hallható kattanással nem rögzül a helyén. Ezt ismételje meg a másik belső sínnél is. Nyomja a burkolatot a belső sínnel együtt a szekrénybe az állványra szerelés befejezéséhez.

    ![Állványra szerelhető sínek felszerelése](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Az eszköz bekábelezése

Az alábbi eljárás ismerteti az Edge-eszköz tápkábellel és hálózati kábellel való bekábelezését.

Mielőtt elkezdené az eszköz kábelezése, a következők szükségesek:

- Kicsomagolt és kiszolgálószekrénybe szerelt állapotú Edge fizikai eszköz.
- Két tápkábel. 
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 25-GbE SFP+ rézkábel minden konfigurálni kívánt adathálózati adapterhez. PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egy hálózati adapter csatlakoztatva kell lennie az internethez (kapcsolattal rendelkező Azure-ban).  
- A hozzáférést a (javasolt) két Áramelosztó egységek.

> [!NOTE]
> - Ha csak egyetlen adathálózati adaptert csatlakoztat, az adatok Azure-ra való küldéséhez egy 25-GbE hálózati adapter használatát (pl. PORT 3, PORT 4, PORT 5 vagy PORT 6) javasoljuk. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - Az Edge-eszköznek csatlakoznia kell az adatközponti hálózathoz, hogy fel tudja dolgozni az adatforrás-kiszolgálókról érkező adatokat. 

Az Edge-eszköz 8 NVMe SSD-vel rendelkezik. Az előlapon találhatók az állapotjelző LED-ek és a bekapcsológomb. Az eszköz a hátsó redundáns áramforrások ellátási egységek (PSUs) tartalmaz. Az eszköznek hat hálózati adaptere van: két 1 Gbps-adapter és négy 25 Gbps-adapter. Az eszköz rendelkezik alaplapi felügyeleti vezérlővel. Keresse meg a különböző portokat az eszköz hátlapján.
 
  ![Egy bekábelezett eszköz hátlapja](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Az alábbi lépéseket és a hálózati eszköz kábelezése.

1. Csatlakoztassa a tápkábeleket a burkolat egy-egy tápegységéhez. A magas rendelkezésre állás biztosításához az egyes tápegységeket különböző áramforrásokhoz csatlakoztassa.

2. Csatlakoztassa a tápkábeleket a kiszolgálószekrény áramelosztó egységeihez (PDU-k). Ügyeljen arra, hogy a két tápegység külön áramforrást használjon.

3. Csatlakozzon az 1-GbE hálózati adapter PORTHOZ 1 a számítógépre, amely a fizikai eszköz konfigurálására szolgál. A PORT 1 a dedikált felügyeleti felület.

4. A PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egyet az adatközponti hálózathoz/internethez kell csatlakoztatni. A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt. A 25-GbE hálózati adapterek esetén használja az SFP+ rézkábeleket.  


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan lehet például a Data Box Edge témákról:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

A következő oktatóanyag az eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

> [!div class="nextstepaction"]
> [Csatlakozás és a Data Box Edge beállítása](./data-box-edge-deploy-connect-setup-activate.md)


