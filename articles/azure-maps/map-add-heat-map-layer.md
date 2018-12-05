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
ms.openlocfilehash: ec1343f85216171adac22f873f9be2e72bb4c282
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892306"
---
# <a name="add-a-heat-map-layer"></a>Egy megadott hőtérképrészlet térképréteg hozzáadása

Maps melegítsük, más néven sűrűségű maps mutasson, képviseli a színek különböző adatok sűrűsége adatvizualizáció típusú. Ezek gyakran használják a térképen "hotspotok" az adatok megjelenítése és nagyszerű mód megjelenítése nagy méretű adatkészletek mutasson.  Több ezer belül a térképnézet pontok, például megjelenítése a szimbólumok az ábrázolási terület részét lefedje, és számos szimbólumok vonatkozik rájuk, megnehezítve a sok betekintést nyerhet az adatokat eredményez. Azonban jelenítenek meg, ez ugyanaz az adatkészlet egy hőtérkép, egyszerűen, ahol a pont adatait a densest és a relatív kapacitású más területeire-e. Melyik megadott hőtérképrészlet a térképek, használt számos forgatókönyv közül választhat. Szívesen adunk néhány ötletet;

* Hőmérsékleti adatok gyakran jelenjen meg hőtérkép lehetővé teszi a két pont között milyen hőmérséklet becsült dátumokkal.
* Egy hőtérkép zaj érzékelők adatait megjelenítése nem csak a zaj insanity mutatja, amelyben az érzékelő van, de távolságban is lehetővé teszi a megoszlás betekintést. A zajszint minden egy helyen nem lehet magas, azonban a átfedésben van a zaj lefedettség terület többféle, ha lehetséges, hogy az átfedésben lévő terület zaj magasabb szintű tapasztalhat, és így hőtérkép látható.
* A GPS-adatok vizualizációja nyomkövetési, ahol az egyes adatpontok intenzitásával a sebessége alapján súly magasság térkép a sebesség tartalmazó kiválóan alkalmas gyors megtekintéséhez, ahol a jármű lerövidíti volt.

> [!TIP]
> Alapértelmezés szerint a buborék rétegek egy adatforrásban lévő összes geometriája koordinátáit jelenik meg. A réteg korlátozása úgy, hogy a vártak csak pont geometriai szolgáltatások beállítása az `filter` tulajdonság a réteg `['==', '$type', 'Point']`

## <a name="add-a-heat-map-layer"></a>Egy megadott hőtérképrészlet térképréteg hozzáadása

Egy leképezési pontok forrását, egy egyszerű hőtérkép az adatforrás átadandó HeatMapLayer osztály egy példányát, és adja hozzá a térkép itt látható módon.

<br/>

<iframe height='500' scrolling='no' title='Egyszerű Heat Map réteg' src='//codepen.io/azuremaps/embed/gQqdQB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/gQqdQB/'>egyszerű Heat Map réteg</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Ebben a példában minden egyes megadott hőtérképrészlet pont sugara 10 képpont sem nagyítási szintek. Való hozzáadásakor a heat map réteget a térkép, ez a minta beszúrja a címke réteg alatt. Ez létrehozza a fenti hőtérkép a fókuszjelzőknek jól láthatóknak címkékhez, jobb felhasználói élményt. Ebben a példában az adatok forrása a [USGS földrengés veszélyek Program](https://earthquake.usgs.gov/) és a pontokat, amelyeket az elmúlt 30 napban történt jelentős földrengések jelöli.

## <a name="customizing-the-heat-map-layer"></a>A megadott hőtérképrészlet térképréteg testreszabása

Az előző példában a radius- és átlátszatlanság beállításainak hőtérkép egyéni. A megadott hőtérképrészlet térképréteg testreszabási; számos lehetőséget kínál.

* `radius`: Egy, amelyben az egyes render képpont radius határozza meg. A radius beállítható egy rögzített szám vagy kifejezés. A radius beállítása egy kifejezés, amely a radius nagyítási szint alapján állítja be, úgy tűnik, hogy rendelkezik egy radius egy egységes, a térképen térbeli területen képviselő megadott hőtérképrészlet térképeket eredményezhet.
* `color`: Itt adhatja meg, hogyan hőtérkép színezettek van. Színátmenet színpalettát megadott hőtérképrészlet maps gyakran használják, de lépcsőzetes színpaletták is akkor hasznos, ha azt szeretné, hogy a hely több mint Munkaeloszlás adatok hőtérkép színe. A színpaletta a legnagyobb kapacitású értékre a minimális színét határozza meg. Megadott hőtérképrészlet Maps színértékek megadva kifejezésként a `heatmap-density` értéket. A színátmenetes kifejezésben 0. indexnél vagy egy lépés szín alapértelmezett színét határozza meg a terület színe, ha nem adatok, vagy a háttér színe. Számos igény szerint állítsa be ezt az értéket átlátható és a egy félig átlátszók fekete. Példa a színe kifejezés;

| Színátmenet színe kifejezés | Lépcsőzetes színe kifejezés | 
|---------------------------|--------------------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"interpolálja"<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[lineáris"\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[intenzitástérkép / sűrűség\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;0, "transzparens",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "lila",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,5, "#fb00fb",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1., "#00c3ff"<br/>\] | \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"lépésben"<br/>&nbsp;&nbsp;&nbsp;&nbsp;\[intenzitástérkép / sűrűség\],<br/>&nbsp;&nbsp;&nbsp;&nbsp;"transzparens"<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,01, "tengerészkék",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,25, "tengerészkék",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,50, "zöld",<br/>&nbsp;&nbsp;&nbsp;&nbsp;0,75, "sárga",<br/>&nbsp;&nbsp;&nbsp;&nbsp;1,00, a "red"<br/>\] |   

* `opacity`: Megadja, hogyan átlátszatlan vagy átlátható a heat map réteg.
* `intensity`: Az egyes adatpontok, az intenzitástérkép általános megnövelje a súlyt egy szorzóval vonatkozik. Ez segít, hogy a kisebb különbségek adatpontok súlyozást úgy válnak egyre könnyebben jelenítheti meg.
* `weight`: Alapértelmezés szerint minden adatpont 1 súlyozási rendelkezik, így minden adatpont súlyozása egyenlően. A súlyozási beállítás egy szorzójaként működik, és beállítható az a szám vagy kifejezés. Ha több számot, például: 2-lenne megfelelője, hogy hozzáadja az egyes adatpontok kétszer, a térképen, így kétszeresére a sűrűséget. Hasonló módon hőtérkép a súlyozási beállítás szám rendereli a intenzitása lehetőség használatával. Azonban egy kifejezés használata esetén az egyes adatpontok súlyát alapulhat másik, a pont tulajdonságai között az egyes metrika alapján. Hajtsa végre a megfelelő földrengés adatokat az egyes például földrengés jelöli, és minden földrengés rendelkezik egy fontos mérőszám magnitude. Földrengések minden esetben fordulhat elő, de a legtöbb alacsony magnitude rendelkezik, és nem is korábban úgy a tűnt. A kifejezésben magnitude értékének használatával a súlyozási beállítás hozzárendelése lehetővé teszi a jelentősebb földrengések kell növekedés hőtérkép jobban képviseli.
* Az alap réteg lehetőségek; mellett minimális/maximális nagyítás látható és szűréséhez, emellett van egy `source` beállítást, ha frissíti az adatforrás és `source-layer` lehetőséget, ha az adatforrás egy vektor csempe forrása.

Itt egy olyan eszköz, a különböző heat map réteg lehetőségek kipróbálásához.

<br/>

<iframe height='700' scrolling='no' title='HEAT Map réteg beállításai' src='//codepen.io/azuremaps/embed/WYPaXr/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/WYPaXr/'>térkép Rétegbeállítások melegítsük</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Az adatforrás a fürtszolgáltatás engedélyezésével egymáshoz közel pontok vannak csoportosítva fürtözött pontként. Pont száma, a fürtökön is használható a súly kifejezésként hőtérkép, és jelentősen csökkentheti a lehet renderelni rendelkező pontok számát. A pontok száma a fürt a pont funkció, ahogy az alábbi point_count tulajdonság tárolja. 
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
> [Egy szimbólum réteg hozzáadása](./map-add-pin.md)

