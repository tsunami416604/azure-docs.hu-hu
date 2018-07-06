---
title: Az Azure Maps szolgáltatásban elérhető kérelem |} A Microsoft Docs
description: Az Azure Maps használata az elérhető alkalmazások készítése
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 537a8c80dc0d1fcb2f536d0e30200de19a2111a4
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867052"
---
# <a name="building-an-accessible-application"></a>Elérhető alkalmazások készítése

Ez a cikk bemutatja, hogyan hozhat létre egy térkép-alkalmazás, amely a képernyőolvasó használható.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Az elérhető alkalmazások' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll típusú <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>, hogy az elérhető alkalmazás</a> által az Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A térkép egyes kisegítő lehetőségeket nyújtó szolgáltatásai az előre összeállított. Egy felhasználó navigálhat a térkép a billentyűzet használata a, és ha egy képernyőolvasó fut, a térkép értesíti a felhasználó módosításait állapotában. Például a felhasználó fog értesítést küldünk a térkép új szélesség, hosszúság, nagyítás és helye a térkép eltolja vagy nagyítása. A kiinduló térképre figyelőszoftvert, amely további adatokat kell rendelkeznie a képernyőolvasót használók megfelelő szöveges adatokat. Használatával [előugró](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) egyik módja, ennek érdekében. Keresés a fenti példában egy előugró ablak, a szöveges adatokat hozzáadni a térkép a térképre figyelőszoftvert, amely minden PIN-kódot. Használatával a [előugró a](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) csatolása módszer lehetővé teszi, hogy a vizuális megjelenítése a térképen az előugró ablak nélkül láthatók a képernyőolvasó legyenek az előugró ablak.

## <a name="next-steps"></a>További lépések

További tudnivalók a helyi menü osztály és az ebben a cikkben használt módszerek:

* [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [csatolása](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [eltávolítása](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [Nyissa meg a](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zárja be](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Tekintse meg a [minta kódlap](http://aka.ms/AzureMapsSamples) további leképezés forgatókönyvek esetén.
