---
title: Adja hozzá az egyéni html Azure leképezésekben |} Microsoft Docs
description: Egyéni html hozzáadása a Javascript-leképezés
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3ee1f394536061cecaa5d775650149b30098560e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600221"
---
# <a name="add-custom-html-to-the-map"></a>A térkép egyéni HTML hozzáadása

Ez a cikk bemutatja egy egyéni HTML, például a képfájl felvétele a térképen. 

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='466' scrolling='no' title='Egyéni html felvétele a térképre - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>egyéni html felvétele a térképre - png</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kódot az első kódblokkot egy térkép objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) utasításokat.

A második kódblokkot egy HTML-elem lemezképből hoz létre.

Az utolsó kódblokkot használ [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) függvény a térkép osztály a lemezkép hozzáadása a térkép a megadott helyre.

## <a name="next-steps"></a>További lépések

További tudnivalók az osztályok és az ebben a cikkben használt módszerek: 
* [térkép](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
    
További kód hozzáadása a maps, tekintse meg a következő cikkeket: 
* [Keresési eredmények megjelenítése](./map-search-location.md)
* [Információ lekérése a koordináta](./map-get-information-from-coordinate.md)

