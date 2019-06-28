---
title: Oktatóanyag az Azure FXT Edge Filer fizikai eszköz telepítése |} A Microsoft Docs
description: Hogyan kicsomagolása, állványra és a fizikai eszköz összetevője a Microsoft Azure FXT Edge Filer hibrid tárolási gyorsítótár kábelezése
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 45f4e12a06e012a06e0b2659d729fce3a191cd32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450421"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Oktatóanyag: Az Azure FXT Edge Filer telepítése 

Ez az oktatóanyag az Azure FXT Edge Filer hibrid tárolási gyorsítótár hardver csomópont telepítését ismerteti. -Azure FXT Edge Filer fürt legalább három hardvercsomópont telepítenie kell.

A telepítési folyamat magában foglalja a kicsomagolás és az eszköz csatlakoztatása, és csatolja a kábel felügyeleti arm (CMA) és előtérbeli bezel rack. Egy külön az oktatóanyag azt ismerteti, csatlakoztatását hálózati kábeleket és a kapcsolódó power. 

Az Azure FXT Edge Filer csomópont telepítése körülbelül egy óra vesz igénybe. 

Az oktatóanyag a beállítási lépések tartalmazza: 

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Csatlakoztassa az eszközt, az állvány
> * Telepítse az első bezel (nem kötelező)

## <a name="installation-prerequisites"></a>Telepítési előfeltételek 

Mielőtt elkezdené, győződjön meg arról, hogy az adatközpont és az állvány fogja használni a következő jellemzőkkel rendelkezik:

* Egy 1 elérhető u tárolóhely a az állványra szerelt célhelyeként csatlakoztassa az eszközt.
* AC-tápegység és hűtéssel rendszerek, amelyek az Azure FXT Edge Filer igényeinek. 

  Tervezési és a telepítés méretezése segítségért olvassa el a [energiagazdálkodási és hőmérsékleti specifikációk](fxt-specs.md#power-and-thermal-specifications) adattábla minősítések és hőmérsékleti kimeneti közös körülmények között.  

  > [!NOTE] 
  > A két redundáns áramforrások ellátási egység (PSUs) teljes körű kihasználása használja Áramelosztó egységek két eltérő ágként Kapcsolatcsoportok hálózati áramellátás Visszaállt csatlakoztatása. Olvasási [tápkábelek csatlakoztatása](fxt-network-power.md#connect-power-cables) részleteiről.  

## <a name="unpack-the-hardware-node"></a>Csomagolja ki a hardver csomópont 

Minden Azure FXT Edge Filer csomópont tartalmazza a szükséges egyetlen mezőben. Hajtsa végre az alábbi lépéseket az eszköz kicsomagolása.

1. Helyezze a csomagot egy sima, vízszintes felületre.

2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a lista vagy a csomagolási súlyosan sérült, nem nyithatja meg. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.

3. Bontsa ki a dobozt. Győződjön meg arról, hogy a következő elemeket tartalmazza:
   * Egy egyetlen lemezház FXT eszköz
   * Két tápkábel
   * Egy előtérbeli bezel és a kulcs
   * Egy sín kit sestavení
   * Egyik kábel felügyeleti arm (CMA)
   * CMA telepítési utasításokat füzet
   * Állvány telepítési utasításokat füzet
   * Biztonsági, környezetvédelmi és szabályozási információk füzet

Ha nem kapta meg összes elem megjelenik, forduljon az eszköz gyártója. 

Ellenőrizze, hogy az eszköz volt-e elegendő idő azonos hőmérsékletet, a helyet telepíti, vagy azt bekapcsolása előtt eléréséhez. Ha úgy látja az eszközt bármely részén páralecsapódás, várjon legalább 24 órán keresztül telepítése előtt.

A következő lépés az, hogy rack csatlakoztassa az eszközt.

## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az Azure FXT Edge Filer eszközön telepíteni kell egy standard szintű 19 hüvelyk állványra. 

Az Azure FXT Edge Filer hibrid szoftvertároló gyorsítótár három vagy több Azure FXT Edge Filer eszközök tevődik össze. Ismételje meg minden egyes eszköz, a rendszer részét képező rack telepítési lépéseket. 

### <a name="rack-install-prerequisites"></a>Állvány előfeltételek telepítése

* Mielőtt elkezdené, olvassa el a biztonsági, környezetvédelmi és szabályozási információk útmutató, amely az eszköz teljesített biztonsági utasításait.

  > [!NOTE]
  > Mindig használja a két személynek, amikor a csomópont, beleértve egy állványra telepítésekor, vagy távolítsa el az állványra szerelt emelő. 

* A berendezés állvány együttes sín telepítés típusának azonosításához. 
  * Négyzetes vagy kerek watering beépülő modul állványokon kövesse az eszköz nélküli sín utasításokat.
  * A többszálú watering állványokon tooled sín kövesse. 
  
    A konfigurációs csatlakoztatási tooled sín meg kell megadni a nyolc csavart, írja be 10-32, 12 – 24, M5 vagy M6. A fő átmérője a csavart kevesebb mint 10 mm (0,4") kell lennie.

### <a name="identify-the-rail-kit-contents"></a>A sín csomag tartalma azonosításához

Keresse meg az összetevők telepítésének sín kit sestavení:

1. Két A7 Dell ReadyRails II késleltetett sín szerelvények (1)
1. Két környezet igénybe vételét és (2) hevederek hurok

![Számozott megrajzolása sín csomag tartalmát.](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Sín szerelvény - eszköz nélküli rails (négyzetes watering vagy kör állványt)

A beépülő modul négyzetes vagy kerek lyukat állványokon kövesse ezt az eljárást össze, és telepítse a rails. 

1. Elhelyezése a bal és jobb sín záró darabok feliratú **első** befelé irányuló. Minden teljes darab helyezze el, hogy azt a lyuk függőleges rack fekszenek első oldalán lévő utasülések. (1)

2. Minden egyes teljes adatra az alsó és felső lyuk csatlakoztatni kívánt terület a tor-igazítása (2)

3. Léphet a háttérrendszere a sín amíg teljesen a függőleges állvány nyomkarima a munkaállomások és a Zárolás gombra kattint a helyen. Ismételje meg ezeket a lépéseket, elhelyezése és a kezelőfelület darab, a függőleges állvány nyomkarima a felhasználószám. (3)

> [!TIP]
> Távolítsa el a rails, a zárolás kiadási gomb lekéréses záró darab középpontját (4) a, és minden egyes sín unseat.

![Telepítésére és eltávolítására használatos eszköz nélküli rails, a számozott lépéseket bemutató ábra](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Sín szerelvény - tooled rails (összefűzött watering állványt)

A többszálú lyuk állványokon kövesse ezt az eljárást össze, és telepítse a rails.

1. Az első és hátsó zárójelben csatlakoztatása a átalánydíjjal Formabontó csavarhúzót távolítsa el a PIN-kódok. (1)
1. Kérje le, és a sín reteszes félkész, hogy eltávolítsa őket a csatlakoztatáshoz szükséges szögletes a elforgatása. (2)
1. A bal és jobb csatlakoztatása az előtér vertikális rack fekszenek csavart két pár használatával a rails csatolni. (3)
1. Húzza az ujját a bal és jobb biztonsági zárójelek közé hátsó függőleges rack fekszenek elleni továbbítja, és csatolja őket csavart két pár használatával. (4)

![Telepítésére és eltávolítására használatos tooled rails, a számozott lépéseket bemutató ábra](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>A rendszer telepíti az állvány egyik kiszolgálója

Kövesse az alábbi lépéseket az Azure FXT Edge Filer eszközt, az állvány egyik kiszolgálója csatlakoztatásához.

1. Kérje le a belső dia rails kívül az állványra szerelt, mindaddig, amíg azok zárolása, egy helyen. (1)
1. Keresse meg a hátsó sín standoff az eszköz minden oldalán, és hátsó J-tárolóhely a dia szerelvényekre be alacsonyabb őket. (2) 
1. Forgatása lefelé az eszköz, mindaddig, amíg az összes sín standoffs J – tárolóhelyben is illeszkedik. (3)
1. Az eszköz aktív leküldéses mindaddig, amíg a zárolás karok kattintson a helyen.
1. Nyomja le a dia-kiadás zárolási gombok mindkét sínen (4), és húzza az ujját az eszközt, az állvány egyik kiszolgálója.

![Rendszer telepítése az állvány diagramban a számozott lépéseket](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítja az állványra szerelt

Az eszköz eltávolítása az állványra szerelt, akkor kövesse ezt az eljárást. 

1. Keresse meg a zárolási karok a belső rails (1) oldalán.
2. Feloldás minden szintje forgatásával, akár a kiadási pozíció (2).
3. A rendszer a partnerek bonyolultnak nyitóbeszélgetést, és lekérheti előre addig, amíg a sín standoffs J – tárolóhely elején. Emelje a rendszer fel, és a forrásadatok az állványra szerelt, és helyezze felületen (3).

![A rendszer eltávolítását az állványra szerelt, a számozott lépéseket ábrája](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>A slam zárolás léphet.

1. Elérhető az előtérben, keresse meg a slam zárolás (1) a rendszer egyik oldalán.
2. A zárolás van életben automatikusan léphet, mivel a rendszer leküldéssel kerülnek az állványra szerelt. 

A rendszer eltávolítja a zárolás van életben feloldásához lekéréses őket (2).

A rendszer az állványra szerelt szállításra való előkészítését, vagy más nem stabil környezetben történő biztonságos választható rögzített csatlakoztatási csavart vannak megadva. Keresse meg a csavart egyes zárolás alatt, és a egy #2 növeljük őket csillagcsavarhúzóra (3).

![Számozott illusztráció vonzó és a slam zárolás feloldása](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Telepítse a kábel felügyeleti ága 

Egy nem kötelező kábel felügyeleti arm (CMA) az a FXT Edge Filer van megadva. A nyomtatott telepíti azt a útmutatás a csomagot. 

<!-- need current images in electronic form from Dell - these are scans -->

1. Csomagolja ki, és azonosíthatja a kábel felügyeleti arm kit összetevői:
   * CMA rendszertálca (1)
   * CMA (2)
   * Nejlon cable tie burkolja (3)
   * CMA melléklet zárójelek (4)
   * Állapot kijelző kábel (5) 

   > [!TIP] 
   > Biztonságos szállításra, az állvány egyik kiszolgálója a CMA, ikonjához, kosarak és a tálca tie burkolja, és szilárdan cinch őket. Ily módon a CMA biztonságossá tétele a rendszer instabil környezetben is biztonságos.

   ![CMA részek ábrája](media/fxt-install/cma-parts-scan-400.png)

2. Telepítse a CMA rendszertálca.

   A CMA tálca támogatást nyújt, és a egy a CMA a visszatartó funkcionál. 

   1. Igazítás, és folytatni a tálca a rails belső szélein fogadó zárójelekkel két oldalán. 
   1. Küldje le a tálca előre mindaddig, amíg azt helyen kattint. (1)
   1. A tálca törléséhez nyomja össze a zárolás-kiadás gombok a központ felé, és a fogadó szögletes zárójelek (2) kívül a tálca lekéréses.

   ![CMA rendszertálca telepítés ábrája](media/fxt-install/cma-tray-scan-400.png)

3. Telepítse a CMA melléklet zárójeleket. 

   > [!NOTE]
   >
   > * A CMA csatlakoztathat jobbra vagy balra csatlakoztatási sín, attól függően, hogy hogyan kívánja útvonal kábelek a rendszerből. 
   > * Az egyszerűség kedvéért csatlakoztassa a CMA ellentétes a tápegységek (oldalán A). Ha csatlakoztatva van, a B oldalon, a CMA bontani kell ahhoz, hogy távolítsa el a külső tápegység. 
   > * Mindig az szolgáltat az energiagazdálkodási eltávolítása előtt távolítsa el a tálca. 

   ![CMA zárójel telepítés ábrája](media/fxt-install/cma-brackets-scan-400.png)

   1. Válassza ki a megfelelő CMA melléklet zárójelet az oldal, ahol szeretné csatlakoztatni a CMA, (B oldalt vagy oldalt tartalmazó).
   1. A CMA melléklet zárójelet telepítse a megfelelő ügyféloldali A vagy B oldal megjelölés hátulján a dia sín található.
   1. Igazítás a lyuk be a zárójelet és a PIN-kódok a dia sín a. Küldje le a szögletes zárójelet, amíg zárol helyen. 

4. Telepítse a CMA.

   1. A rendszer hátulján található fér a zárolás, a CMA az előtérben a legbelső zárójelet dia sestavení mindaddig, amíg a zárolás végez (1). 
   1. A többi zárolás, a legkülső zárójelet végén fér, mindaddig, amíg a zárolás végez (2). 
   1. A CMA eltávolításához működésűeknek mindkét zárolás van életben billentyűkombináció lenyomásával a CMA kiadási gombok felső részén a belső és külső reteszes házak (3).

   ![Fő CMA telepítés ábrája](media/fxt-install/cma-install-scan-400.png)

   Erről a rendszer a hozzáférés és a szolgáltatás a CMA is elforgatva. Oldalpántos végén a CMA, erről a tálca unseat (1), hogy elvégezhető. Miután a tálca a unseated, párhuzamos a CMA, erről a rendszer (2).

   ![Illusztráció: CMA elforgatott nyissa meg a szolgáltatás](media/fxt-install/cma-open-scan-400.png)

## <a name="install-the-front-bezel-optional"></a>Telepítse az első bezel (nem kötelező)

Ez a szakasz ismerteti, hogyan telepítését és eltávolítását az első bezel (síktárcsára) az Azure FXT Edge Filer hardver. 

Az első bezel telepítése: 

1. Keresse meg, és távolítsa el a bezel kulcsot, amely a bezel csomag. 
1. Igazítás a bezel a váz elejéhez a, és helyezze be a PIN-kódok a bezel jobb oldalán található a csomópont a jobb oldalon rack csatlakoztatási nyomkarima lyukakba. 
1. Bal oldali végére az alakzatot a váz bezel illeszkedik. Nyomja le az bezel, mindaddig, amíg a bal oldalon a gombra kattint a helyen.
1. A bezel zárolása a kulccsal.

Az első bezel eltávolítása: 
1. A bezel feloldásához a bezel kulcs használatával.
1. Nyomja le a kiadási gomb bal oldalán, és kérje le a bezel, erről a váz bal oldalán.
1. A jobb oldali záró kihúzása, és távolítsa el a bezel.
   
   ![A kép a kiadási gomb bal oldalán a bezel és annak eltávolításáról kiindulásként passzív bal oldaláról](media/fxt-install/remove-bezel-updated-600.png)

## <a name="next-steps"></a>További lépések

Miután kibontotta és állványba helyezte az eszközt, a telepítés folytatásához a hálózati kábel csatlakoztatása és hálózati áramellátás Visszaállt csatlakozik az Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [A hálózati portok kábelek, és adja meg a power](fxt-network-power.md)