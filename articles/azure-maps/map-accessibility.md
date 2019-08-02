---
title: Elérhető alkalmazás készítése Azure Mapsokkal | Microsoft Docs
description: Elérhető alkalmazás létrehozása Azure Maps használatával
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638785"
---
# <a name="building-an-accessible-application"></a>Akadálymentes alkalmazás létrehozása

Ebből a cikkből megtudhatja, hogyan készíthet olyan Térkép-alkalmazást, amelyet képernyőolvasók használhatnak.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Elérhető alkalmazás létrehozása' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg, hogy a toll elérhetővé teszi<a href='https://codepen.io/azuremaps'>@azuremaps</a>-e a Azure Maps () <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>alkalmazást</a> a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A Térkép a kisegítő lehetőségekkel előre összeépítve érhető el. A felhasználók a billentyűzettel is megkereshetik a térképet. Ha egy képernyőolvasó fut, a Térkép értesíti a felhasználót, hogy az állapota megváltozik.
Például a felhasználók értesítést kapnak a Térkép változásairól, amikor a Térkép megpárol vagy kicsinyítve van. Az alaptérképre helyezett további információknak megfelelő szöveges információval kell rendelkezniük a képernyőolvasó felhasználói számára.

Az [előugró ablak](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) használata az egyik lehetőség ennek a megvalósítására. A fenti keresési példában egy szöveges adatokat tartalmazó felugró ablak jelenik meg a térképen a térképen elhelyezett összes PIN-kódhoz. Az [előugró ablak](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [csatolási](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) metódusának használatával az előugró ablak látható egy képernyőolvasóban anélkül, hogy az előugró ablak vizuálisan jelenjen meg a térképen.

## <a name="next-steps"></a>További lépések

További információ az előugró osztályról és a cikkben használt módszerekről:

> [!div class="nextstepaction"]
> [Lakosság](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

További példák a kód megadására:

> [!div class="nextstepaction"]
> [Kód mintájának lapja](https://aka.ms/AzureMapsSamples)
