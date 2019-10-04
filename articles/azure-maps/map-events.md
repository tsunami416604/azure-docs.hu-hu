---
title: Események kezelése a Azure Mapskal | Microsoft Docs
description: Interaktív web SDK-Térkép készítése térképi eseményekkel
author: jingjing-z
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c5b4ed73d7bc4d89a67280a0bb183f374ae093d8
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70899433"
---
# <a name="interact-with-the-map"></a>A térképpel való interakció

Ez a cikk bemutatja, hogyan használhatja a [map Class Events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events) tulajdonságot a Térkép eseményeinek és a Térkép különböző rétegeinek kiemeléséhez. Azt is bemutatja, hogyan használhatja a Map Class Events tulajdonságot az események kiemelésére a HTML-jelölővel való interakció során.

## <a name="interact-with-the-map"></a>A térképpel való interakció

Játsszon a lenti térképsel, és tekintse meg a jobb oldalon látható, a megfelelő egérmutatóval jelölt eseményeket. A **js lapra** kattintva megtekintheti és szerkesztheti a JavaScript-kódot. Kattintson a **Szerkesztés CodePen** gombra, és szerkessze a kódot a CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interakció a térképsel – egér eseményei' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>a Térkép – az egér eseményei</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>A Térkép rétegekkel való interakció

A következő kód kiemeli az események nevét, amelyekkel a rendszer a szimbólum réteggel folytatott kommunikáció során felveszi a munkát. A szimbólum, a buborék, a vonal és a sokszög réteg egyaránt támogatja ugyanezeket az eseményeket. A Heat Térkép és a csempe réteg nem támogatja ezeket az eseményeket.

<br/>

<iframe height='600' scrolling='no' title='Interakció a térképekkel – rétegbeli események' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört a <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Térkép – réteg eseményeivel</a> .
</iframe>

## <a name="interact-with-html-marker"></a>Interakció a HTML-jelölővel

A következő kód hozzáadja a JavaScript-leképezési eseményeket egy HTML-jelölőhöz. Kiemeli továbbá azon események nevét is, amelyek a HTML-jelölővel való interakció során felkészülnek.

<br/>

<iframe height='500' scrolling='no' title='Interakció a Térkép-HTML-jelölő eseményeivel' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Térkép-HTML jelölő eseményeivel kommunikáló</a> tollat.
</iframe>

A következő táblázat felsorolja az összes támogatott leképezési osztály eseményét.

| Esemény             | Leírás |
|-------------------|-------------|
| boxzoomend        | A "Box zoom" interakció befejezése után.|
| boxzoomstart      | A "Box zoom" interakció indításakor indul el.|
| kattintson             | Egy mutató eszköz megnyomásakor és a térképen való kikapcsolásakor jelenik meg.|
| bezárás             | Akkor aktiválódik, ha a felugró ablak manuálisan vagy programozott módon van lezárva.|
| ContextMenu       | Akkor következik be, amikor rákattintanak az egér jobb oldali gombjára.|
| dataadded         | Az adatforráshoz való hozzáadáskor aktiválódik.|
| dataremoved       | A rendszer az adatforrásból eltávolítja az alakzatokat.|
| datasourceupdated | Az adatforrás-objektum frissítésekor aktiválódik.|
| DblClick          | Akkor következik be, amikor egy mutató eszköz kétszer kattint a térképen megjelenő ponton.|
| húzza              | A Térkép, az előugró ablak vagy a HTML-jelölő "húzás a serpenyőbe" művelete során ismételten felmerül.|
| dragend           | Ha egy "húzás a serpenyőbe" művelet befejeződik, a Térkép, az előugró ablak vagy a HTML-jelölő jelenik meg.|
| dragstart         | Az "húzza a serpenyőbe" interakciót a Térkép, az előugró ablak vagy a HTML-jelölő használatával kezdi meg.|
| hiba             | Hiba bekövetkezésekor következik be.|
| keydown           | A billentyű lenyomásakor aktiválódik.|
| KeyPress          | Egy tipizálható karaktert (ANSI-kulcsot) előállító kulcs megnyomásakor.|
| KeyUp             | Kulcs felszabadításakor.|
| layeradded        | Egy réteg a térképhez való hozzáadásakor aktiválódik.|
| load              | Rögtön az összes szükséges erőforrás letöltését követően, a Térkép első vizuálisan teljes renderelése történt.|
| MouseDown         | Egy mutatóeszköz a térképen való megnyomásakor aktiválódik.|
| MouseMove         | Egy mutató eszköz a térképen való áthelyezésekor.|
| mouseout          | Kilőtt, amikor egy pont eszköz elhagyja a Térkép vászonját.|
| mouseover         | Egy mutató eszköz a térképen való áthelyezésekor.|
| MouseUp           | Egy mutatóeszköz a térképen való kiadása után jelenik meg.|
| Áthelyezése              | Az egyik nézetről a másikra történő animált áttérés során többször is elindítható a felhasználói interakció vagy módszerek eredményeképpen.|
| moveend           | Közvetlenül azután, hogy a Térkép befejezte a váltást az egyik nézetről a másikra, a felhasználói interakció vagy módszerek eredményeképpen.|
| movestart         | Közvetlenül a Térkép megkezdése előtt az egyik nézetről a másikra való áttérést a felhasználói interakció vagy módszerek eredményeképpen lehet megkezdeni.|
| nyitás              | Akkor aktiválódik, amikor a felugró ablak manuálisan vagy programozott módon van megnyitva.|
| betűközű             | Akkor következik be, amikor a Térkép szurok (billenés) megváltozik a felhasználói interakció vagy módszerek eredményeképpen.|
| pitchend          | Közvetlenül a térképi szurok (billenés) befejezése után a rendszer a felhasználói interakció vagy metódusok eredményeképpen változtatja meg a módosítást.|
| pitchstart        | Minden alkalommal, amikor a Térkép szurok (billenés) megkezdi a változást a felhasználói interakció vagy módszerek eredményeképpen.|
| üzemkész             | Akkor aktiválódik, ha a minimálisan szükséges leképezési erőforrások betöltődik, mielőtt a Térkép készen áll a programozással való együttműködésre.|
| renderelési            | <p> A Térkép a képernyőre való rajzolásakor következik be, a következők eredményeképpen:<ul><li>A Térkép pozíciójának, nagyításának, magasságának vagy megjelölésének módosítása</li><li>A Térkép stílusának módosítása</li><li>Adatforrás-forrás módosítása</li><li>Vektoros csempe, GeoJSON-fájl, karakterjel vagy sprite betöltése.</li></ul></p>|
| lakik            | Közvetlenül a Térkép átméretezése után következik be.|
| elforgatása            | Többször is elindítható a "húzás a forgatáshoz" interakció során.|
| rotateend         | A "húzás a forgatáshoz" művelet véget ér.|
| rotatestart       | A "húzás az elforgatásra" művelet elindul.|
| shapechanged      | Egy alakzat objektum tulajdonságának módosításakor aktiválódik.|
| sourceadded       | Akkor aktiválódik, amikor egy adatforrást vagy VectorTileSource adnak hozzá a térképhez.|
| sourceremoved     | Az adatforrás-vagy VectorTileSource eltávolítását a térképről.|
| styledata         | Akkor következik be, amikor a Térkép stílusa betöltődik vagy módosul.|
| tokenacquired     | HRE hozzáférési jogkivonat beszerzése esetén.|
| touchcancel       | Akkor következik be, amikor egy touchcancel esemény kerül a térképen belül.|
| touchend          | Akkor következik be, amikor egy touchend esemény kerül a térképen belül.|
| touchmove         | Akkor következik be, amikor egy touchmove esemény kerül a térképen belül.|
| touchstart        | Akkor következik be, amikor egy touchstart esemény kerül a térképen belül.|
| kerék             | Akkor következik be, amikor egy görgős esemény történik a térképen.|
| Nagyítás              | A felhasználói interakció vagy módszerek eredményeképpen az egyik nagyítási szintről a másikra történő animált áttérés során többször is elindítható.|
| zoomend           | Közvetlenül a Térkép befejezése után az egyik nagyítási szintről a másikra való áttérést a felhasználói interakció vagy módszerek eredményeképpen végezheti el.|
| zoomstart         | Közvetlenül a Térkép megkezdése előtt az egyik nagyítási szintről a másikra vált, a felhasználói interakció vagy módszerek eredményeképpen.|


## <a name="next-steps"></a>További lépések

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [A Azure Maps Services modul használata](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kódminták](https://docs.microsoft.com/samples/browse/?products=azure-maps)
