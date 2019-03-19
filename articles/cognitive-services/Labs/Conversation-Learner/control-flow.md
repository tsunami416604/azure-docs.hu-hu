---
title: Beszélgetés Learner átvitelvezérlés – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg a Beszélgetéstanuló vezérlési folyamatában.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e0a0a88e249c0a032e5afaeea14b9b3cfcbdc319
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080660"
---
## <a name="control-flow"></a>Átvitelvezérlés

Ez a dokumentum ismerteti a vezérlési folyamatában, a beszélgetés Learner (CL) szerint jelenik meg az alábbi ábrán látható.

![](media/controlflow.PNG)

1. Felhasználó sikeresen megadja egy kifejezéssel a robot a például, "Mi az az időjárás, a Seattle?"
1. CL átadja egy gépi tanulási modellt, amely kinyeri az entitásokat, a felhasználói bevitel
   - Ez a modell által Beszélgetéstanuló hozhat létre, és www.luis.ai által üzemeltetett
1. Minden kinyert entitásokat, és a felhasználói bevitel szöveg-, a robot kódban entitás észlelési visszahívási metódus lesznek átadva.
    - Ez a kód előfordulhat, hogy a set/törlés/módosítására entitásértékek
1. Neurális hálózat CL elvégzi az entitások kinyeréséhez, és a felhasználói bevitel, és a pontszámok a robot definiált összes művelet kimenete
   - Ebben a példában a legnagyobb valószínűség műveletet, hogy az időjárás-előrejelzés küldése:

     ![](media/controlflow_forecast.PNG)

1. A kiválasztott művelet ebben az esetben szükséges egy API-hívások időjárás-előrejelzés beolvasása. 
1. Ez az API, amely a CL volt regisztrálva. AddCallback módot, majd meghívni.  Az API-t, az eredmény ezután visszaérkezik a felhasználó, egy üzenet – például "Sunny 67-es, a magas."
1. A majd kezdeményezték a Neurális hálózat alapján az előző lépésben a következő művelet meghatározásához.
1. A Neurális hálózat majd előrejelzi a lehetséges műveletek következő készletét, és a kiválasztott művelet van a felhasználó számára megjelenő, ebben az esetben "Bármi más?"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hogyan, akik a Beszélgetéstanuló](./how-to-teach-cl.md)
