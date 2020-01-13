---
title: Rajzolási eszköztár hozzáadása térképhez | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá rajzolási eszköztárat térképekhez Microsoft Azure Maps web SDK használatával
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fd235f3f39d67f86c8387add79ca0dbf17dc5906
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911665"
---
# <a name="drawing-tool-events"></a>Rajzolási eszköz eseményei

A térképeken a rajzolási eszközök használatakor gyakran hasznos bizonyos eseményekre reagálni, amikor a felhasználó a térképen rajzol. A következő táblázat felsorolja a `DrawingManager` osztály által támogatott összes eseményt.

| Esemény | Leírás |
|-------|-------------|
| `drawingchanged` | Az alakzatban lévő bármely koordináta hozzáadása vagy módosítása történt. | 
| `drawingchanging` | Akkor aktiválódik, amikor megjelenik egy alakzat előnézeti koordinátái. Például a rendszer többször is kiváltja, ahogy egy koordináta van húzva. | 
| `drawingcomplete` | Egy alakzat szerkesztési módból való befejezése vagy kilépésekor fellépett. |
| `drawingmodechanged` | A rajzolási mód módosult. Az új rajzolási mód átkerül az eseménykezelőbe. |
| `drawingstarted` | Akkor aktiválódik, amikor a felhasználó megkezdi az alakzat rajzolását, vagy szerkesztési módba helyezi az alakzatot.  |

Az alábbi kód azt mutatja be, hogyan működik a rajzolási eszközök moduljának eseményei. Rajzoljon alakzatokat a térképen, és tekintse meg az események tüzet.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolási eszközök eseményei" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>rajzolási eszközeinek eseményeit</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Példák

A következő példák a rajzolási eszközök eseményeit használó gyakori forgatókönyvekre mutatnak.

### <a name="select-points-in-polygon-area"></a>Pontok kijelölése a sokszög területen

A következő kód bemutatja, hogyan figyelheti meg a sokszögeket (sokszögeket, téglalapokat és köröket) ábrázoló alakzatokat, és meghatározhatja, hogy a Térkép mely adatpontjain belül legyen a rajzolt terület. A `drawingcomplete` eseményt a Select logika kiváltására használjuk. A választás logikában a térképen lévő összes adatpontot a rendszer körkörösen és teszteli a rajzolt alakzat sokszög területével együtt. Ez a példa a nyílt forráskódú [Turf. js](https://turfjs.org/) függvénytár használatát teszi lehetővé a térbeli metszet számításának végrehajtásához.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="A rajzolt sokszög területének kijelölése" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat a <a href='https://codepen.io/azuremaps/pen/XWJdeja'>rajzolt sokszög területen</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rajzolás és keresés a sokszög területén

A következő kód bemutatja, hogyan végezheti el az érdeklődési pontok keresését egy alakzat területén, miután a felhasználó befejezte az alakzat rajzolását. A rendszer a `drawingcomplete` eseményt használja a keresési logika elindításához. Ha a felhasználó téglalapot vagy sokszöget rajzol, a rendszer a geometrián belüli keresést hajtja végre. Ha kört rajzol, a rendszer a sugár és a középpont pozícióját használja egy érdekes hely kereséséhez. A `drawingmodechanged` eseményt a rendszer annak meghatározására használja, hogy a felhasználó mikor váltson át rajzolási módba, és törli a vásznat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rajzolás és keresés a sokszög területén" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Rajzolás és keresés a sokszög területén</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) használatával a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Mérési eszköz létrehozása

A következő kód azt mutatja be, hogyan használhatók a rajzolási események egy mérési eszköz létrehozásához. A `drawingchanging` a rajzolt alakzat figyelésére szolgál. Ahogy a felhasználó áthelyezi az egeret, a rendszer kiszámítja az alakzat dimenzióit. A `drawingcomplete` eseményt a rendszer az alakzat végső számításának végrehajtásához használja a megrajzolása után. A `drawingmodechanged` esemény segítségével határozható meg, hogy a felhasználó mikor váltson át rajzolási módba, és törli a vászon és a régi mérési adatokat.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mérési eszköz" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>mérési eszközét</a> Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan használhatja a rajzolási eszközök modul további funkcióit:

> [!div class="nextstepaction"]
> [Alakzatadatok beolvasása](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interakció típusa és billentyűparancsok](drawing-tools-interactions-keyboard-shortcuts.md)

További információ a Services modulról:

> [!div class="nextstepaction"]
> [Szolgáltatások modul](how-to-use-services-module.md)

További példák a kód megadására:

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)
