---
title: Az Azure-leképezések egy koordináta információkat jelenít meg |} Microsoft Docs
description: Hogyan egy címet kapcsolatos információk megjelenítéséhez a térképen, amikor a felhasználó kiválaszt egy koordináta
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: bb8644724cc872a0a8bc331e76251218492fd93d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="get-information-from-a-coordinate"></a>Információ lekérése a koordináta

Ez a cikk bemutatja, hogyan fordított cím keresést, és kattintásra után megjelenítése ablakra helyének címét egy előugró ablak. 

## <a name="understand-the-code"></a>A kód ismertetése

<iframe height='500' scrolling='no' title='Információ lekérése a koordináta' src='//codepen.io/azuremaps/embed/ddXzoB/?height=516&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ddXzoB/'>információt lekérni egy koordináta</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot mutató frissíti az egérmutatót stílusát.

A harmadik kódblokkot egy előugró ablak hoz létre. Látható [egy előugró ablak hozzáadása a térképen](./map-add-popup.md) utasításokat.

Az utolsó kódblokk egy esemény-figyelőhöz a kattintások hozzáadja. Egy kattintás után küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps fordított cím Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Sikeres választ, a cím ablakra hely gyűjt, és határozza meg a helyi menü Tartalom megjelenítése és elhelyezése keresztül [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions) funkció a helyi menü osztály

## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 
* [Fordított cím keresése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addEventListener](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addeventlistener)
* [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [setPopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#setpopupoptions)
    * [Nyissa meg](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zárja be](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)
