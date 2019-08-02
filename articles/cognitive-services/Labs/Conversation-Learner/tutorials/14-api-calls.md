---
title: API-hívások használata Conversation Learner MODELREL – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatók az API-hívások Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 261536932cc82a28ad4ee3ffc3575ea41fe9ec5b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703922"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>API-hívások hozzáadása Conversation Learner modellhez

Ez az oktatóanyag bemutatja, hogyan adhat hozzá API-hívásokat a modellhez. Az API-hívások a robotban definiált és írt függvények, amelyek Conversation Learner hívhatók.

## <a name="video"></a>Videó

[![API-hívások – oktatóanyag – előzetes verzió](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag megköveteli, hogy a "tutorialAPICalls. TS" robot fusson.

    npm run tutorial-api-calls

## <a name="details"></a>Részletek

- Az API-hívások elolvashatják és módosíthatják az entitásokat.
- Az API-hívások hozzáférnek a Memory Manager-objektumhoz.
- Az API-hívások argumentumokat is igénybe vehetnek.

### <a name="open-the-demo"></a>A bemutató megnyitása

A webes felhasználói felületen kattintson az "oktatóanyagok importálása" elemre, és válassza ki a "tutorial-14-APICalls" nevű modellt.

### <a name="entities"></a>Entitások

A modellben `number`egy entitást definiáltak.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-hívások
Az API-hívások kódja a következő fájlban van definiálva: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- A `RandomGreeting` visszahívás a `greeting` tömbben definiált véletlenszerű üdvözlést adja vissza.
- A `Multiply` visszahívás két, a meghívó művelet által átadott számot fog szorozni, és visszaadja a felhasználói felületen megjeleníthető eredményt.
    - Figyelje meg, hogy a Memory Manager az első argumentum. 
    - Figyelje meg, hogy az API-visszahívás több bemenetet is igénybe `num2string`vehet, ebben az esetben `num1string` és.
- A `ClearEntities` visszahívás törli a szám entitást, így a felhasználó megadhat egy másik számot. 
    - Szemlélteti, hogy az API-hívások hogyan kezelhetik az entitásokat.

### <a name="actions"></a>Műveletek
Négy műveletet hoztunk létre. Hárman a "nem várt" API-műveletek, a negyedik pedig egy "text" művelet, amely a felhasználót a többi oktatóanyagban megjelenő kérdéshez hasonló módon kéri le. Az egyes létrehozási módjának megtekintéséhez tegye a következőket:
1. A bal oldali panelen kattintson a "műveletek" elemre, majd a rácsban felsorolt négy művelet egyikére.
2. Figyelje meg az űrlap egyes mezőinek értékeit.
3. Figyelje meg `Refresh` az API mező melletti gombot.
    - Ha leállítottuk a robotot, és az API-kat a felhasználói felület oldalának bekapcsolása közben módosítjuk, `Refresh` akkor a gombra kattintva megtekintheti a legújabb módosításokat.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>ClearEntities, szorzás és RandomGreeting
Mindhárom művelet API-típusú. Ezek mindegyike az API callback függvényeit használja a feladatok végrehajtásához, és lehetséges, hogy egy értéket ad vissza a felhasználónak.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Mit szeretne szorozni a 12 értékkel"
Ez a "text" művelet, és egyszerűen csak a felhasználó kérdését kérdezi le. Habár ez a művelet valójában nem működik együtt az egyik API-visszahívással sem, az arra kéri a felhasználót, hogy válaszoljon egy olyan számra, amely egy olyan entitás memóriájában fog működni, amelyet aztán a "szorzás" művelet használ, amely az egyik API-visszahívást használja.


### <a name="train-dialog"></a>Betanítás párbeszédpanel

Lássunk egy "betanítási párbeszédablakot".

1. A bal oldali panelen kattintson `Train Dialogs`a elemre, majd a `New Train Dialog` gombra.
2. Írja be a "Hello" kifejezést.
3. Kattintson a `Score Actions` gombra.
4. Válassza a(z) `RandomGreeting` lehetőséget. 
    - Ez végrehajtja a véletlenszerű üdvözlés API-hívást.
    - Ez nem fog várni a felhasználói válaszra.
5. A következők szerint válasszon: `What number to do you want to multiply by 12?`
6. Írjon be egy számot, egy számot és egy számot.
    - Figyelje meg, hogy a szám automatikusan `number` entitásként van megjelölve.
7. Kattintson a `Score Actions` gombra.
8. Válassza ki `Multiply` a műveletet.
    - Figyelje meg a szorzás eredményét 12 alapján.
    - Figyelje meg, hogy a memória továbbra is tartalmazza a megadott értéket`number`
9. Válassza ki `ClearEntities` a műveletet.
    - Figyelje meg `number` , hogy az entitás értéke törölve lett a memóriából.
10. Kattintson a `Save` gombra.

Megismerte, hogyan regisztrálhat API-visszahívásokat, az általános mintákat, és hogyan határozhat meg argumentumokat, és hogyan rendelhet hozzájuk értékeket és entitásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kártyák 1. rész](./15-cards.md)
