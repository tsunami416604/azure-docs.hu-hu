---
title: Képsablonok az Azure Maps Web SDK-ban | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan használhatja a képsablonokat HTML-jelölőkkel és a Microsoft Azure Maps Web SDK különböző rétegeivel.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: ee8e8ee4ca64de0390b6fa34e36fb4d06348a8ac
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804809"
---
# <a name="how-to-use-image-templates"></a>Rendszerképsablonok használata

A képek HTML-jelölőkkel és az Azure Maps webes SDK-n belüli különböző rétegekkel használhatók:

 - A szimbólumrétegek képikonnal jeleníthetik meg a térkép pontjait. A szimbólumok egy vonalútvonal mentén is renderelhetők.
 - A sokszögrétegek kitöltési mintaképpel renderelhetők. 
 - A HTML-jelölők képek és más HTML-elemek használatával jeleníthetik meg a pontokat.

Annak érdekében, hogy a rétegek jó teljesítményt, töltse be a képeket a térkép kép sprite erőforrás renderelés előtt. A [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), a SymbolLayer, preloads egy pár marker képek egy maroknyi színben a térkép kép sprite, alapértelmezés szerint. Ezek a jelölőképek és egyebek SVG-sablonként érhetők el. Ezeket fel lehet használni, hogy hozzon létre képeket egyéni mérlegek, vagy az ügyfél elsődleges és másodlagos színként használható. Összesen 42 képsablon áll rendelkezésre: 27 szimbólumikon és 15 sokszög kitöltési minta.

Képsablonokat lehet hozzáadni a térkép kép sprite `map.imageSprite.createFromTemplate` erőforrások a függvény használatával. Ez a funkció legfeljebb öt paraméter átadását teszi lehetővé;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

Az `id` egy egyedi azonosítót hoz létre. A `id` van rendelve a képhez, amikor hozzá van adva a térképek kép sprite. Ezzel az azonosítóval megadhatja, hogy melyik képerőforrást szeretné megjeleníteni a rétegekben. A `templateName` program meghatározza, hogy melyik képsablont kell használni. A `color` beállítás beállítja a kép `secondaryColor` elsődleges színét, a beállítások pedig a kép másodlagos színét. A `scale` beállítás méretezi a képsablont, mielőtt alkalmazná azt a kép sprite.The option scales the image template before applyit to the image sprite. Amikor a képet alkalmazza a kép sprite, ez át alakul png. Az éles renderelés biztosítása érdekében jobb, ha a sprite-hoz való hozzáadás előtt felskálázza a képsablont, mint egy rétegben.

Ez a függvény aszinkron módon betölti a képet a kép sprite. Így egy ígéret, hogy megvárhatja, amíg ez a funkció befejeződik.

A következő kód bemutatja, hogyan hozhat létre képet az egyik beépített sablonból, és hogyan használhatja azt szimbólumréteggel.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Képsablon használata szimbólumréteggel

Miután egy képsablon betöltődik a térképképsprite-ba, szimbólumként renderelhető egy szimbólumrétegben, `image` ha `iconOptions`a képerőforrás-azonosítóra hivatkozik a lehetőségben.

A következő minta egy szimbólumréteget jelenít meg a `marker-flat` képsablon használatával, egy fehér elsődleges színnel és egy fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Szimbólumréteg beépített ikonsablonnal" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Tollszimbólum réteget az Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps ( ) <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>beépített ikonsablonjával</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Képsablon használata vonalak útvonala mentén

Miután egy képsablon betöltődik a térképképsprite-ba, egy vonal útvonala mentén renderelhető, ha `lineSpacing`egy LineString-et ad hozzá egy adatforráshoz, és egy szimbólumréteget használ egy opcióval, és hivatkozik a képerőforrás azonosítójára a `image` th `iconOptions`opcióban. 

A következő minta egy rózsaszín vonalat jelenít meg `car` a térképen, és egy szimbólumréteget használ a képsablon használatával, skicskás kék elsődleges színnel és fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Vonalréteg beépített ikonsablonnal" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io'>CodePen</a>beépített ikonsablonját<a href='https://codepen.io/azuremaps'>@azuremaps</a>az Azure Maps ( ) beépített <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>ikonsablonjával.</a>
</iframe>

> [!TIP]
> Ha a képsablon felfelé `rotation` mutat, állítsa a szimbólumréteg ikonbeállítását 90-re, ha azt szeretné, hogy a vonal irányába mutasson.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Sokszögréteggel rendelkező képsablon használata

Miután egy képsablon betöltődik a térképkép sprite, akkor lehet renderelni, mint egy kitöltési `fillPattern` minta egy sokszög réteg hivatkozva a kép erőforrás-azonosító a lehetőséget a réteg.

A következő minta egy sokszögréteget jelenít meg a `dot` képsablon használatával piros elsődleges színnel és átlátszó másodlagos színnel.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sokszög kitöltése beépített ikonsablonnal" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>Tollkitöltés sokszöget az</a> Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps ( ) beépített ikonsablonjával a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

> [!TIP]
> A kitöltési minták másodlagos színének beállítása megkönnyíti az alapul szolgáló térkép megtekintését, így továbbra is biztosítja az elsődleges mintát. 

## <a name="use-an-image-template-with-an-html-marker"></a>HTML-jelölővel rendelkező képsablon használata

A képsablon a `altas.getImageTemplate` függvény használatával beolvasható, és HTML-jelölő tartalomként használható. A sablon átadható `htmlContent` a jelölő beállításának, majd `color`testreszabható a , `secondaryColor`és `text` a lehetőségekkel.

A következő minta a `marker-arrow` sablont piros elsődleges színnel, rózsaszín másodlagos színnel és "00" szöveges értékkel használja.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-jelölő beépített ikonsablonnal" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen HTML Marker t beépített<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>ikonsablonnal</a> az Azure Maps ( ) által a <a href='https://codepen.io'>CodePen oldalon.</a>
</iframe>

## <a name="create-custom-reusable-templates"></a>Egyéni újrafelhasználható sablonok létrehozása

Ha az alkalmazás ugyanazt az ikont használja különböző ikonokkal, vagy ha olyan modult hoz létre, amely további képsablonokat ad hozzá, egyszerűen hozzáadhatja és lekérheti ezeket az ikonokat az Azure Maps webes SDK-ból. Használja az alábbi statikus `atlas` függvényeket a névtéren.

| Név | Visszatérés típusa | Leírás | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Egyéni SVG-képsablon hozzáadása az atlasz névtérhez. |
|  `getImageTemplate(templateName: string, scale?: number)`| sztring | Név szerint lekéri az SVG-sablont. |
| `getAllImageTemplateNames()` | karakterlánc[] |  Név szerint lekéri az SVG-sablont. |

Az SVG-lemezképsablonok a következő helyőrző értékeket támogatják:

| Helyőrző | Leírás |
|-|-|
| `{color}` | Az elsődleges szín. | 
| `{secondaryColor}` | A másodlagos szín. | 
| `{scale}` | Az SVG-kép png-képpé alakul át, amikor hozzáadja a térképképsprite-hoz. Ez a helyőrző a sablon konvertálása előtt méretezhető, hogy az egyértelműen jelenjen meg. | 
| `{text}` | Html-jelölővel használva a szöveg megjelenítésének helye. |

A következő példa bemutatja, hogyan kell egy SVG-sablont, és adja hozzá az Azure Maps webes SDK újrafelhasználható ikonsablonként. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Egyéni ikonsablon hozzáadása az Atlas névtérhez" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Lásd a Toll <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>egyéni ikonsablon hozzáadása atlasz névtérhez</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) segítségével a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="list-of-image-templates"></a>Képsablonok listája

Ez a táblázat az Azure Maps webes SDK-n belül jelenleg elérhető összes képsablont sorolja fel. A sablon neve minden kép felett van. Alapértelmezés szerint az elsődleges szín a kék, a másodlagos szín pedig a fehér. Annak érdekében, hogy a másodlagos szín fehér háttéren láthatóbbá legyen, a következő képeken a másodlagos szín fekete színűre van állítva.

**Szimbólumikonsablonok**

|||||
|:-:|:-:|:-:|:-:|
| Marker | marker-vastag | jelölőkör | jelölő-lapos |
|![jelölő ikon](./media/image-templates/marker.png)|![jelölő-vastag ikon](./media/image-templates/marker-thick.png)|![jelölőkör ikon](./media/image-templates/marker-circle.png)|![jelölő-lapos ikon](./media/image-templates/marker-flat.png)|
||||
| jelölő-négyzet | marker-négyzet-klaszter | jelölő-nyíl | jelölő-golyóstű | 
|![jelölő-négyzet ikon](./media/image-templates/marker-square.png)|![jelölő-négyzet-fürt ikon](./media/image-templates/marker-square-cluster.png)|![jelölő-nyíl ikon](./media/image-templates/marker-arrow.png)|![marker-ball-pin ikon](./media/image-templates/marker-ball-pin.png)|
||||
| jelölő-négyzet-lekerekített | marker-négyzet-lekerekített-klaszter | flag | jelző-háromszög |
| ![jelölő-négyzet-lekerekített ikon](./media/image-templates/marker-square-rounded.png) | ![jelölő-négyzet-lekerekített-fürt ikon](./media/image-templates/marker-square-rounded-cluster.png) | ![jelző ikon](./media/image-templates/flag.png) | ![jelzőháromszög ikon](./media/image-templates/flag-triangle.png) |
||||
| háromszög | háromszög-vastag | háromszög-nyíl felfelé | háromszög-nyíl-balra |
| ![háromszög ikon](./media/image-templates/triangle.png) | ![háromszög-vastag ikon](./media/image-templates/triangle-thick.png) | ![háromszög-felfelé nyíl ikon](./media/image-templates/triangle-arrow-up.png) | ![háromszög-nyíl-balra ikon](./media/image-templates/triangle-arrow-left.png) |
||||
| Hatszög | hatszög-vastag | hatszög-lekerekített | hatszög-kerek-vastag |
| ![hatszög ikon](./media/image-templates/hexagon.png) | ![hatszög-vastag ikon](./media/image-templates/hexagon-thick.png) | ![hatszögalakú anonika](./media/image-templates/hexagon-rounded.png) | ![hatszög-kerek-vastag ikon](./media/image-templates/hexagon-rounded-thick.png) |
||||
| Pin | tűs kör | lekerekített négyzet | lekerekített négyzetes vastag |
| ![gombostű ikon](./media/image-templates/pin.png) | ![tűs kör ikon](./media/image-templates/pin-round.png) | ![lekerekített négyzet eson](./media/image-templates/rounded-square.png) | ![lekerekített négyzetes vastag ikon](./media/image-templates/rounded-square-thick.png) |
||||
| nyíl felfelé | nyíl-fel-vékony | car ||
| ![nyílfelfelé ikon](./media/image-templates/arrow-up.png) | ![nyíl-fel-vékony ikon](./media/image-templates/arrow-up-thin.png) | ![az autó ikonja](./media/image-templates/car.png) | |

**Sokszög kitöltési mintasablonjai**

|||||
|:-:|:-:|:-:|:-:|
| Ellenőrző | ellenőrző elforgatott | Körök | körök-elosztott |
| ![ellenőrző ikonja](./media/image-templates/checker.png) | ![ellenőrző elforgatott ikonja](./media/image-templates/checker-rotated.png) | ![körök ikonja](./media/image-templates/circles.png) | ![kör-elosztott ikon](./media/image-templates/circles-spaced.png) |
|||||
| átlós-vonalak | átlós vonalak lefelé | átlós csíkok fel | átlós csíkok lefelé |
| ![átlós-line-up ikon](./media/image-templates/diagonal-lines-up.png) | ![átlós vonalak lefelé ikonja](./media/image-templates/diagonal-lines-down.png) | ![átlós csíkok fel](./media/image-templates/diagonal-stripes-up.png) | ![átlós csíkok lefelé ikonja](./media/image-templates/diagonal-stripes-down.png) |
|||||
| rácsvonalak | elforgatott rácsvonalak | elforgatott rácscsíkok | x-kitöltés |
| ![rácsvonalak ikonja](./media/image-templates/grid-lines.png) | ![elforgatott rácsvonalak ikonja](./media/image-templates/rotated-grid-lines.png) | ![elforgatott rácscsíkok ikon](./media/image-templates/rotated-grid-stripes.png) | ![x-kitöltés ikon](./media/image-templates/x-fill.png) |
|||||
| cikk-cakk | cikk-cakk-függőleges | Pontok |  |
| ![Cikk-cakk ikon](./media/image-templates/zig-zag.png) | ![cikk-zag-függőleges ikon](./media/image-templates/zig-zag-vertical.png) | ![dots ikon](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Próbálja ki most eszköz

A következő eszközzel a különböző beépített képsablonokat különböző módokon jelenítheti meg, és testreszabhatja az elsődleges és másodlagos színeket és léptékeket.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ikonsablon beállításai" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg az Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>( ) <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Tollikon sablonbeállításait</a> a <a href='https://codepen.io'>CodePen webhelyen.</a>
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlasz névtér](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Az alábbi cikkekben további kódmintákat, ahol képsablonokat lehet használni:

> [!div class="nextstepaction"]
> [Szimbólumréteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Vonalréteg hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszögréteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)
