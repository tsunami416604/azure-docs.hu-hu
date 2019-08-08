---
title: Képsablonok a Azure Maps web SDK-ban | Microsoft Docs
description: Hogyan használhatók a képsablonok a Azure Maps web SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 1ca29fafae1269b21f4e39a9d2594f84af035d25
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856688"
---
# <a name="how-to-use-image-templates"></a>A képsablonok használata

A képek HTML-jelölőket és különböző rétegeket használhatnak a Azure Maps web SDK-n belül:

 - A szimbólumok rétegei képikont ábrázoló pontokat adhatnak a térképen. A szimbólumok a sorok elérési útja mentén is megjeleníthető.
 - A sokszög rétegek kitöltési minta képpel is megjeleníthető. 
 - A HTML-jelölők képek és más HTML-elemek használatával adhatnak ki pontokat.

A megfelelő teljesítmény biztosítása érdekében ezeket a képeket a renderelés előtt be kell tölteni a Térkép rendszerképének sprite-erőforrásba. A SymbolLayer [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions) előre betölt néhány jelölő képet néhány színben a térképi kép sprite-ban alapértelmezés szerint. Ugyanezek a jelölő rendszerképek és további SVG-sablonok is elérhetők, és használhatók egyéni léptékű rendszerképek létrehozására, valamint az ügyfél elsődleges és másodlagos színére. Összesen 42 rendszerkép-sablon van megadva; 27 szimbólum ikonjai és 15 sokszög kitöltési mintázata

A `map.imageSprite.createFromTemplate` képsablonok a függvény használatával adhatók hozzá a Térkép-rendszerkép sprite-erőforrásaihoz. Ez a függvény legfeljebb öt paraméter átadását teszi lehetővé;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

ahol a `id` az egy egyedi azonosító, amelyet a rendszer a képhez rendelt, amikor hozzáadja őket a Maps-képhez a sprite-hoz. Használja ezt az azonosítót a rétegek között annak meghatározásához, hogy melyik képerőforrást szeretné megjeleníteni. Az `templateName` határozza meg, hogy melyik képsablont kell használni. A `color` beállítás megadja a rendszerkép elsődleges színét, és a `secondaryColor` beállítások a rendszerkép másodlagos színét állítja be. A `scale` beállítással méretezhető a Képsablon, mielőtt alkalmazza azt a képen látható sprite-ra. Ha a képet a rendszer a képre alkalmazza a sprite-ra, a rendszer PNG-re konvertálja. A ropogós renderelés érdekében érdemes a képsablont a sprite-ra méretezni, mielőtt felvenné azt egy rétegben.

Ez a függvény aszinkron módon tölti be a képet a sprite-ba, így egy olyan ígéretet ad vissza, amely megvárhatja, hogy a függvény befejeződjön.

A következő kód bemutatja, hogyan hozhat létre egy rendszerképet az egyik beépített sablonból, és hogyan használhatja azt egy szimbólum réteggel.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Képsablon használata szimbólum réteggel

Miután betöltötte a képsablont a térképi képen a sprite-ba, a képerőforrás- `image` `iconOptions`azonosítóra hivatkozva szimbólumként is megjeleníthető.

Az alábbi minta egy szimbólum réteget jelenít meg a `marker-flat` képsablonban egy kékeszöld elsődleges színnel és egy fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Beépített ikon sablonnal rendelkező szimbólum réteg" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>szimbólum réteget a beépített ikon sablonnal</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Képsablon használata a vonalak elérési útja mentén

Miután betöltötte a képsablont a térképi képen a sprite-ba, azt a vonal elérési útján jelenítheti meg, ha hozzáad egy LineString egy adatforráshoz, és egy `lineSpacing`szimbólum réteget használ egy kapcsolóval, és a képerőforrás azonosítóját használja `image` az o lehetőségben. f th `iconOptions`. 

Az alábbi minta egy rózsaszín vonalat jelenít meg a térképen, és egy szimbólum réteget használ a `car` Képsablon használatával, egy Vagány kék elsődleges színnel és egy fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Beépített ikon sablonnal rendelkező vonal réteg" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a Pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>line réteget a beépített ikon sablonnal</a> .
</iframe>

> [!TIP]
> Ha a Képsablon felfelé mutat, állítsa a `rotation` szimbólum réteg ikonját a 90 értékre, ha azt szeretné, hogy a sor azonos irányba mutasson.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Képsablon használata sokszög réteggel

Miután betöltötte a képsablont a térképi képen a sprite-ba, a képerőforrás- `fillPattern` azonosítóra hivatkozva kitöltési mintaként jelenítheti meg a réteget.

Az alábbi minta egy sokszög réteget jelenít meg a `dot` képsablonnal, vörös elsődleges színnel és transzparens másodlagos színnel.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A sokszög kitöltése beépített ikon sablonnal" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollas <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>kitöltés sokszögét a beépített ikon sablonnal</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Kitöltési minták másodlagos színének beállítása megkönnyíti az alapul szolgáló Térkép megtekintését, így továbbra is az elsődleges mintát fogja biztosítani. 

## <a name="use-an-image-template-with-an-html-marker"></a>Képsablon használata HTML-jelölővel

Egy Képsablon lekérhető a `altas.getImageTemplate` függvény használatával, és a HTML-jelölő tartalmának használata. A sablon átadható `htmlContent` a jelölőnek, majd testreszabható a `color`, `secondaryColor`, és `text` kapcsolók használatával.

Az alábbi minta egy vörös `marker-arrow` elsődleges színnel, egy rózsaszín másodlagos színnel és egy "00" szöveges értékkel rendelkező sablont használ.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-jelölő beépített ikon sablonnal" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a Pen HTML-jelölőt a <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>beépített ikon sablonnal</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Egyéni újrafelhasználható sablonok létrehozása

Ha az alkalmazás ugyanazt az ikont használja különböző ikonokkal, vagy ha olyan modult hoz létre, amely további képsablonokat ad hozzá, egyszerűen hozzáadhat és lekérheti ezeket az ikonokat a Azure Maps web SDK-ból `atlas` a következő statikus függvények használatával: névtér.

| Name (Név) | Visszatérési típus | Leírás | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Hozzáadja az egyéni SVG-képsablont az Atlas-névtérhez. |
|  `getImageTemplate(templateName: string, scale?: number)`| sztring | Egy SVG-sablon beolvasása név alapján. |
| `getAllImageTemplateNames()` | karakterlánc [] |  Egy SVG-sablon beolvasása név alapján. |

Az SVG-képsablonok a következő helyőrző értékeket támogatják:

| Helyőrző | Leírás |
|-|-|
| `{color}` | Az elsődleges szín. | 
| `{secondaryColor}` | A másodlagos szín | 
| `{scale}` | Az SVG-képet png-képpé alakítja a rendszer, amikor hozzáadja őket a Térkép képéhez sprite. Ez a helyőrző felhasználható a sablon méretezésére a konvertálás előtt, így biztosítva, hogy a rendszer világosan megjelenítse. | 
| `{text}` | A szöveg megjelenítésének helye HTML-jelölővel való használatkor. |

Az alábbi példa bemutatja, hogyan hozhat létre SVG-sablont, és hogyan adhatja hozzá a Azure Maps web SDK-hoz újrafelhasználható ikon sablonként. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Egyéni ikon-sablon hozzáadása az Atlas-névtérhez" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Egyéni ikon-sablon hozzáadása az Atlas-névtérhez</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) lehetőséget a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Képsablonok listája

A következő táblázat felsorolja a Azure Maps web SDK-ban jelenleg elérhető összes képsablont az egyes rendszerképek feletti sablon nevével. Alapértelmezés szerint az elsődleges szín kék színű, a másodlagos szín pedig fehér. Ahhoz, hogy a másodlagos szín könnyebben látható legyen a fehér háttéren, a következő képeknél a másodlagos szín feketére van állítva.

**Szimbólum ikon sablonjai**

|||||
|:-:|:-:|:-:|:-:|
| marker | jelölő – vastag | jelölő – kör | jelölő – lapos |
|![jelölő ikon](./media/image-templates/marker.png)|![jelölő – vastag ikon](./media/image-templates/marker-thick.png)|![jelölő – kör ikon](./media/image-templates/marker-circle.png)|![jelölő – lapos ikon](./media/image-templates/marker-flat.png)|
||||
| jelölő – négyzet | jelölő-négyzet-fürt | jelölő – nyíl | jelölő – golyós PIN-kód | 
|![jelölő – négyzet ikon](./media/image-templates/marker-square.png)|![jelölő – négyzet-fürt ikon](./media/image-templates/marker-square-cluster.png)|![jelölő – nyíl ikon](./media/image-templates/marker-arrow.png)|![jelölő-labda-PIN ikon](./media/image-templates/marker-ball-pin.png)|
||||
| jelölő – négyzet kerekített | jelölő-négyzet-kerekített fürt | zászló | jelölő – háromszög |
| ![jelölő – négyzet alakú lekerekített ikon](./media/image-templates/marker-square-rounded.png) | ![jelölő-négyzet-lekerekített fürt ikonja](./media/image-templates/marker-square-rounded-cluster.png) | ![jelölő ikon](./media/image-templates/flag.png) | ![jelölő – háromszög ikon](./media/image-templates/flag-triangle.png) |
||||
| háromszög | háromszög – vastag | háromszög – nyíl fel | háromszög – nyíl balra |
| ![háromszög ikon](./media/image-templates/triangle.png) | ![háromszög – vastag ikon](./media/image-templates/triangle-thick.png) | ![háromszög-nyíl ikon](./media/image-templates/triangle-arrow-up.png) | ![háromszög – nyíl bal oldali ikon](./media/image-templates/triangle-arrow-left.png) |
||||
| hatszög | hatszög – vastag | hatszög – lekerekített | hatszög – kerekített – vastag |
| ![hatszög ikon](./media/image-templates/hexagon.png) | ![hatszögletű – vastag ikon](./media/image-templates/hexagon-thick.png) | ![hatszög – lekerekített ikon](./media/image-templates/hexagon-rounded.png) | ![hatszög – kerekített vastagságú ikon](./media/image-templates/hexagon-rounded-thick.png) |
||||
| rögzítés | rögzítési kör | lekerekített négyzet | lekerekített négyzetes vastag |
| ![rögzítés ikon](./media/image-templates/pin.png) | ![rögzítési kör ikonja](./media/image-templates/pin-round.png) | ![lekerekített négyzet ikon](./media/image-templates/rounded-square.png) | ![lekerekített négyzetes vastagságú ikon](./media/image-templates/rounded-square-thick.png) |
||||
| nyíl fel | nyíl – vékony | autó ||
| ![nyíl ikon](./media/image-templates/arrow-up.png) | ![nyíl – vékony ikon](./media/image-templates/arrow-up-thin.png) | ![autós ikon](./media/image-templates/car.png) | |

**Sokszög kitöltési mintájának sablonjai**

|||||
|:-:|:-:|:-:|:-:|
| Checker | ellenőr – elforgatva | körök | körökre lefoglalt |
| ![ellenőrzési ikon](./media/image-templates/checker.png) | ![ellenőr által elforgatott ikon](./media/image-templates/checker-rotated.png) | ![körök ikon](./media/image-templates/circles.png) | ![körökre kihelyezett ikon](./media/image-templates/circles-spaced.png) |
|||||
| átlós vonalak – felfelé | átlós vonalak – lefelé | átlós sávok – felfelé | átlós sávok – lefelé |
| ![átlós vonalak – felfelé ikon](./media/image-templates/diagonal-lines-up.png) | ![átlós vonalak – lefelé ikon](./media/image-templates/diagonal-lines-down.png) | ![átlós sávok – ikon](./media/image-templates/diagonal-stripes-up.png) | ![átlós sáv – lefelé ikon](./media/image-templates/diagonal-stripes-down.png) |
|||||
| rács – vonalak | elforgatva – rács – vonalak | elforgatva – Grid-Stripes | x – kitöltés |
| ![rács – vonalak ikon](./media/image-templates/grid-lines.png) | ![elforgatva – rács – vonalak ikon](./media/image-templates/rotated-grid-lines.png) | ![elforgatva – Grid-Stripes ikon](./media/image-templates/rotated-grid-stripes.png) | ![x-Fill ikon](./media/image-templates/x-fill.png) |
|||||
| Zig-Zag | Zig-Zag-vertikális | pontok |  |
| ![Zig-Zag ikon](./media/image-templates/zig-zag.png) | ![Zig-Zag-függőleges ikon](./media/image-templates/zig-zag-vertical.png) | ![pontok ikon](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Kipróbálás most eszköz

Az alábbi eszközzel különböző módokon jelenítheti meg a különböző beépített képsablonokat, és testre szabhatja az elsődleges és másodlagos színeket és a méretezést.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ikon sablon beállításai" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>ikon sablonjának beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [Atlas-névtér](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

A következő cikkekből megtudhatja, hogy miként használhatók a képsablonok:

> [!div class="nextstepaction"]
> [Szimbólum réteg hozzáadása](map-add-pin.md)

> [!div class="nextstepaction"]
> [Alakzat hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)
