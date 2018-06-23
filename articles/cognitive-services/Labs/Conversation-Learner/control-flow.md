---
title: Beszélgetés tanuló folyamatábrán - Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg a beszélgetési tanuló folyamatábrán.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 592ca82db7e0ab3ff89d25b61f38f8b226f3bc86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348574"
---
## <a name="control-flow"></a>Átvitelvezérlés

Ez a dokumentum ismerteti a folyamatábrán az a beszélgetés tanuló (CL) szerint jelenik meg az alábbi ábra.

![](media/controlflow.PNG)

1. Egy kifejezés és a botot például "Mi legyen a budapesti időjárási?" kifejezést adja meg felhasználói
1. CL átadja a felhasználó által megadott a gépi tanulási modell, amely kinyeri entitások számára
    - Ez a modell által beszélgetés tanuló leghatékonyabban, és www.luis.ai által üzemeltetett
2. A kibontott entitások, és a felhasználó bemeneti szöveget, a rendszer átadja a botot kódban entitás észlelési visszahívási metódus.
    - Ezt a kódot is entitást set/törlés/kezelheti értékek
1. Neurális hálózat CL elvégzi entitás kibontásával felhasználói bevitelt, és a botot definiált összes művelet eredményeinek kimeneti
    - Ebben a példában a legnagyobb valószínűség művelet, hogy adja meg a időjárás:

![](media/controlflow_forecast.PNG)

5. A kiválasztott művelet ebben az esetben szükséges egy API-hívás a időjárás beolvasása. 
6. Ez az API, amely a CL volt regisztrálva. AddAPICallback módszer, majd elindítása.  Ez az API eredményét ezután visszaérkezik a felhasználó, egy üzenet – például "A 67-es nagy Sunny."
7. A majd kezdeményezték a Neurális hálózat alapján az előző lépésben a következő művelet meghatározásához.
8. Neurális hálózat majd előrejelzi a következő lehetséges műveletek készletét, és a kiválasztott művelet nem jelenik meg a felhasználó ebben az esetben "Bármi más?"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hogyan mutatja meg a beszélgetési tanuló ](./how-to-teach-cl.md)
