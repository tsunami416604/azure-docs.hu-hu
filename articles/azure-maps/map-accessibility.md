---
title: Az Azure-leképezések elérhető kérelem |} Microsoft Docs
description: Hogyan hozható létre egy Azure Maps használatával elérhető alkalmazás
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
ms.openlocfilehash: 2523287669628b8c58028cae9887743c20b6bc5e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655085"
---
# <a name="building-an-accessible-application"></a>Az elérhető alkalmazás felépítése

Ez a cikk bemutatja, hogyan hozhat létre egy térkép alkalmazás, amely a képernyőolvasók használhatja.

## <a name="understand-the-code"></a>A kód értelmezése

<iframe height='500' scrolling='no' title='Elérhető alkalmazás' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Tekintse meg a toll <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>elérhető kérelmet</a> Azure leképezésekhez (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) a <a href='https://codepen.io'>CodePen</a>.
</iframe>

A térkép néhány kisegítő lehetőségek az előre elkészített. A felhasználó a billentyűzet használata a térkép navigálhat, és a képernyőolvasók fut, ha a térkép értesíti a felhasználó állapotában módosításait. Például a felhasználó értesítést kap a térkép új szélesség, hosszúság, nagyítás és Helység szerint ha a leképezés eltolja vagy nagyítása. Azokat az adatokat, amelyek a alap térképen kell képernyő olvasó számára megfelelő szöveges adatokat. Használatával [előugró](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) egyik módja, ennek érdekében. A fenti keresés példa egy előugró ablak, a szöveges adatokat hozzáadni a térkép összes PIN-kódot, amely a térképen van. Használatával a [előugró ablak meg](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest) csatolása módszer lehetővé teszi a képernyőolvasók vizuálisan a térképen az a helyi menü megjelenítése nélkül is lássák az előugró.

## <a name="next-steps"></a>További lépések

További tudnivalók a helyi menü osztály és a jelen cikkben használt módszerek:

* [Helyi menü](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest)
    * [Csatolása](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#attach)
    * [Távolítsa el](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#remove)
    * [Nyissa meg](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#open)
    * [Zárja be](https://docs.microsoft.com/javascript/api/azure-maps-javascript/popup?view=azure-iot-typescript-latest#close)

Tekintse meg a [minta kódlap](http://aka.ms/AzureMapsSamples) több leképezés forgatókönyvek esetén.
