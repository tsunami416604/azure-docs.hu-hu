---
title: Hibakeresési hibák egyéni parancsok alkalmazásának létrehozásakor (előzetes verzió)
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan lehet hibakeresést végezni az egyéni parancsok alkalmazásának létrehozásakor.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b0a26b4d3f0f59f8e05c5990bbc64ee55f12f124
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307742"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Hibakeresési hibák egyéni parancsok alkalmazásának létrehozásakor

Ez a cikk azt ismerteti, hogyan lehet hibakeresést végezni, amikor hibákat lát el az egyéni parancsok alkalmazásának létrehozásakor. 

## <a name="errors-when-creating-an-application"></a>Hibák az alkalmazások létrehozásakor
Az egyéni parancsok szintén létrehoznak egy alkalmazást a [luisban](https://www.luis.ai/) egyéni parancsok alkalmazásának létrehozásakor. 

[Luis korlátozza a 500 alkalmazást egy szerzői erőforráson](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits). A LUIS-alkalmazás létrehozása sikertelen lehet, ha olyan authoring-erőforrást használ, amely már rendelkezik 500-alkalmazásokkal. 

Győződjön meg arról, hogy a kiválasztott LUIS authoring-erőforrás kevesebb, mint 500. Ha nem, akkor új LUIS authoring Resource, váltson át egy másikra, vagy próbálja meg törölni a LUIS-alkalmazásait.  

## <a name="errors-when-deleting-an-application"></a>Hibák az alkalmazások törlésekor
### <a name="cant-delete-luis-application"></a>A LUIS-alkalmazás nem törölhető
Egyéni parancsok alkalmazásának törlésekor az egyéni parancsok megpróbálják törölni az egyéni parancsok alkalmazáshoz társított LUIS-alkalmazást is.

Ha a LUIS-alkalmazás törlése sikertelen volt, lépjen a [Luis](https://www.luis.ai/) -fiókjába, és törölje azokat manuálisan.

### <a name="toomanyrequests"></a>TooManyRequests
Ha egyszerre nagy mennyiségű alkalmazást próbál meg törölni, valószínű, hogy a "TooManyRequests" hibaüzeneteket fogja látni. Ez azt jelenti, hogy a törlési kérelmeket az Azure szabályozza. 

Frissítse az oldalt, és próbálkozzon kevesebb alkalmazás törlésével.

## <a name="errors-when-modifying-an-application"></a>Hiba történt egy alkalmazás módosításakor

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Nem lehet törölni egy paramétert vagy egy webes végpontot
A paramétert nem lehet törölni, ha használatban van. Távolítsa el a paraméter hivatkozásait bármilyen beszédes válaszban, példa mondatokat, feltételeket, műveleteket, majd próbálkozzon újra.

### <a name="cant-delete-a-web-endpoint"></a>Nem lehet törölni a webes végpontot
Használat közben nem törölhet webes végpontot. A webes végpont eltávolítása előtt távolítsa el a webes végpontot használó **hívási webes végponti** műveletet.

## <a name="errors-when-training-an-application"></a>Hibák az alkalmazás betanításakor
### <a name="build-in-intents"></a>Létrehozás szándék szerint
LUIS nem tartalmaz igen/nem szándékot. A csak az "igen", a "No" kifejezéssel rendelkező minta mondatok betanítása sikertelen lesz. 

| Kulcsszó | Változatok | 
| ------- | --------- | 
| Yes | Persze, OK |
| No | Nem, nem | 

### <a name="common-sample-sentences"></a>Gyakori minta mondatok
Az egyéni parancsok nem teszik lehetővé a különböző parancsok közötti közös példákban szereplő mondatok használatát. Egy alkalmazás betanítása meghiúsulhat, ha az egyik parancsban szereplő egyes mondatok már egy másik parancsban vannak definiálva. 

Ügyeljen arra, hogy a különböző parancsok között ne legyenek közös példák. 

Az ajánlott eljárás a minta mondatok különböző parancsokban való kiegyensúlyozására: a [Luis ajánlott eljárása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

### <a name="empty-sample-sentences"></a>Üres minta mondatok
Minden parancshoz legalább 1 minta mondatnak kell lennie.

### <a name="undefined-parameter-in-sample-sentences"></a>Nem definiált paraméter a minta mondatokban
Egy vagy több paraméter a minta mondatokban van használatban, de nincs definiálva.

### <a name="training-takes-too-long"></a>A képzés túl hosszú időt vesz igénybe
A LUIS Training célja, hogy gyorsan tudjon tanulni kevesebb példát. Ne adjon hozzá túl sok példa mondatot. 

Ha több példája is hasonló, adjon meg egy paramétert, és vegye fel őket egy mintázatba, és adja hozzá például a mondatokhoz.

Meghatározhatja például a (z) {Vehicle} paramétert az alább látható példához, és csak a "könyv a {Vehicle}" kifejezést adja hozzá a mondatokhoz.

| Példa mondatokra | Mintázat | 
| ------- | ------- | 
| Autó befoglalása | A könyv {Vehicle} | 
| Repülőjáratok lefoglalása | A könyv {Vehicle} |
| Taxi foglalása | A könyv {Vehicle} |

A LUIS-képzés bevált gyakorlata a [Luis ajánlott eljárása](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

## <a name="cant-update-luis-key"></a>Nem sikerült frissíteni a LUIS-kulcsot
### <a name="reassign-to-e0-authoring-resource"></a>Ismételt hozzárendelés E0-létrehozási erőforráshoz
A LUIS nem támogatja a LUIS-alkalmazás újbóli hozzárendelését a E0-készítő erőforráshoz.

Ha módosítania kell a szerzői erőforrást a F0-ből a E0-be, vagy másik E0-erőforrásra kell váltania, akkor hozza létre újra az alkalmazást.

### <a name="save-button-is-disabled"></a>A Mentés gomb le van tiltva
Ha soha nem rendel hozzá LUIS-előrejelzési erőforrást az alkalmazáshoz, a Mentés gomb le lesz tiltva, ha a szerzői erőforrást előrejelzési erőforrás hozzáadása nélkül próbálja meg módosítani.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Minták megtekintése a GitHubon](https://aka.ms/speech/cc-samples)
