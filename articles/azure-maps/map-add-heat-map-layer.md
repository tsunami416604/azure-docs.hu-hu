---
title: Heat map réteg hozzáadása az Azure Maps |} A Microsoft Docs
description: A Javascript-leképezés egy megadott hőtérképrészlet térképréteg hozzáadása
author: rbrundritt
ms.author: richbrun
ms.date: 12/2/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 93dae9a69997dd1b513d205118a112119025f4fd
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057115"
---
# <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Maps melegítsük, más néven sűrűségű maps mutasson, képviseli a színek különböző adatok sűrűsége adatvizualizáció típusú. Azok jártas gyakran "hotspotok" adatok megjelenítése a térképen, és nagyszerű mód megjelenítése nagy méretű adatkészletek mutasson.  Például pontjai között a térképnézet szimbólumok, mint tízezer megjelenítése az ábrázolási terület leggyakrabban és vonatkozik rájuk, megnehezítve a sok betekintést nyerhet az adatokat számos szimbólumok eredményezne. Azonban jelenítenek meg, ez ugyanaz az adatkészlet egy hőtérkép, egyszerűen, ahol a pont adatait a densest és a relatív kapacitású más területeire-e. Melyik megadott hőtérképrészlet a térképek, használt számos forgatókönyv közül választhat. Szívesen adunk néhány ötletet;

* Hőmérsékleti adatok gyakran jelenjen meg hőtérkép lehetővé teszi a két pont között milyen hőmérséklet becsült dátumokkal.
* Egy hőtérkép zaj érzékelők adatait megjelenítése nem csak jeleníti meg a zaj intenzitását, amelyben az érzékelő van, de távolságban is lehetővé teszi a megoszlás betekintést. A zajszint minden egy helyen nem lehet magas, azonban a átfedésben van a zaj lefedettség terület többféle, ha lehetséges, hogy az átfedésben lévő terület zaj magasabb szintű tapasztalhat, és így hőtérkép látható.
* A GPS-adatok vizualizációja nyomkövetési, ahol az egyes adatpontok intenzitásával a sebessége alapján súlyozott magasság térkép a sebesség tartalmazó kiválóan alkalmas gyors megtekintéséhez, ahol a jármű lerövidíti volt.

> [!TIP]
> HEAT map rétegek alapértelmezés szerint egy adatforrásban lévő összes geometriája koordinátáit jelenik meg. A réteg korlátozása úgy, hogy ez csak a Render geometriai szolgáltatásait, mutasson a `filter` tulajdonság a réteg `['==', ['geometry-type'], 'Point']` vagy `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` Ha fel szeretne venni, valamint a MultiPoint szolgáltatások.

## <a name="add-a-heat-map-layer"></a>Hőtérképréteg hozzáadása

Adatforrásként pontok egy heat map render, át kell adnia az adatforrás egy példányát, a `HeatMapLayer` osztályt, és adja hozzá a térkép itt látható módon.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű Heat Map réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>egyszerű Heat Map réteg</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ebben a példában minden egyes megadott hőtérképrészlet pont sugara 10 képpont sem nagyítási szintek. Való hozzáadásakor a heat map réteget a térkép, ez a minta beszúrja a címke réteg jobb felhasználói élmény, a fenti hőtérkép a fókuszjelzőknek jól láthatóknak címkékhez létrehozása alatt. Ebben a példában az adatok forrása a [USGS földrengés veszélyek Program](https://earthquake.usgs.gov/) és a pontokat, amelyeket az elmúlt 30 napban történt jelentős földrengések jelöli.

## <a name="customizing-the-heat-map-layer"></a>A megadott hőtérképrészlet térképréteg testreszabása

Az előző példában a radius- és átlátszatlanság beállításainak hőtérkép egyéni. A megadott hőtérképrészlet térképréteg testreszabási; számos lehetőséget kínál.

* `radius`: Határozza meg, amelyben az egyes megjelenítése egy képpontos radius. A radius beállítható egy rögzített szám vagy kifejezés. Egy kifejezés használatával lehetőség a radius-alapú, amelyek a térképen (például 5 mérföldes radius) egy egységes térbeli területen megjelenő nagyítási szint méretezése.
* `color`: Itt adhatja meg, hogyan hőtérkép színezettek van. Színátmenetes színpalettát megadott hőtérképrészlet maps gyakran használják, és interpolációs kifejezése érhet el is lehet, de színpaletták is hasznos, ha azt szeretné, hogy a megadott hőtérképrészlet leképezése lépcsőzetes szín keresse meg a több mint Munkaeloszlás adatok, és léptető kifejezést érhető el. A színpaletta a legnagyobb kapacitású értékre a minimális színét határozza meg. Megadott hőtérképrészlet Maps színértékek megadva kifejezésként a `heatmap-density` értéket. Az index interpolációs kifejezésben 0 színét vagy léptető kifejezést, alapértelmezett színét határozza meg a terület színe, ha nem szerepel megjeleníthető adat és a háttérszínt egy használható. Számos igény szerint állítsa be ezt az értéket átlátható és a egy félig átlátszók fekete. Példa a színe kifejezés;

| Interpolációs színe kifejezés | Lépcsőzetes színe kifejezés | 
|--------------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolálja"<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[lineáris"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[intenzitástérkép / sűrűség\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.5, '#fb00fb',<br/>&nbsp;&nbsp;&nbsp;&nbsp;1., "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"lépésben"<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[intenzitástérkép / sűrűség\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transzparens"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "tengerészkék",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0.25, 'green',<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, a "red"<br/>\] | 

* `opacity`: Megadja, hogyan átlátszatlan vagy átlátható a heat map réteg.
* `intensity`: Az intenzitástérkép általános megnövelje az egyes adatpontok súlyát egy szorzóval vonatkozik, és elősegíti, hogy a kisebb különbségek a súlyt adatpontok, úgy válnak egyre könnyebben jelenítheti meg.
* `weight`: Alapértelmezés szerint minden adatpont 1 súlyozási rendelkezik, így minden adatpont súlyozása egyenlően. A súlyozási beállítás egy szorzójaként működik, és beállítható az a szám vagy kifejezés. Ha egy szám van beállítva a súly, például: 2 lenne megfelelője, hogy hozzáadja az egyes adatpontok kétszer, a térképen, így kétszeresére sűrűsége. Hasonló módon hőtérkép a súlyozási beállítás szám rendereli a intenzitása lehetőség használatával. Azonban egy kifejezés használata esetén az egyes adatpontok súlyát alapulhat néhány metrika megjelenik az egyes tulajdonságait. Hajtsa végre a megfelelő földrengés adatokat az egyes például földrengés jelöli. Fontos szempont minden földrengés adatpont rendelkezik, egy magnitude érték. Földrengések minden esetben fordulhat elő, de a legtöbb alacsony magnitude rendelkezik, és nem is korábban úgy a tűnt. A kifejezésben magnitude értékének használatával a súly hozzárendelése az egyes lehetővé teszi a jelentősebb földrengések jobban képviseltesse hőtérkép.
* Amellett, hogy az alap réteg lehetőségek; minimális/maximális nagyítás látható és szűréséhez, emellett van egy `source` beállítást, ha frissíti az adatforrás és `source-layer` lehetőséget, ha az adatforrás egy vektor csempe forrása.

Itt egy olyan eszköz, a különböző heat map réteg lehetőségek kipróbálásához.

<br/>

<iframe height='700' scrolling='no' title='HEAT Map réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>térkép Rétegbeállítások melegítsük</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="consistent-zoomable-heat-map"></a>Consistent zoomable heat map

Alapértelmezés szerint a radius adatpontok jelennek meg a megadott hőtérképrészlet térképréteg rendelkeznek egy rögzített képpontos sugarat rendel minden nagyítási szint. Mivel a térképet, mert már teljes együttesen az adatok összesítések és a megadott hőtérképrészlet térképréteg megjelenése. A `zoom` kifejezés segítségével méretezheti a sugarat rendel minden egyes nagyítási szintjét úgy, hogy az egyes vonatkozik a leképezés egy fizikai területen. Ez fogja elérhetővé tenni keresse meg a megadott hőtérképrészlet térképréteg statikus, és konzisztens. Minden egyes a térkép nagyítási szintjét függőleges rendelkezik kétszer annyi képpont, és vízszintes, az előző nagyítási szintjét, mint ilyen, a radius skálázás úgy, hogy az egyes nagyítási szintjét, megduplázódik hoz létre egy, a következőhöz konzisztens az összes nagyítási szint hőtérkép. A használatával lehet elvégezni a `zoom` egy 2-es `exponential interpolation` kifejezés az alábbi példában látható módon. Nagyítás a térkép megtekintéséhez, hogyan mutat, a radius az adatok a nagyítási szint heat map méretezési csoportban.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Consistent zoomable heat map" src="//codepen.io/azuremaps/embed/OGyMZr/?height=500&theme-id=light&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/OGyMZr/'>konzisztens zoomable hőtérkép</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Az adatforrás a fürtszolgáltatás engedélyezésével egymáshoz közel pontok vannak csoportosítva fürtözött pontként. Pont száma, a fürtökön is használható a súly kifejezésként hőtérkép, és jelentősen csökkentheti a lehet renderelni rendelkező pontok számát. A pontok száma a fürt tárolja egy `point_count` tulajdonság a pont funkció az alább látható módon. 
> ```JavaScript
> var layer = new atlas.layer.HeatMapLayer(datasource, null, {
>    weight: ['get', 'point_count']
> });
> ```
> Csak néhány képpont hiba esetén a fürtözési radius fog lehet kis visual különbség a renderelési. Egy nagyobb radius fog további pontok csoportosíthatja minden egyes fürt és az intenzitástérkép teljesítményének javítása, de rendelkezik a több észrevehető lesz a különbségeket.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [HeatMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](./map-add-pin.md)

