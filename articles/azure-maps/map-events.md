---
title: Egér-események kezelése Azure Mapsokkal | Microsoft Docs
description: Interaktív JavaScript-Térkép készítése térképi eseményekkel
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: da6b183155de0fbc370751254a6842343d280874
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638971"
---
# <a name="interact-with-the-map---mouse-events"></a>Interakció a Térkép-egér eseményeivel

Ez a cikk bemutatja, hogyan használhatja a [map Class](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) [Events](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) tulajdonságot a Térkép eseményeinek és a Térkép különböző rétegeinek kiemeléséhez. Azt is bemutatja, hogyan használhatja a Map Class Events tulajdonságot az események kiemelésére a HTML-jelölővel való interakció során.

## <a name="interact-with-the-map"></a>A térképpel való interakció

<iframe height='600' scrolling='no' title='Interakció a térképsel – egér eseményei' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a tollat <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>a Térkép – az egér eseményei</a> Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() alapján a <a href='https://codepen.io'>CodePen</a>.
</iframe>

Játsszon a fenti térképen, és tekintse meg a jobb oldalon látható megfelelő egérmutatókat. A **js lapra** kattintva megtekintheti és szerkesztheti a JavaScript-kódot. Kattintson a **Szerkesztés CodePen** gombra, és szerkessze a kódot a CodePen.

## <a name="interact-with-map-layers"></a>A Térkép rétegekkel való interakció

<iframe height='600' scrolling='no' title='Interakció a térképekkel – rétegbeli események' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) című témakört a <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Térkép – réteg eseményeivel</a> .
</iframe>

A fenti kód kiemeli annak az eseménynek a nevét, amelyet a rendszer a szimbólum réteggel való interakció során fellőtt. A szimbólum, a buborék, a vonal és a sokszög réteg egyaránt támogatja ugyanezeket az eseményeket. A csempe réteg nem támogatja ezeket az eseményeket.

## <a name="interact-with-html-marker"></a>Interakció a HTML-jelölővel

<iframe height='500' scrolling='no' title='Interakció a Térkép-HTML-jelölő eseményeivel' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a <a href='https://codepen.io'>CodePen</a>-on Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) által a <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Térkép-HTML jelölő eseményeivel kommunikáló</a> tollat.
</iframe>

A fenti kód hozzáadja a JavaScript-leképezési eseményeket egy HTML-jelölőhöz. Kiemeli továbbá azon események nevét is, amelyek a HTML-jelölővel való interakció során felkészülnek.

## <a name="next-steps"></a>További lépések

További információ a cikkben használt osztályokról és módszerekről:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A következő cikkekben talál részletes példákat:

> [!div class="nextstepaction"]
> [A Azure Maps Services modul használata](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)
