---
title: Rajzolási eszközök interakciós típusai és billentyűparancsok a Azure Mapsban | Microsoft Docs
description: Alakzatok rajzolása és szerkesztése egér, érintőképernyő vagy billentyűzet használatával a Azure Maps web SDK-ban
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8385f294426f6d793f7b539006eafd0b2cb940ea
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75481944"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>A rajzolási eszközök modulban található interakciós típusok és billentyűparancsok

Ez a cikk a Térkép alakzatainak egérrel, érintőképernyővel vagy billentyűparancsokkal történő rajzolásának és szerkesztésének különböző módjait ismerteti.

A rajzolási kezelő három különböző módszert támogat a térképekkel való interakciók rajzolásához.

* `click` – a koordinátákat az egér vagy érintés gombra kattintva adja hozzá a rendszer.
* `freehand ` – a koordinátákat akkor adja hozzá a rendszer, ha az egér vagy a érintés a térképen van húzva. 
* `hybrid` – a koordinátákat akkor adja hozzá a rendszer, ha az egér vagy a érintés kattint.

## <a name="how-to-draw-shapes"></a>Alakzatok rajzolása

Az alábbi, az alakzatokat ábrázoló különböző módokat vázolja fel a térképen. Bármely alakzat rajzolásának megkezdése előtt a Rajzfigyelő `drawingMode` beállítását egy támogatott rajzolási beállításra kell beállítani. Ezt programozott módon teheti meg, vagy lenyomhatja az eszköztár egyik rajzolási gombját. A rajzolási mód engedélyezve marad, még egy alakzat rajzolása után is, így könnyen megrajzolhatja az azonos típusú további alakzatokat. A rajzolási mód programozott állapotba helyezhető, vagy az eszköztár aktuális rajzolási módok gombjára kattintva. 

### <a name="how-to-draw-a-point"></a>Pont rajzolása

Ha a Rajzfigyelő `draw-point` rajzolási módban van, az alábbi műveleteket végezheti el a Térkép pontjainak rajzolásához. Ezek a módszerek az összes interakciós móddal együtt működnek.

**Rajzolás indítása**
 - Kattintson a bal egérgombra, vagy érintse meg a térképet, és adjon hozzá egy pontot a térképhez. 
 - Ha az egér a Térkép fölé esik, nyomja le a `F` kulcsot, és a rendszer az egérmutatót tartalmazó koordináta használatával adja hozzá a pontot. Ez nagyobb pontossági módszert biztosít a pontnak a térképhez való hozzáadására, mivel a bal oldali egérgomb lenyomása miatt kevesebb mozgás lesz az egéren.
 - Ha további pontokat szeretne felvenni a térképre, kattintson a gombra, érintse meg vagy nyomja meg `F`.
 
**Rajzolás befejezése**
 - Kattintson a Rajzolás eszköztár bármelyik gombjára. 
 - Programozott módon állíthatja be a rajzolási módot. 
 - Nyomja le a `C` kulcsot.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` kulcsot.

### <a name="how-to-draw-a-line"></a>Vonal rajzolása

Ha a Rajzfigyelő `draw-line` módban van, a következő műveleteket végezheti el a térképi pontok rajzolásához, attól függően, hogy milyen interakciós mód van beállítva.

**Rajzolás indítása**
 - Kattintási mód
   * Kattintson a bal egérgombra, vagy koppintson a térképre egy sor minden pontjának a térképen való hozzáadásához. Az egyes kattintásokhoz/érintésekhez tartozó vonalhoz egy koordináta kerül. 
   * Ha az egér a Térkép fölé esik, nyomja le a `F` kulcsot, és a rendszer az egérmutatót tartalmazó koordináta használatával adja hozzá a pontot. Ez nagyobb pontossági módszert biztosít a pontnak a térképhez való hozzáadására, mivel a bal oldali egérgomb lenyomása miatt kevesebb mozgás lesz az egéren.
   * Tartsa a kattintást, amíg az összes kívánt pont hozzá nem lett adva a sorhoz.
 - Szabadkézi mód
   * Nyomja le a bal oldali egérgombot, vagy koppintson lefelé a térképen, és húzza az egeret vagy az érintési pontot. A koordinátákat a rendszer hozzáadja az egérrel vagy érintési ponttal a Térkép körül. Amint megtörtént az egér vagy az érintéses esemény elindítása, a rendszer befejezi a rajzolást. A koordináták hozzáadásának gyakoriságát a rajzolási kezelők `freehandInterval` lehetőség határozza meg.
 - Hibrid mód
   * A kattintások és a szabadkézi módszerek közötti váltás a kívánt módon, egy sor rajzolásakor. Például kattintson a néhány pontra, majd az egér húzásával adjon hozzá egy csomó pontot, majd kattintson a továbbiak elemre. 

**Rajzolás befejezése**
 - Hibrid/kattintásos mód
   * Kattintson duplán a térképre az utolsó ponton. 
   * Kattintson a Rajzolás eszköztár bármelyik gombjára. 
   * Programozott módon állíthatja be a rajzolási módot. 
 - Szabadkézi mód
   * Adja meg az egérgombot vagy az érintési pontot.
 - Nyomja le a `C` kulcsot.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` kulcsot.

### <a name="how-to-draw-a-polygon"></a>Sokszög rajzolása

Ha a Rajzfigyelő `draw-polygon` módban van, a következő műveleteket végezheti el a térképi pontok rajzolásához, attól függően, hogy milyen interakciós mód van beállítva.

**Rajzolás indítása**
 - Kattintási mód
   * Kattintson a bal egérgombra, vagy koppintson a térképre a sokszög minden pontjának a térképen való hozzáadásához. Az egyes kattintásokhoz és érintésekhez egy koordináta kerül a sokszögbe. 
   * Ha az egér a Térkép fölé esik, nyomja le a `F` kulcsot, és a rendszer az egérmutatót tartalmazó koordináta használatával adja hozzá a pontot. Ez nagyobb pontossági módszert biztosít a pontnak a térképhez való hozzáadására, mivel a bal oldali egérgomb lenyomása miatt kevesebb mozgás lesz az egéren.
   * Tartsa a kattintást, amíg az összes kívánt pont hozzá nem lett adva a sokszöghez.
 - Szabadkézi mód
   * Nyomja le a bal oldali egérgombot, vagy koppintson lefelé a térképen, és húzza az egeret vagy az érintési pontot. A koordinátákat a rendszer hozzáadja a sokszöghez, mert az egér vagy az érintési pont mozog a Térkép körül. Amint megtörtént az egér vagy az érintéses esemény elindítása, a rendszer befejezi a rajzolást. Vegye figyelembe, hogy a koordináták hozzáadásának gyakoriságát a Rajzeszközök `freehandInterval` lehetőség határozza meg.
 - Hibrid mód
   * A kattintások és a szabadkézi módszerek közötti váltás a kívánt módon, miközben egyetlen sokszöget rajzol. Például kattintson a néhány pontra, majd az egér húzásával adjon hozzá egy csomó pontot, majd kattintson a továbbiak elemre. 

**Rajzolás befejezése**
 - Hibrid/kattintásos mód
   * Kattintson duplán a térképre az utolsó ponton. 
   * Kattintson a sokszög első pontjára.
   * Kattintson a Rajzolás eszköztár bármelyik gombjára. 
   * Programozott módon állíthatja be a rajzolási módot. 
 - Szabadkézi mód
   * Adja meg az egérgombot vagy az érintési pontot.
 - Nyomja le a `C` kulcsot.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` kulcsot.

### <a name="how-to-draw-a-rectangle"></a>Négyszög rajzolása

Ha a Rajzfigyelő `draw-rectangle` módban van, a következő műveleteket végezheti el a térképi pontok rajzolásához, attól függően, hogy milyen interakciós mód van beállítva. A generált alakzat a [téglalapok kiterjesztett GeoJSON-specifikációját](extend-geojson.md#rectangle)követi.

**Rajzolás indítása**
 - Nyomja le a bal oldali egérgombot, vagy érintse meg a térképen a téglalap első sarkának hozzáadásához, majd húzza a téglalapot a négyszög létrehozásához. 

**Rajzolás befejezése**
 - Adja meg az egérgombot vagy az érintési pontot.
 - Programozott módon állíthatja be a rajzolási módot. 
 - Nyomja le a `C` kulcsot.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` kulcsot.

### <a name="how-to-draw-a-circle"></a>Kör rajzolása

Ha a Rajzfigyelő `draw-circle` módban van, a következő műveleteket végezheti el a térképi pontok rajzolásához, attól függően, hogy milyen interakciós mód van beállítva. A generált alakzat a [körök kibővített GeoJSON-specifikációját](extend-geojson.md#circle)fogja követni.

**Rajzolás indítása**
 - Nyomja le a bal oldali egérgombot, vagy koppintson a térképen a kör középpontjának hozzáadásához, és húzza a köröket egy sugárba. 

**Rajzolás befejezése**
 - Adja meg az egérgombot vagy az érintési pontot.
 - Programozott módon állíthatja be a rajzolási módot. 
 - Nyomja le a `C` kulcsot.

**Rajzolás megszakítása**
 - Nyomja le a `Escape` kulcsot.

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

A rajzolási eszközök támogatják a billentyűparancsokat, amelyek megkönnyítik a Térkép alakzatainak rajzolását és szerkesztését. Ezek a billentyűparancsok akkor működőképesek, ha a Térkép fókuszban van.

| Jelmagyarázat      | Műveletek                            |
|----------|-----------------------------------|
| `C` | Befejezi a folyamatban lévő összes rajzot, és a rajzolási módot Üresjáratra állítja. A fókusz a legfelső szintű Térkép elemre lép.  |
| `Escape` | Megszakítja a folyamatban lévő összes rajzot, és a rajzolási módot Üresjáratra állítja. A fókusz a legfelső szintű Térkép elemre lép.  |
| `F` | Egy pontot, vonalat vagy sokszöget tartalmazó koordinátákat helyez el, ha az egér a Térkép fölé esik. A térképre kattintáskor vagy hibrid módban való kattintással egyenértékű művelet. Ez a parancsikon lehetővé teszi, hogy pontosabb és gyorsabb rajzok legyenek, mivel az egérmutatót az egér mozgatásával, a másikkal pedig a gomb megnyomásával lehet lenyomni. |

## <a name="next-steps"></a>Következő lépések

További információ a rajzolási eszközök modul osztályairól:

> [!div class="nextstepaction"]
> [Rajzolási kezelő](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Rajzolási eszköztár](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)