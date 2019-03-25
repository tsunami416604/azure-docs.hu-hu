---
title: Oktatóanyag az Azure Data Box Edge fizikai eszköz telepítése |} A Microsoft Docs
description: A második oktatóanyagban az Azure Data Box Edge telepítésével kapcsolatos kicsomagolása, állványra és a fizikai eszköz bekábelezése foglalja magában.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: a1357e92b868f85556fc4d665eb475abd095fece
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400009"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Oktatóanyag: Az Azure Data Box Edge telepítése

Ez az oktatóanyag a Data Box Edge fizikai eszköz üzembehelyezési módját ismerteti. A telepítési folyamat magában foglalja a kicsomagolása, állványra csatlakoztatása és az eszköz kábelezése. 

A telepítés befejezéséhez körülbelül két órát is eltarthat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz rack csatlakoztatási
> * Az eszköz bekábelezése

## <a name="prerequisites"></a>Előfeltételek

A fizikai eszköz a következő telepítésének előfeltételei:

### <a name="for-the-data-box-edge-resource"></a>Data Box Edge-erőforrás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Összes lépését végrehajtotta [üzembe helyezését az Azure Data Box Edge](data-box-edge-deploy-prep.md).
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

- Tekintse át a Data Box Edge hálózati követelményeinek, és konfigurálja az adatközponti hálózathoz az igényeknek. További információkért lásd [a Data Box Edge hálózati követelményeit](data-box-edge-system-requirements.md#networking-port-requirements) ismertető szakaszt.

- Győződjön meg arról, hogy a minimális internetes sávszélesség 20 MB/s az eszköz optimális működéséhez.


## <a name="unpack-the-device"></a>Az eszköz kicsomagolása

Az eszköz egyetlen dobozban érkezik. Az eszköz kibontásához hajtsa végre a következő lépéseket. 

1. Helyezze a csomagot egy sima, vízszintes felületre.
2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a lista vagy a csomagolási súlyosan sérült, nem nyithatja meg. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.
3. Bontsa ki a dobozt. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
    - Egy darab Edge-eszköz
    - Két tápkábel
    - Egy sín kit sestavení
    - A biztonsági, környezetvédelmi és szabályozási információk füzet

Ha nem kapta meg összes itt felsorolt elem, forduljon az ügyfélszolgálathoz Data Box Edge. A következő lépés az, hogy rack csatlakoztassa az eszközt.


## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az eszköz egy standard szintű 19 hüvelyk rack kell telepíteni. Használja az alábbi eljárást az állvány standard 19 hüvelyk állvány az eszköz csatlakoztatása.

> [!IMPORTANT]
> A Data Box Edge-eszközöket a megfelelő működéshez kiszolgálószekrénybe kell szerelni.


### <a name="prerequisites"></a>Előfeltételek

- Mielőtt elkezdené, olvassa el a biztonsági, környezetvédelmi és szabályozási információk füzet biztonsági utasításait. E könyvből teljesített az eszközzel.
- A kijelölt területen, az állvány ház alján legközelebb eső a a rails telepítésének megkezdése.
- A tooled sín csatlakoztatáshoz szükséges konfigurációt, meg kell megadni a nyolc csavart: #10 – 32, #12 – 24, #M5 vagy #M6. A fő átmérője a csavart kevesebb mint 10 mm (0,4") kell lennie.

### <a name="identify-the-rail-kit-contents"></a>A sín csomag tartalma azonosításához

Keresse meg az összetevők telepítésének sín kit sestavení:
1. Két A7 Dell ReadyRails sín szerelvények késleltetett II
2. Két környezet igénybe vételét és hevederek hurok

![Sín csomag tartalmának azonosítása](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Telepítése és eltávolítása az eszköz nélküli rails (négyzetes watering vagy kör állványt)

1. Elhelyezése a bal és jobb sín záró darabok feliratú **ELŐTÉR** befelé irányuló, és minden, a függőleges rack fekszenek első oldalán a lyuk felhasználószám záró darab elhelyezés.
2. Minden egyes teljes adatra a kívánt U tárolóhelyek az alsó és felső lyuk igazítása
3. Léphet a háttérrendszere a sín amíg teljesen a függőleges állvány nyomkarima a munkaállomások és a Zárolás gombra kattint a helyen. Ismételje meg ezeket a lépéseket, elhelyezése és a kezelőfelület darab, a függőleges állvány nyomkarima a felhasználószám.
4. Távolítsa el a rails, a zárolás kiadási gomb lekéréses záró darab középpontját a, és minden egyes sín unseat.

![Telepítése és eltávolítása az eszköz nélküli rails](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Telepítése és eltávolítása tooled rails (összefűzött watering állványt)

1. Távolítsa el az első és hátsó zárójelben átalánydíjjal Formabontó csavarhúzót használatával csatlakoztatja a PIN-kódok.
2. Kérje le, és a sín reteszes félkész, hogy eltávolítsa őket a csatlakoztatáshoz szükséges szögletes a elforgatása.
3. A bal és jobb csatlakoztatása az előtér vertikális rack fekszenek csavart két pár használatával a rails csatolni.
4. Húzza az ujját a bal és jobb biztonsági zárójelek közé hátsó függőleges rack fekszenek elleni továbbítja, és csatolja őket csavart két pár használatával.

![Telepítése és eltávolítása tooled rails](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>A rendszer egy állványra telepítése

1. Kérje le a belső dia rails kívül az állványra szerelt, mindaddig, amíg azok zárolása, egy helyen.
2. Keresse meg a hátsó sín standoff a rendszer minden oldalán, és hátsó J-tárolóhely a dia szerelvényekre be alacsonyabb őket. Forgatása lefelé a rendszer, mindaddig, amíg az összes sín standoffs J – tárolóhelyben is illeszkedik.
3. A rendszer csak a zárolást karok helyen kattintson aktív leküldéses.
4. Nyomja le a dia-kiadás zárolási gombok on rails és a diák a rendszer, az állvány egyik kiszolgálója.

![Rendszer telepítése egy állványra](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítja az állványra szerelt

1. Keresse meg a zárolási karok a belső rails oldalára.
2. Feloldás minden szintje forgatásával, akár kiadási pozícióját.
3. A rendszer a partnerek bonyolultnak nyitóbeszélgetést, és lekérheti előre addig, amíg a sín standoffs J – tárolóhely elején. Emelje a rendszer fel, és a forrásadatok az állványra szerelt, és helyezze felületen.

![Távolítsa el a rendszer, az állvány egyik kiszolgálója](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Léphet, és engedje el a slam zárolás

MEGJEGYZÉS: A rendszerek nem slam zárolás van életben felszerelt biztonságos csavart, a rendszer ez az eljárás 3. lépésében leírtak szerint.

1. A rendszer egyik oldalán az első elérhető, keresse meg a slam zárolás.
2. A zárolás van életben automatikusan léphet, a rendszer leküldéssel kerülnek az állványra szerelt, és a zárolás van életben a kiindulásként kiadott.
3. A rendszer, az állvány egyik kiszolgálója szállításra való előkészítését, vagy más instabil környezetek biztonságossá tételéhez, keresse meg a rögzített csatlakoztatási csavart egyes zárolás alatt, és elősegíti az egyes csavart egy # 2 megerősítését csillagcsavarhúzóra.

![Léphet, és engedje el slam zárolás](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)

### <a name="route-the-cables"></a>Irányíthatja a kábelek

> [!NOTE]
>  Nem fejeződött megrendeli a választható kábel felügyeleti Arm (CMA), ha a két hook használja, és irányíthatja a kábelek, a rendszer hátulján található sín Kit megadott hevederek ikonjához.

1. Keresse meg a külső CMA szögletes mindkét rack karimával belső oldalára.
2. A kábelek óvatosan, kötegeli őket lekérése, és a bal oldalon, a rendszer összekötők törlése.
3. A hozzászóláslánc a hook és loop hevederek tooled tárolóhely a a külső CMA szögletes zárójelek, a rendszer a kábel csomagjaiból biztonságossá minden oldalán keresztül.


![Irányíthatja a kábelek](./media/data-box-edge-deploy-install/routing-cables.png)

## <a name="cable-the-device"></a>Az eszköz bekábelezése

Az alábbi eljárás ismerteti az Edge-eszköz tápkábellel és hálózati kábellel való bekábelezését.

Mielőtt elkezdené az eszköz kábelezése, a következők szükségesek:

- Kicsomagolt és kiszolgálószekrénybe szerelt állapotú Edge fizikai eszköz.
- Két tápkábel. 
- Legalább egy 1-GbE RJ-45 hálózati kábel a felügyeleti felülethez való csatlakozáshoz. Az eszközön két 1-GbE hálózati adapter (egy felügyeleti és egy adathálózati) található.
- Egy 25-GbE SFP+ rézkábel minden konfigurálni kívánt adathálózati adapterhez. PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egy hálózati adapter csatlakoztatva kell lennie az internethez (kapcsolattal rendelkező Azure-ban).  
- A hozzáférést a (javasolt) két Áramelosztó egységek.

> [!NOTE]
> - Ha csak egy data hálózati adapter kapcsolódik, azt javasoljuk, például a PORT 3, PORT 4, PORT 5 vagy PORT 6 25/10-GbE hálózati adapter használatával adatok küldése az Azure-bA. 
> - A legjobb teljesítmény érdekében és nagy mennyiségű adat kezeléséhez fontolja meg az összes adatport csatlakoztatását.
> - Az Edge-eszköznek csatlakoznia kell az adatközponti hálózathoz, hogy fel tudja dolgozni az adatforrás-kiszolgálókról érkező adatokat.

Az Edge-eszköz 8 NVMe SSD-vel rendelkezik. Az előlapon találhatók az állapotjelző LED-ek és a bekapcsológomb. Az eszköz a hátsó redundáns áramforrások ellátási egységek (PSUs) tartalmaz. Az eszköz hat hálózati adapterrel rendelkezik:

- Két 1-GB/s sávszélességű adapter
- Négy 25-GB/s felületek, mint 10-GB/s sávszélességű adapter segédanyagként is használható.
- Alaplapi felügyeleti vezérlőnek (BMC). 

Keresse meg a különböző portokat az eszköz hátlapján.
 
  ![Egy bekábelezett eszköz hátlapja](./media/data-box-edge-deploy-install/backplane-cabled.png)

A 6 portok megfelelő két hálózati kártya van az eszközön: 

 - QLogic FastLinQ 41264
 - QLogic FastLinQ 41262

Támogatott kábelek, kapcsolók és a hálózati kártyák adó teljes listájáért látogasson el [Cavium FastlinQ 41000 sorozat együttműködési mátrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Az alábbi lépéseket és a hálózati eszköz kábelezése.

1. Csatlakoztassa a tápkábeleket a burkolat egy-egy tápegységéhez. A magas rendelkezésre állás biztosításához az egyes tápegységeket különböző áramforrásokhoz csatlakoztassa.

2. Csatlakoztassa a tápkábeleket a kiszolgálószekrény áramelosztó egységeihez (PDU-k). Ügyeljen arra, hogy a két tápegység külön áramforrást használjon.

3. Csatlakozzon az 1-GbE hálózati adapter PORTHOZ 1 a számítógépre, amely a fizikai eszköz konfigurálására szolgál. A PORT 1 a dedikált felügyeleti felület.

4. A PORT 2, PORT 3, PORT 4, PORT 5 vagy PORT 6 közül legalább egyet az adatközponti hálózathoz/internethez kell csatlakoztatni. A PORT 2 csatlakoztatása esetén használja az RJ-45 hálózati kábelt. A 10/25-GbE hálózati adapterek SFP + rézeres kábelek használja.  

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan lehet például a Data Box Edge témákról:

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Az eszköz állványra szerelése
> * Az eszköz bekábelezése

A következő oktatóanyag az eszköz csatlakoztatását, beállítását és aktiválását mutatja be.

> [!div class="nextstepaction"]
> [Csatlakozás és a Data Box Edge beállítása](./data-box-edge-deploy-connect-setup-activate.md)


