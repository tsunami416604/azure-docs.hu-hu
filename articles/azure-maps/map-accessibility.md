---
title: Az Azure Maps szolgáltatásban elérhető kérelem |} A Microsoft Docs
description: Az Azure Maps használata az elérhető alkalmazások készítése
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247228"
---
# <a name="building-an-accessible-application"></a>Elérhető alkalmazások készítése

Ez a cikk bemutatja, hogyan hozhat létre egy térkép-alkalmazás, amely a képernyőolvasó használható.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Az elérhető alkalmazások' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>, hogy az elérhető alkalmazás</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A térkép kisegítő lehetőségeket nyújtó szolgáltatásai az előre összeállított származnak. A felhasználók kaphatnak a billentyűzet használata a térképen. Ha egy képernyőolvasó fut, a térkép értesíti a felhasználót, állapotában módosításait.
Például felhasználó értesítést kap változások térképen a térkép eltolja vagy nagyítása. A kiinduló térképre figyelőszoftvert, amely további adatokat kell rendelkeznie a képernyőolvasót használók megfelelő szöveges adatokat.

Használatával [előugró](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) egyik módja, ennek érdekében. Keresés a fenti példában egy előugró ablak, a szöveges adatokat hozzáadni a térkép a térképre figyelőszoftvert, amely minden PIN-kódot. Használatával a [előugró a](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [csatolása](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) módszer lehetővé teszi, hogy a vizuális megjelenítése a térképen az előugró ablak nélkül láthatók a képernyőolvasó legyenek az előugró ablak.

## <a name="next-steps"></a>További lépések

További tudnivalók a helyi menü osztály és az ebben a cikkben használt módszerek:

> [!div class="nextstepaction"]
> [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Tekintse meg a további Kódminták:

> [!div class="nextstepaction"]
> [Znaková stránka-minta](https://aka.ms/AzureMapsSamples)
