---
title: 'Oktatóanyag: Telepítsen egy Azure FXT Edge Filer fizikai eszközt'
description: A Microsoft Azure FXT Edge Filer hibrid tárolási gyorsítótárának fizikai eszközösszetevőjének kicsomagolása, állványa és kábelezése
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551029"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Oktatóanyag: Az Azure FXT Edge Filer telepítése 

Ez az oktatóanyag ismerteti, hogyan telepíthet egy hardvercsomópontot az Azure FXT Edge Filer hibrid tárolási gyorsítótárához. Legalább három hardvercsomópontot kell telepítenie egy Azure FXT Edge Filer-fürt létrehozásához.

A szerelési eljárás magában foglalja a készülék kicsomagolását és állványfelszerelését, valamint a kábelkezelő kar (CMA) és az elülső keret csatlakoztatását. Egy külön oktatóanyag ismerteti a hálózati kábelek csatlakoztatását és a csatlakozó áramot. 

Körülbelül egy órát vesz igénybe egy Azure FXT Edge Filer csomópont telepítése. 

Ez az oktatóanyag a következő telepítési lépéseket tartalmazza: 

> [!div class="checklist"]
> * Az eszköz kicsomagolása
> * Szerelje fel a készüléket állványra
> * Az elülső keret beszerelése (opcionális)

## <a name="installation-prerequisites"></a>Telepítési előfeltételek 

Mielőtt elkezdené, győződjön meg arról, hogy az adatközpont és az állvány a következő funkciókkal rendelkezik:

* Egy rendelkezésre álló 1U slot az állványon, ahol a készüléket csatlakoztatni kívánja.
* Az Azure FXT Edge Filer igényeinek megfelelő hálózati és hűtési rendszerek. (Olvassa el [a teljesítmény- és hőspecifikációkat](fxt-specs.md#power-and-thermal-specifications) a telepítés megtervezéséhez és méretezéséhez.)  

  > [!NOTE] 
  > A két redundáns tápegység (PSUs) teljes kihasználásához használjon energiaelosztó egységeket két különböző elágazási áramkörön a hálózati tápellátás csatlakoztatásakor. A részletekért olvassa el [A tápkábelek csatlakoztatása](fxt-network-power.md#connect-power-cables) szöveget.  

## <a name="unpack-the-hardware-node"></a>A hardvercsomópont kicsomagolása 

Minden Egyes Azure FXT Edge Filer csomópont egyetlen dobozban kerül kiszállításra. Hajtsa végre ezeket a lépéseket az eszköz kicsomagolásához.

1. Helyezze a csomagot egy sima, vízszintes felületre.

2. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a doboz vagy a csomagolás súlyosan megsérült, ne nyissa ki. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy az eszköz működőképes állapotban van-e.

3. Bontsa ki a dobozt. Győződjön meg arról, hogy a következő elemeket tartalmazza:
   * Egyetlen ház FXT eszköz
   * Két tápkábel
   * Egy elülső keret és kulcs
   * Egy sínkészlet szerelvény
   * Egy kábelkezelő kar (CMA)
   * CmA telepítési útmutató füzet
   * Állvány szerelési útmutató füzet
   * Biztonsági, környezetvédelmi és szabályozási tájékoztató füzet

Ha nem kapta meg az összes felsorolt cikket, kérjen támogatást az eszköz forgalmazójához. 

Győződjön meg róla, hogy a készüléknek elegendő ideje volt arra, hogy elérje a helyiséghőmérsékletét, mielőtt beszerelné vagy bekapcsolna. Ha kondenzációt észlel a készülék bármely részén, várjon legalább 24 órát a telepítés előtt.

A következő lépés az, hogy rack csatlakoztatni a készüléket.

## <a name="rack-the-device"></a>Az eszköz állványra szerelése

Az Azure FXT Edge Filer eszközt egy szabványos 19 hüvelykes állványra kell telepíteni. 

Az Azure FXT Edge Filer hibrid tárolási gyorsítótár három vagy több Azure FXT Edge Filer eszközből áll. Ismételje meg az állványtelepítési lépéseket minden olyan eszköz esetében, amely a rendszer része. 

### <a name="rack-install-prerequisites"></a>Rack-telepítés előfeltételei

* Mielőtt elkezdené, olvassa el a biztonsági utasításokat a biztonsági információk és a szabályozási információk című füzetben, amelyet a készülékhez szállított.

  > [!NOTE]
  > Mindig két embert használjon a csomópont felemelésekor, beleértve azt is, ha állványra telepíti, vagy eltávolítja az állványról. 

* Azonosítsa a berendezéstartóhoz használt sínberendezés típusát. 
  * Négyzetes vagy kerek furatbepattintóállványok esetén kövesse a szerszám nélküli sínre vonatkozó utasításokat.
  * Menetes furattartók esetén kövesse a szerszámos sínutasításokat. 
  
    A szerszámos sínszerelési konfigurációhoz nyolc csavart kell szállítania, 10-32, 12-24, M5 vagy M6 típusú. A csavarok fejátmérőjének 10 mm-nél (0,4")-nél kisebbnek kell lennie.

### <a name="identify-the-rail-kit-contents"></a>A sínkészlet tartalmának azonosítása

Keresse meg a sínkészlet szerelvény felszereléséhez szükséges alkatrészeket:

1. Két A7 Dell ReadyRails II csúszó sínszerelvény (1)
1. Két horog- és hurokszíj (2)

![A sínkészlet tartalmának számozott rajza](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Sínszerelvény - szerszám nélküli sínek (négyzetes furat- vagy kerek furattartók)

A pattintós négyzetes vagy kerek furatú állványok esetében kövesse ezt az eljárást a sínek összeszereléséhez és felszereléséhez. 

1. Helyezze a bal és a jobb sín végdarabokat **front** felirattal befelé. Helyezze el a végdarabokat úgy, hogy az a függőleges rack karimák elülső oldalán lévő lyukakba kerüljön. (1)

2. Igazítsa az állvány alsó és felső lyukait a csatlakoztatni kívánt helyre. (2)

3. Kapcsolja be a sín hátsó végét, amíg teljesen a függőleges rack karimára nem ül, és a retesz a helyére nem kattan. Ismételje meg ezeket a lépéseket, hogy helyezze el és ülesse el az elülső végét a függőleges rack karimán. (3)

> [!TIP]
> A sínek eltávolításához húzza meg a záródarab felezőpontjának (4) reteszkioldó gombját, és húzza le az egyes síneket.

![A szerszám nélküli sínek telepítésének és eltávolításának diagramja, számozott lépésekkel](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Sínszerelvény - szerszámozott sínek (Menetes furattartók)

Menetes furatokkal ellátott állványok esetén kövesse ezt az eljárást a sínek összeszereléséhez és felszereléséhez.

1. Egy lapos végű csavarhúzóval távolítsa el a csapokat az első és a hátsó rögzítőkonzolról. (1)
1. Húzza meg és forgassa el a sínreteszrészeket, hogy eltávolítsák őket a szerelőkonzolokról. (2)
1. Csatlakoztassa a bal és jobb oldali rögzítősíneket az első függőleges rack karimákhoz két pár csavarral. (3)
1. Csúsztassa előre a bal és jobb hátsó hátsó konzolokat a hátsó függőleges rack karimákhoz, és csatlakoztassa őket két pár csavarral. (4)

![A szerszámozott sínek telepítésének és eltávolításának diagramja, számozott lépésekkel](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Telepítse a rendszert az állványra

Kövesse az alábbi lépéseket az Azure FXT Edge Filer eszköz állványra való csatlakoztatásához.

1. Húzza ki a belső csúszósíneket az állványból, amíg a helyükre nem rögzülnek. (1)
1. Keresse meg a hátsó sín patthelyzetet a készülék mindkét oldalán, és engedje le őket a csúszda szerelvények hátsó J-nyílásaiba. (2) 
1. Forgassa lefelé a készüléket, amíg az összes sínpatthelyzet a J-hornyokban nem található. (3)
1. Nyomja befelé a készüléket, amíg a rögzítőkarok a helyükre nem kattannak.
1. Nyomja meg mindkét sín (4) kioldózárgombjait, és csúsztassa a készüléket az állványba.

![A rendszer telepítése állványdiagramra, számozott lépésekkel](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>A rendszer eltávolítása az állványról

Ha el szeretné távolítani az eszközt az állványról, kövesse ezt az eljárást. 

1. Keresse meg a rögzítőkarokat a belső sínek (1) oldalán.
2. Oldja fel az egyes karok kioldási helyzetének (2) elforgatásával.
3. Fogja meg erősen a rendszer oldalait, és húzza előre, amíg a sín patthelyzeta j-hornyok elején van. Emelje fel a rendszert, és távolodjon el az állványtól, és helyezze vízszintes felületre (3).

![A rendszer állványról való eltávolításának illusztrációja, számozott lépésekkel](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Kapcsolja be a csapzárat

1. Az elülső oldalra nézve keresse meg a csapzárat (1) a rendszer mindkét oldalán.
2. A reteszek automatikusan bekapcsolódnak, ahogy a rendszer benyomódik az állványba. 

A rendszer eltávolításakor húzza fel a reteszt (2).

Opcionális kemény reszerelhető csavarok állnak rendelkezésre, hogy biztosítsa a rendszert a rack szállításra vagy más instabil környezetben. Keresse meg a csavart az egyes reteszek alatt, és húzza meg egy #2 Phillips csavarhúzóval (3).

![A csapzár megszólításának és elengedésének számozott illusztrációja](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Telepítse a kábelkezelő kart 

Az FXT Edge Filer opcionális kábelkezelő kar (CMA) rendelkezésre áll. A telepítéshez a nyomtatott utasításokat a csomag ban találja. 

1. Csomagolja ki és azonosítsa a kábelkezelő karkészlet alkatrészeit:
   * CMA tálca (1)
   * CMA (2) Az
   * Nylon kábelnyakkendő pakolások (3)
   * CMA rögzítő konzolok (4)
   * Állapotjelző kábel (5) 

   > [!TIP] 
   > Ahhoz, hogy biztosítsa a CMA szállítás a rack, hurok a nyakkendő pakolások körül mind a kosarak és a tálca és cinch őket határozottan. A CMA ilyen módon történő biztosítása instabil környezetben is biztosítja a rendszert.

   ![CMA-alkatrészek illusztrációja](media/fxt-install/cma-kit-400.png)

2. Telepítse a CMA tálcát.

   A CMA tálca támogatást nyújt, és a CMA rögzítőjeként működik. 

   1. Igazítsa és bevonja a tálca mindkét oldalát a sín belső szélein lévő vevőtartó konzolokkal. 
   1. Tolja előre a tálcát, amíg a helyére nem kattan. (1)
   1. A tálca eltávolításához nyomja középre a reteszoldó gombokat, és húzza ki a tálcát a vevőtartókonzolokból (2).

   ![A CMA tálca telepítésének illusztrációja](media/fxt-install/cma-tray-install-400.png)

3. Telepítse a CMA melléklettartókat. 

   > [!NOTE]
   >
   > * A CMA-t a jobb vagy a bal oldali rögzítősínhez csatolhatja, attól függően, hogy hogyan kívánja a kábeleket a rendszerből irányítani. 
   > * A kényelem érdekében szerelje fel a CMA-t a tápegységekkel (A. oldal) ellentétes oldalra. Ha a B oldalra van szerelve, a külső tápegység eltávolítása érdekében a CMA-t le kell választani. 
   > * A tápegységek eltávolítása előtt mindig vegye ki a tálcát. 

   ![CmA konzol beszerelésének illusztrációja](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Válassza ki a megfelelő CMA rögzítőkonzolt ahhoz az oldalhoz, ahová a CMA-t fel szeretné szerelni (B. vagy A. oldal).
   1. Szerelje fel a CMA rögzítőkonzolt a csúszósín hátulján a megfelelő A vagy B oldalsó jelzéssel.
   1. Igazítsa a konzolon lévő lyukakat a csúszósínen lévő csapokhoz. Nyomja le a konzolt, amíg a helyére nem rögzül. 

4. Telepítse a CMA-t.

   1. A rendszer hátulján szerelje fel a reteszt a CMA elülső végén a tárgylemez legbelső konzoljára, amíg a retesz be nem kapcsol (1). 
   1. Szerelje fel a másik reteszt a legkülső konzol végére, amíg a retesz be nem kapcsol (2). 
   1. A CMA eltávolításához oldja ki mindkét reteszt a belső és külső reteszház (3) tetején található CMA kioldógombok megnyomásával.

   ![A CMA fő telepítésének illusztrációja](media/fxt-install/cma-install-400.png)

   A CMA elforgatható a rendszertől a hozzáférés és a szolgáltatás számára. A csuklós végén emelje le a CMA-t a tálcáról, hogy leüleltse (1). Miután letette a tálcáról, lendítse el a CMA-t a rendszertől (2).

   ![A szervizelt CMA illusztrációja](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Az elülső keret beszerelése (opcionális)

Ez a szakasz bemutatja, hogyan telepítheti és távolíthatja el az Azure FXT Edge Filer hardver első előtétét (előlapot). 

Az elülső keret felszerelése: 

1. Keresse meg és távolítsa el a keretkulcsot, amely a keretcsomagban található. 
1. Igazítsa az előlapot a váz elejéhez, és helyezze be a keret jobb oldalán lévő csapokat a csomópont jobb oldali racktartó peremének furataiba. 
1. Illessze a keret bal végét az alvázra. Nyomja meg az előlapot, amíg a bal oldali gomb a helyére nem kattan.
1. Zárd be az előlapot a kulccsal.

Az elülső keret eltávolítása: 
1. Oldja fel az előlapot az előlap gombbal.
1. Nyomja meg a kioldó gombot a bal oldalon, és húzza el a keret bal végét az alváztól.
1. Akassza ki a jobb végét, és távolítsa el az előlapot.
   
   ![A kioldógombot a keret bal oldalán látható, és hogyan távolítható el a bal oldalról kifelé húzva](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>További lépések

Miután kicsomagolta és gyakorlelte az eszközt, folytassa a beállítást a hálózati kábelek csatlakoztatásával és a hálózati áramazon az Azure FXT Edge Filer-hez való csatlakoztatásával.

> [!div class="nextstepaction"]
> [Kábelezze a hálózati portokat és a tápellátást](fxt-network-power.md)