---
title: Egyéni html hozzáadása az Azure Maps |} A Microsoft Docs
description: Egyéni html hozzáadása egy Javascript-térkép
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e5cfbc7ddc10edf9b21afce73e3b7f8795fcdac9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46121957"
---
# <a name="add-custom-html-to-the-map"></a>A térkép egyéni HTML hozzáadása

Ez a cikk bemutatja, hogyan például egy képfájlt egy egyéni HTML hozzáadása a térképen.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='466' scrolling='no' title='Egyéni html hozzáadása egy térkép - png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>egyéni html hozzáadása egy térkép - png</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A fenti kód az első kódblokkot egy térkép-objektumot hoz létre. Látható [térkép létrehozásához](./map-create.md) útmutatást.

A második kódblokkot HTML-elemmel egy lemezkép alapján hoz létre.

A legutóbbi kódblokkot használ [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addhtml) függvény a térkép osztály a lemezkép hozzáadása a térkép a megadott pozíciónál.

## <a name="next-steps"></a>További lépések

További információ az osztályok és módszerek a cikk ezt használja:

> [!div class="nextstepaction"]
> [Térkép](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

A maps hozzá további kódot példák a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Keresési eredmények megjelenítése](./map-search-location.md)

> [!div class="nextstepaction"]
> [Koordináta információinak lekérése](./map-get-information-from-coordinate.md)