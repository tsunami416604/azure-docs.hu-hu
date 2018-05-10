---
title: Keresési eredmények az Azure-leképezések |} Microsoft Docs
description: Hogyan hajthat végre egy keresést Azure Maps igénylése, majd az eredmények megtekintése a Javascrip térképen
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
ms.openlocfilehash: f66b1f93d7bc4c2e7c511c10d7091760e8f6d023
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="show-search-results-on-the-map"></a>Keresési eredmények jelenjenek meg a térképen

Ez a cikk bemutatja, hogyan keresési kérés és a keresési eredmények megjelenítése a térképen. 

## <a name="understand-the-code"></a>A kód ismertetése

<iframe height='500' scrolling='no' title='Keresési eredmények megjelenítése a térképen' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>megjelenítése keresési találatok a térképen</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot hoz létre, és hozzáadja a keresési réteget PIN a térképen. Látható [PIN kód hozzáadásához a térképen](./map-add-pin.md) utasításokat.

A harmadik kódblokkot küld egy [XMLHttpRequest](https://xhr.spec.whatwg.org/) való [Azure Maps intelligens egyeztetésű keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

Az utolsó kódblokkot elemzi a bejövő válasz. A sikeres válasz összegyűjti a szélességi és hosszúsági információkat, mindegyik helyen adja vissza. A térkép PIN-kód, a hely pontok hozzáadása, és beállítja a térkép összes a PIN-kódok megjelenítéséhez a határokon.


## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 

* [Azure hozzárendeli az intelligens egyeztetésű keresési API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
