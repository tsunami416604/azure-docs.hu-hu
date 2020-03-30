---
title: Térképesemények kezelése | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan tehet interaktív webes SDK-leképezést térképes eseményekkel a Microsoft Azure Maps webes SDK használatával.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534898"
---
# <a name="interact-with-the-map"></a>A térkép kölcsönhatása

Ez a cikk bemutatja, hogyan használhatja a [térképesemények osztályt.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) A tulajdonság kiemeli az eseményeket a térképen és a térkép különböző rétegein. A HTML-jelölővel való interakció során is kiemelheti az eseményeket.

## <a name="interact-with-the-map"></a>A térkép kölcsönhatása

Játssz az alábbi térképpel, és nézd meg a megfelelő egéreseményeket a jobb oldalon. A **JS fülre** kattintva megtekintheti és szerkesztheti a JavaScript-kódot. A CodePen kódjának módosításához a **CodePen szerkesztése** gombra is kattinthat.

<br/>

<iframe height='600' scrolling='no' title='Kölcsönhatásban a térkép - egér események' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a Toll <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>használata a térképpel – egéresemények</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Együttműködés a térképrétegekkel

A következő kód kiemeli a kilőtt eseményt, ahogy a szimbólumréteggel kommunikál. A szimbólum, buborék, vonal és sokszög réteg mind ugyanazt az eseménykészletet támogatja. A hőtérkép és a csemperétegek nem támogatják ezeket az eseményeket.

<br/>

<iframe height='600' scrolling='no' title='Kölcsönhatásban áll a térképpel – Rétegesemények' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>tollat a térképpel való interakcióban – Rétegesemények az</a> Azure Maps által (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen oldalon.</a>
</iframe>

## <a name="interact-with-html-marker"></a>Együttműködés a HTML-jelölővel

A következő kód javascript-leképezési eseményeket ad hozzá egy HTML-jelölőhöz. Kiemeli azoknak az eseményeknek a nevét is, amelyek a HTML-jelölővel való interakció során feltüzelnek.

<br/>

<iframe height='500' scrolling='no' title='Kölcsönhatásban áll a térkép - HTML Marker események' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lásd a Toll <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>interakció a térkép - HTML Marker események</a> az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Az alábbi táblázat az összes támogatott térképosztály-eseményt sorolja fel.

| Esemény               | Leírás |
|---------------------|-------------|
| `boxzoomend`        | A "doboznagyítás" művelet befejezésekor aktiválódik.|
| `boxzoomstart`      | A "doboznagyítás" művelet megkezdésekor aktiválódik.|
| `click`             | Akkor aktiválódik, ha megnyom egy mutatóeszközt, és a térkép ugyanazon pontján elengedi.|
| `close`             | Akkor aktiválódik, ha a felugró ablak manuálisan vagy programszerűen van bezárva.|
| `contextmenu`       | Akkor aktiválódik, ha az egér jobb gombjára kattint.|
| `data`              | Akkor aktiválódik, ha bármilyen térképadat betöltődik vagy megváltozik. |
| `dataadded`         | Akkor aktiválódik, ha `DataSource`alakzatokat ad hozzá a hoz.|
| `dataremoved`       | Akkor aktiválódik, ha `DataSource`alakzatokat távolít a ból.|
| `datasourceupdated` | Az objektum `DataSource` frissítésekor aktiválódik.|
| `dblclick`          | Akkor aktiválódik, ha egy mutatóeszközre kétszer kattintanak a térkép ugyanazon pontján.|
| `drag`              | A térképen, a felugró ablakban vagy a HTML-jelölőben a "húzás pásztába" művelet során többször aktiválódik.|
| `dragend`           | Akkor aktiválódik, ha a "húzás a pásztázáshoz" művelet befejeződik a térképen, a felugró ablakban vagy a HTML-jelölőben.|
| `dragstart`         | Akkor aktiválódik, ha a "húzás sal" művelet indul a térképen, a felugró ablakban vagy a HTML-jelölőben.|
| `error`             | Hiba esetén aktiválódik.|
| `idle`              | <p>Az utolsó képkocka megjelenítése után aktiválódik, mielőtt a térkép "tétlen" állapotba lépne:<ul><li>Nincs folyamatban kameraváltás.</li><li>Az összes jelenleg kért csempe betöltve.</li><li>Minden halványítási/átmeneti animáció befejeződött.</li></ul></p>|
| `keydown`           | Akkor lőnek, ha lenyomnak egy gombot.|
| `keypress`          | Akkor aktiválódik, ha megnyom egy olyan billentyűt, amely egy elgépelhető karaktert (ANSI-billentyűt) hoz létre.|
| `keyup`             | A kulcs elengedésekekén.|
| `layeradded`        | Akkor aktiválódik, ha egy réteget ad hozzá a térképhez.|
| `layerremoved`      | Akkor aktiválódik, ha egy réteget eltávolítanak a térképről.|
| `load`              | Azonnal aktiválódott, miután az összes szükséges erőforrást letöltötte, és a térkép első vizuálisan teljes renderelése megtörtént.|
| `mousedown`         | Akkor aktiválódik, ha egy mutatóeszközt megnyom a térképen, vagy ha egy elem tetején van.|
| `mouseenter`        | Akkor aktiválódik, ha egy mutatóeszköz először a térkép vagy egy elem fölé kerül. |
| `mouseleave`        | Akkor aktiválódik, ha egy mutatóeszközt kimozdítanak a térképről vagy egy elemből. |
| `mousemove`         | Akkor aktiválódik, ha egy mutatóeszközt a térképen vagy egy elemen belül mozgat.|
| `mouseout`          | Akkor lőnek ki, amikor egy ponteszköz elhagyja a térkép vásznát, a mi elemünk etet.|
| `mouseover`         | Akkor aktiválódik, ha egy mutatóeszközt helyez át a térképen vagy egy elemen.|
| `mouseup`           | Akkor aktiválódik, ha egy mutatóeszköz felszabadul a térképen belül, vagy ha egy elem tetején van.|
| `move`              | Az egyik nézetből a másikba való animált átmenet során ismételten aktiválódik, akár felhasználói beavatkozás, akár metódus okán.|
| `moveend`           | Közvetlenül azután lett kirúgva, hogy a térkép befejezte az egyik nézetről a másikra való áttérést, akár felhasználói beavatkozás, akár metódusok eredményeként.|
| `movestart`         | Közvetlenül a térkép kezdete előtt indítva az egyik nézetből a másikba való átmenetet, akár felhasználói beavatkozás, akár metódusok eredményeként.|
| `open`              | A felugró ablak manuális vagy programozott megnyitásakor aktiválódik.|
| `pitch`             | Akkor aktiválódik, amikor a térkép hangmagassága (dőlésszöge) megváltozik a felhasználói beavatkozás vagy a módszerek következtében.|
| `pitchend`          | Közvetlenül a térkép hangmagasságának (dőlésszögének) befejezése után aktiválódik a felhasználói beavatkozás vagy a módszerek eredményeként.|
| `pitchstart`        | Akkor aktiválódik, amikor a térkép hangmagassága (dőlésszöge) megváltozik a felhasználói beavatkozás vagy a módszerek eredményeként.|
| `ready`             | Akkor aktiválódik, ha a minimálisan szükséges térképerőforrások betöltődnek, mielőtt a térkép készen áll a programozott interakcióra.|
| `render`            | <p>Akkor aktiválódik, amikor a térkép a képernyőre kerül, a következők alapján:<ul><li>A térkép pozíciójának, nagyításának, hangmagasságának vagy csapágyának módosítása.</li><li>A térkép stílusának megváltoztatása.</li><li>Forrásra `DataSource` váltás.</li><li>Vektorcsempe, GeoJSON-fájl, jeljel vagy sprite betöltése.</li></ul></p>|
| `resize`            | A térkép átméretezése után azonnal tüzeltek.|
| `rotate`            | A "húzással forogni" interakció során többször tüzelt.|
| `rotateend`         | Akkor aktiválódik, ha a "forgatható húzás" művelet véget ér.|
| `rotatestart`       | A "forgatható húzás" művelet indításakor aktiválódik.|
| `shapechanged`      | Alakzatobjektum-tulajdonság módosításakor aktiválódik.|
| `sourcedata`        | Akkor aktiválódik, ha a térkép egyik forrása betöltődik vagy megváltozik, beleértve azt az is, ha egy forráshoz tartozó csempe betöltődik vagy megváltozik. |
| `sourceadded`       | Akkor aktiválódik, ha a `DataSource` vagy `VectorTileSource` hozzáadódik a térképhez.|
| `sourceremoved`     | Akkor aktiválódik, ha a `DataSource` vagy `VectorTileSource` eltávolítják a térképről.|
| `styledata`         | Akkor aktiválódik, amikor a térkép stílusa betöltődik vagy megváltozik.|
| `styleimagemissing` | Akkor aktiválódik, ha egy réteg megpróbál betölteni egy képet a nem létező képsprite-ból |
| `tokenacquired`     | AAD-hozzáférési jogkivonat beszerzése esetén aktiválódik.|
| `touchcancel`       | Akkor aktiválódik, ha egy touchcancel esemény történik a térképen.|
| `touchend`          | Akkor aktiválódik, ha egy érintéses esemény történik a térképen belül.|
| `touchmove`         | Akkor aktiválódik, ha a térképen touchmove esemény történik.|
| `touchstart`        | Akkor aktiválódik, ha egy touchstart esemény történik a térképen.|
| `wheel`             | Akkor aktiválódik, ha egérkerék-esemény következik be a térképen belül.|
| `zoom`              | Az egyik nagyítási szintről a másikra történő animált átmenet során ismételten aktiválódik, akár felhasználói beavatkozás, akár metódus okán.|
| `zoomend`           | Közvetlenül azután lett kirúgva, hogy a térkép befejezte az egyik nagyítási szintről a másikra való áttérést, akár felhasználói beavatkozás, akár metódusok eredményeként.|
| `zoomstart`         | Közvetlenül a térkép kezdete előtt, a felhasználói beavatkozás vagy a módszerek eredményeként az egyik nagyítási szintről a másikra való áttérés megkezdődik.|


## <a name="next-steps"></a>További lépések

A teljes kódpéldákat az alábbi cikkekben talál:

> [!div class="nextstepaction"]
> [Az Azure Maps Services modul használata](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)
