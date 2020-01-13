---
title: Képsablonok a Azure Maps web SDK-ban | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan használhatók a képsablonok HTML-jelölővel és különböző rétegekkel a Microsoft Azure Maps web SDK-ban.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: cb182a5db77a517b11fb1863665f8c54d58b254a
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911569"
---
# <a name="how-to-use-image-templates"></a>Rendszerképsablonok használata

A képek HTML-jelölővel és a Azure Maps web SDK-n belüli különböző rétegekkel is használhatók:

 - A szimbólumok rétegei képikont ábrázoló pontokat adhatnak a térképen. A szimbólumok a sorok elérési útja mentén is megjeleníthető.
 - A sokszög rétegek kitöltési minta képpel is megjeleníthető. 
 - A HTML-jelölők képek és más HTML-elemek használatával adhatnak ki pontokat.

A megfelelő teljesítmény biztosítása érdekében ezeket a képeket a renderelés előtt be kell tölteni a Térkép rendszerképének sprite-erőforrásba. A SymbolLayer [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions) előre betölt néhány jelölő képet néhány színben a térképi kép sprite-ban alapértelmezés szerint. Ugyanezek a jelölő rendszerképek és további SVG-sablonok is elérhetők, és használhatók egyéni léptékű rendszerképek létrehozására, valamint az ügyfél elsődleges és másodlagos színére. Összesen 42 rendszerkép-sablon van megadva; 27 szimbólum ikonjai és 15 sokszög kitöltési mintázata

A képsablonok a `map.imageSprite.createFromTemplate` függvény használatával adhatók hozzá a Térkép képének-erőforrásaihoz. Ez a függvény legfeljebb öt paraméter átadását teszi lehetővé;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

ahol a `id` egy egyedi azonosító, amelyet a rendszer akkor hoz létre, amikor a rendszer hozzáadja a képhez a Maps-képhez sprite-hoz. Használja ezt az azonosítót a rétegek között annak meghatározásához, hogy melyik képerőforrást szeretné megjeleníteni. A `templateName` megadja, hogy melyik képsablont kell használni. A `color` beállítás megadja a rendszerkép elsődleges színét, és a `secondaryColor` beállítások a rendszerkép másodlagos színét állítja be. A `scale` lehetőség a Képsablon méretezése előtt méretezi a képet a sprite-ra. Ha a képet a rendszer a képre alkalmazza a sprite-ra, a rendszer PNG-re konvertálja. A ropogós renderelés érdekében érdemes a képsablont a sprite-ra méretezni, mielőtt felvenné azt egy rétegben.

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

Miután betöltötte a képsablont a térképi képen a sprite-ban, szimbólumként jeleníthető meg a képerőforrás-AZONOSÍTÓra hivatkozva a `iconOptions``image` lehetőségében.

Az alábbi minta egy szimbólum réteget jelenít meg a `marker-flat` képsablonnal, amely egy kékeszöld elsődleges színnel és egy fehér másodlagos színnel jelenik meg. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Beépített ikon sablonnal rendelkező szimbólum réteg" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>szimbólum réteget a beépített ikon sablonnal</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Képsablon használata a vonalak elérési útja mentén

Miután betöltötte a képsablont a Térkép képére, az egy vonal elérési útján jeleníthető meg egy LineString egy adatforráshoz való hozzáadásával, valamint egy `lineSpacing`lehetőséggel rendelkező szimbólum-réteggel és a képerőforrás AZONOSÍTÓjának hivatkozásával a th `iconOptions``image` lehetőségnél. 

Az alábbi minta egy rózsaszín vonalat jelenít meg a térképen, és egy szimbólum réteget használ a `car` Képsablon használatával, egy Vagány kék elsődleges színnel és egy fehér másodlagos színnel. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Beépített ikon sablonnal rendelkező vonal réteg" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a toll <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>vonal réteget a beépített ikon sablonnal</a> .
</iframe>

> [!TIP]
> Ha a Képsablon felfelé mutat, állítsa a szimbólum réteg `rotation` ikonját a 90 értékre, ha azt szeretné, hogy az a vonalhoz hasonló irányba mutasson.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Képsablon használata sokszög réteggel

Miután betöltötte a képsablont a térképi képen a sprite-ba, a képerőforrás-AZONOSÍTÓra hivatkozva kitöltési mintaként jelenítheti meg a réteg `fillPattern` lehetőségében.

Az alábbi minta egy sokszög réteget jelenít meg a `dot` képsablonnal, vörös elsődleges színnel és átlátszó másodlagos színnel.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A sokszög kitöltése beépített ikon sablonnal" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával megtekintheti a tollas <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>kitöltés sokszögét beépített ikonos sablonnal</a> .
</iframe>

> [!TIP]
> A Kitöltési minták másodlagos színének beállítása megkönnyíti az alapul szolgáló Térkép megtekintését, így továbbra is az elsődleges mintát fogja biztosítani. 

## <a name="use-an-image-template-with-an-html-marker"></a>Képsablon használata HTML-jelölővel

A Képsablon lekérhető a `altas.getImageTemplate` függvénnyel, és HTML-jelölő tartalmának használatával. A sablon átadható a jelölő `htmlContent` lehetőségének, majd testreszabható a `color`, a `secondaryColor`és a `text` beállítások használatával.

A következő minta a `marker-arrow` sablont használja piros elsődleges színnel, egy rózsaszín másodlagos színnel és egy "00" szöveges értékkel.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="HTML-jelölő beépített ikon sablonnal" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
A <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML-jelölőjét beépített ikon sablonnal</a> .
</iframe>

## <a name="create-custom-reusable-templates"></a>Egyéni újrafelhasználható sablonok létrehozása

Ha az alkalmazás ugyanazt az ikont használja különböző ikonokkal, vagy ha olyan modult hoz létre, amely további képsablonokat ad hozzá, egyszerűen hozzáadhat és lekérheti ezeket az ikonokat a Azure Maps web SDK-ból a `atlas` névtér következő statikus funkcióival.

| Név | Visszatérési típus | Leírás | 
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
| jelölő – négyzet kerekített | jelölő-négyzet-kerekített fürt | flag | jelölő – háromszög |
| ![jelölő – négyzet alakú lekerekített ikon](./media/image-templates/marker-square-rounded.png) | ![jelölő-négyzet-lekerekített fürt ikonja](./media/image-templates/marker-square-rounded-cluster.png) | ![jelölő ikon](./media/image-templates/flag.png) | ![jelölő – háromszög ikon](./media/image-templates/flag-triangle.png) |
||||
| háromszög | háromszög – vastag | háromszög – nyíl fel | háromszög – nyíl balra |
| ![háromszög ikon](./media/image-templates/triangle.png) | ![háromszög – vastag ikon](./media/image-templates/triangle-thick.png) | ![háromszög-nyíl ikon](./media/image-templates/triangle-arrow-up.png) | ![háromszög – nyíl bal oldali ikon](./media/image-templates/triangle-arrow-left.png) |
||||
| hatszög | hatszög – vastag | hatszög – lekerekített | hatszög – kerekített – vastag |
| ![hatszög ikon](./media/image-templates/hexagon.png) | ![hatszögletű – vastag ikon](./media/image-templates/hexagon-thick.png) | ![hatszög – lekerekített ikon](./media/image-templates/hexagon-rounded.png) | ![hatszög – kerekített vastagságú ikon](./media/image-templates/hexagon-rounded-thick.png) |
||||
| PIN kód | rögzítési kör | lekerekített négyzet | lekerekített négyzetes vastag |
| ![gombostű ikon](./media/image-templates/pin.png) | ![rögzítési kör ikonja](./media/image-templates/pin-round.png) | ![lekerekített négyzet ikon](./media/image-templates/rounded-square.png) | ![lekerekített négyzetes vastagságú ikon](./media/image-templates/rounded-square-thick.png) |
||||
| nyíl fel | nyíl – vékony | car ||
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
Lásd a toll <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>ikon sablonjának beállításait</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Következő lépések

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
> [Vonal rétegének hozzáadása](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Sokszög réteg hozzáadása](map-add-shape.md)

> [!div class="nextstepaction"]
> [HTML-készítők hozzáadása](map-add-bubble-layer.md)
