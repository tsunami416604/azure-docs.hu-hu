---
title: Rajzeszközök interakciós típusai és billentyűparancsai a térképen | Microsoft Azure Maps
description: Alakzatok rajzolása és szerkesztése egérrel, érintőképernyővel vagy billentyűzettel a Microsoft Azure Maps Web SDK-ban
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198292"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Kapcsolatitevékenység-típusok és billentyűparancsok a rajzeszközök modulban

Ez a cikk az alakzatok egérrel, érintőképernyővel vagy billentyűparancsokkal történő rajzolásának és szerkesztésének különböző módjait ismerteti.

A rajzkezelő három különböző módot támogat a térképpel való interakcióra, az alakzatok rajzolására.

* `click`- Koordináták kerülnek, ha az egér vagy érintés kattintott.
* `freehand `- Koordináták kerülnek, ha az egér vagy érintés húzta a térképen.
* `hybrid`- Koordináták kerülnek, ha az egér vagy érintés kattintott, vagy húzta.

## <a name="how-to-draw-shapes"></a>Alakzatok rajzolása

 Mielőtt bármilyen alakzatot megrajzolna, állítsa a `drawingMode` rajzkezelő beállítását támogatott rajzbeállításra. Ez a beállítás programozható vagy meghívható az eszköztár egyik rajzgombjának megnyomásával. A rajzolási mód az alakzat rajzolása után is engedélyezve marad, így könnyen rajzolhat azonos típusú alakzatokat. Programozott módon állítsa a rajzolási módot tétlen állapotba. Másik lehetőségként váltson tétlen állapotba az eszköztár aktuális rajzmódgombjára kattintva.

A következő szakaszok az alakzatok térképen való rajzolásának különböző módjait tagolják.

### <a name="how-to-draw-a-point"></a>Hogyan kell felhívni egy pontot

Ha a rajzkezelő `draw-point` rajzolási módban van, a következő műveletekvégezhetők a pontok rajzolására a térképen. Ezek a módszerek minden interakciós móddal működnek.

**Rajzolás megkezdése**
 - Kattintson a bal egérgombbal, vagy érintse meg a térképet, hogy adjon egy pontot a térképhez. 
 - Ha az egér a térkép `F` felett van, nyomja le a billentyűt, és egy pont kerül hozzáadásra az egérmutató koordinátához. Ez a módszer nagyobb pontosságot biztosít egy pont hozzáadásához a térképhez. A bal egérgomb megnyomása miatt az egér mozgása kisebb lesz.
 - Tartsa kattintva, megható, `F` vagy nyomja meg, hogy adjunk több pontot a térképhez.
 
**Rajz befejezése**
 - Kattintson bármelyik gombra a rajz eszköztárán. 
 - Programozott módon állítsa be a rajzolási módot. 
 - Nyomja `C` meg a gombot.

**Rajz megszakítása**
 - Nyomja `Escape` meg a gombot.

### <a name="how-to-draw-a-line"></a>Hogyan rajzolj egy vonalat

Ha a rajzkezelő `draw-line` üzemmódban van, a következő műveleteket lehet elvégezni, hogy pontokat rajzoljon a térképre, a kapcsolati üzemmódtól függően.

**Rajzolás megkezdése**
 - Kattintási mód
   * Kattintson a bal egérgombbal, vagy érintse meg a térképet, hogy hozzá minden egyes pontját egy sor a térképen. A rendszer minden egyes kattintáshoz vagy érintéshez hozzáad egy koordinátát a vonalhoz. 
   * Ha az egér a térkép `F` felett van, nyomja le a billentyűt, és egy pont kerül hozzáadásra az egérmutató koordinátához. Ez a módszer nagyobb pontosságot biztosít egy pont hozzáadásához a térképhez. A bal egérgomb megnyomása miatt az egér mozgása kisebb lesz.
   * Tartsa kattintva, amíg az összes kívánt pontot hozzá adta a vonalhoz.
 - Szabadkézi mód
   * Nyomja le a bal egérgombot, vagy érintse meg a térképen, és húzza az egeret, vagy érintse meg körül. A koordináták a vonalhoz kerülnek, ahogy az egér vagy az érintőpont a térkép körül mozog. Amint az egér vagy a touch-up esemény aktiválódik, a rajz befejeződik. A koordináták hozzáadásának gyakoriságát `freehandInterval` a rajzkezelők beállítás határozza meg.
 - Hibrid mód
   * Váltson a kattintás és a szabadkézi módszerek között, igény szerint, miközben egyetlen vonalat rajzol. Például kattintson néhány pontra, majd tartsa nyomva az egeret, és húzza az egeret egy csomó pont hozzáadásához, majd kattintson még néhányra. 

**Rajz befejezése**
 - Hibrid/Kattintás mód
   * Kattintson duplán a térképre az utolsó ponton. 
   * Kattintson bármelyik gombra a rajz eszköztárán. 
   * Programozott módon állítsa be a rajzolási módot. 
 - Szabadkézi mód
   * Engedje fel az egérgombot vagy az érintőpontot.
 - Nyomja `C` meg a gombot.

**Rajz megszakítása**
 - Nyomja `Escape` meg a gombot.

### <a name="how-to-draw-a-polygon"></a>Hogyan rajzoljon egy sokszöget

Ha a rajzkezelő `draw-polygon` üzemmódban van, a következő műveleteket lehet elvégezni, hogy pontokat rajzoljon a térképre, a kapcsolati üzemmódtól függően.

**Rajzolás megkezdése**
 - Kattintási mód
   * Kattintson a bal egérgombbal, vagy érintse meg a térképet a sokszög egyes pontjainak hozzáadásához a térképen. A sokszög minden egyes kattintáshoz vagy érintéshez hozzáad egy koordinátát. 
   * Ha az egér a térkép `F` felett van, nyomja le a billentyűt, és egy pont kerül hozzáadásra az egérmutató koordinátához. Ez a módszer nagyobb pontosságot biztosít egy pont hozzáadásához a térképhez. A bal egérgomb megnyomása miatt az egér mozgása kisebb lesz.
   * Tartsa kattintva, amíg az összes kívánt pontot hozzá adta a sokszög.
 - Szabadkézi mód
   * Nyomja le a bal egérgombot, vagy érintse meg a térképen, és húzza az egeret, vagy érintse meg körül. A koordináták a sokszöghez adnak, ahogy az egér vagy az érintőpont a térkép körül mozog. Amint az egér vagy a touch-up esemény aktiválódik, a rajz befejeződik. A koordináták hozzáadásának gyakoriságát `freehandInterval` a rajzkezelők beállítás határozza meg.
 - Hibrid mód
   * Váltson a kattintás és a szabadkézi módszerek között, igény szerint, egyetlen sokszög rajzolása közben. Például kattintson néhány pontra, majd tartsa nyomva az egeret, és húzza az egeret egy csomó pont hozzáadásához, majd kattintson még néhányra. 

**Rajz befejezése**
 - Hibrid/Kattintás mód
   * Kattintson duplán a térképre az utolsó ponton. 
   * Kattintson a sokszög első pontjára.
   * Kattintson bármelyik gombra a rajz eszköztárán. 
   * Programozott módon állítsa be a rajzolási módot. 
 - Szabadkézi mód
   * Engedje fel az egérgombot vagy az érintőpontot.
 - Nyomja `C` meg a gombot.

**Rajz megszakítása**
 - Nyomja `Escape` meg a gombot.

### <a name="how-to-draw-a-rectangle"></a>Hogyan rajzoljon egy téglalapot

Ha a rajzkezelő `draw-rectangle` üzemmódban van, a következő műveleteket lehet elvégezni, hogy pontokat rajzoljon a térképre, a kapcsolati üzemmódtól függően. A generált alakzat követi a [kiterjesztett GeoJSON specifikáció téglalapok](extend-geojson.md#rectangle).

**Rajzolás megkezdése**
 - Nyomja le a bal egérgombot, vagy érintse meg a térképen, hogy adja hozzá az első sarkában a téglalap, és húzza a téglalap létrehozásához. 

**Rajz befejezése**
 - Engedje fel az egérgombot vagy az érintőpontot.
 - Programozott módon állítsa be a rajzolási módot. 
 - Nyomja `C` meg a gombot.

**Rajz megszakítása**
 - Nyomja `Escape` meg a gombot.

### <a name="how-to-draw-a-circle"></a>Hogyan kell felhívni egy kört

Ha a rajzkezelő `draw-circle` üzemmódban van, a következő műveleteket lehet elvégezni, hogy pontokat rajzoljon a térképre, a kapcsolati üzemmódtól függően. A generált alakzat követi a [kiterjesztett GeoJSON specifikáció körök](extend-geojson.md#circle).

**Rajzolás megkezdése**
 - Nyomja le a bal egérgombot, vagy touch-down a térképen, hogy adjunk a központ a kör, és húzza, hogy a körök sugara. 

**Rajz befejezése**
 - Engedje fel az egérgombot vagy az érintőpontot.
 - Programozott módon állítsa be a rajzolási módot. 
 - Nyomja `C` meg a gombot.

**Rajz megszakítása**
 - Nyomja `Escape` meg a gombot.

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

A rajzeszközök támogatják a billentyűparancsokat. Ezek a billentyűparancsok akkor működnek, ha a térkép fókuszban van.

| Kulcs      | Műveletek                            |
|----------|-----------------------------------|
| `C` | Befejezi a folyamatban lévő rajzokat, és a rajzi módot tétlenre állítja. A fókusz a legfelső szintű térképelemre kerül.  |
| `Escape` | Megszakítja a folyamatban lévő rajzokat, és a rajzolási módot tétlenre állítja. A fókusz a legfelső szintű térképelemre kerül.  |
| `F` | Koordinátát ad egy ponthoz, vonalhoz vagy sokszöghez, ha az egér a térkép felett van. Egyenértékű művelet kattintva a térképre, ha a click vagy hibrid módban. Ez a parancsikon pontosabb és gyorsabb rajzokat tesz lehetővé. Az egyik kezével elhelyezze az egeret, a másikkal pedig megnyomhatja a gombot anélkül, hogy az egeret a sajtómozdulatból mozgatná. |

## <a name="next-steps"></a>További lépések

További információ a rajzeszközök modul osztályairól:

> [!div class="nextstepaction"]
> [Rajzkezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolás eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)
