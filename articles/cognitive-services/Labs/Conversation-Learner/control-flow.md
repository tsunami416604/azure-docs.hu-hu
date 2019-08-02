---
title: Conversation Learner vezérlési folyamat – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Tudnivalók a Conversation Learner vezérlési folyamatáról.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 3ec839c1a930ffbe73989149360f1b02866a3c50
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705317"
---
## <a name="control-flow"></a>Átvitelvezérlés

Ez a dokumentum a Conversation Learner (CL) vezérlési folyamatát írja le az alábbi ábrán látható módon.

![](media/controlflow.PNG)

1. A felhasználó egy kifejezést vagy kifejezést ír be a robotba, például "mi az időjárás Seattle-ben?"
1. A CL átadja a felhasználói bemenetet egy gépi tanulási modellnek, amely kibontja az entitásokat
   - Ezt a modellt a Conversation Learner hozza létre, és a www.luis.ai üzemelteti
1. A robot kódjában a kinyert entitások és a felhasználó bemeneti szövege át lesz adva az entitás-észlelés visszahívási metódusának.
    - Ez a kód beállíthatja/törölheti vagy módosíthatja az entitások értékeit
1. A CL neurális hálózat ezután végrehajtja az entitás kinyerését és a felhasználói bevitelt, és a robotban definiált összes műveletet kiadja.
   - Ebben a példában a legnagyobb valószínűséggel az időjárás-előrejelzést kell megadnia:

     ![](media/controlflow_forecast.PNG)

1. A kiválasztott művelet, ebben az esetben egy API-hívást igényel az időjárás-előrejelzés lekéréséhez. 
1. Ez az API, amelyet a CL használatával regisztráltak. Ezt követően meghívja a AddCallback metódust.  Az API eredményét ezután a rendszer üzenetként adja vissza a felhasználónak, például: "Sunny, magas 67."
1. A rendszer ezután meghívja a neurális hálózatot, hogy meghatározza a következő műveletet az előző lépés alapján.
1. A neurális hálózat ezután előre megjósolja a lehetséges műveletek következő készletét, és a kiválasztott művelet a felhasználó számára jelenik meg, ebben az esetben "bármi más?"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Conversation Learner tanítása](./how-to-teach-cl.md)
