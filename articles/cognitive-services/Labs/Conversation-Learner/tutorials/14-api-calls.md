---
title: API használatával meghívja a Beszélgetéstanuló modellel – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használható az API-hívások Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: aba3c2eb925370704ea52364891502a7a09cc9ec
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212517"
---
# <a name="how-to-add-api-calls-to-a-conversation-learner-model"></a>API-hívások Beszélgetéstanuló modell hozzáadása

Ez az oktatóanyag bemutatja, hogyan adhat hozzá a modell API-hívások. API-hívások olyan funkció, amely határozza meg, és írja be a robot, és amely Beszélgetéstanuló hívhatja.

## <a name="video"></a>Videó

[![API-hívások oktatóanyag előzetes](https://aka.ms/cl_Tutorial_v3_APICalls_Preview)](https://aka.ms/cl_Tutorial_v3_APICalls)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e a "tutorialAPICalls.ts" robot.

    npm run tutorial-api-calls

## <a name="details"></a>Részletek

- API-hívások megtekintheti és módosíthatja az entitásokat.
- API-hívások hozzáférése a memória-kezelő objektumot.
- API-hívások argumentumokat is igénybe vehet.

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen kattintson a "Oktatóanyagokban importálása", és válassza a "Az oktatóanyag-14 – APICalls" nevű modell.

### <a name="entities"></a>Entitások

Hogy meghatároztuk a modellben nevű több entitást `number`.

![](../media/tutorial12_entities.PNG)

### <a name="api-calls"></a>API-hívások
A kódot az API-hívásokhoz van definiálva a következő fájlt: `C:\<installedpath>\src\demos\tutorialAPICalls.ts`.

![](../media/tutorial12_apicalls.PNG)

- A `RandomGreeting` visszahívási adja vissza a meghatározott véletlenszerű üdvözlőszöveget a `greeting` tömb.
- A `Multiply` visszahívási fog szorozza meg a műveletet, amely meghívja az és renderelhető eredményt adja vissza a felhasználói felület által átadott két számot.
    - Figyelje meg, hogy memóriakezelő az első argumentum. 
    - Figyelje meg, hogy API-visszahívások ebben az esetben eltarthat több bemenet `num1string` és `num2string`.
- A `ClearEntities` visszahívási törli a számú entitást, hogy a felhasználó megadhat egy másik számot. 
    - Azt mutatja be, hogyan API-hívások entitásokat is módosíthatja.

### <a name="actions"></a>Műveletek
Négy művelet hoztunk létre. Három a "Nem-Wait" API-műveletek, a negyedik van egy "Szöveg" műveletet, amely a felhasználó mit úgy találtuk, a többi hasonló kérdést tesz fel. Minden egyes létrehozásának módja megtekintéséhez tegye a következőket:
1. A bal oldali panelen kattintson a "Műveletek", majd kattintson az egyik a rácsban szereplő négy műveleteket.
2. Figyelje meg, hogy az űrlap felugró mezők értékeit.
3. Figyelje meg a `Refresh` gombra az API-t a mező mellett.
    - Ha azt is, állítsa le a robot, és módosítsa az API-k, a felhasználói felület lapon pedig ezt követően kattinthat a `Refresh` gombra a legújabb módosításainak életbe léptetéséhez.

![](../media/tutorial12_actions.PNG)

#### <a name="clearentities-multiply-and-randomgreeting"></a>Szorzás ClearEntities, és RandomGreeting
Ezek a műveletek három olyan API-típus. Ezek mindegyike támaszkodik a munkához, és valószínűleg vissza az értéket a felhasználó számára megjelenő API visszahívási függvényekben.

#### <a name="what-number-do-you-want-to-multiply-by-12"></a>"Milyen számon szeretné szorzása a következővel 12"
Ez a "Text" művelet, és egyszerűen a felhasználó egy kérdést tesz fel. Ez a művelet ténylegesen nem működik együtt az API-visszahívások egyikét, míg azt kéri a felhasználót, egy szám, amely lép a memória olyan entitás, amely ezután felhasználhatók a "Szorzása" művelet, amely használja az API-visszahívások egyikét, válaszoljon.


### <a name="train-dialog"></a>Train párbeszédpanel

Vegyük végig egy "képzési párbeszédpanel".

1. A bal oldali panelen, kattintson a `Train Dialogs`, akkor a `New Train Dialog` gombra.
2. Írja be a "hello".
3. Kattintson a `Score Actions` gombra.
4. Válassza a(z) `RandomGreeting` lehetőséget. 
    - Ez hajtja végre a véletlenszerű üdvözlőszöveget API-hívás.
    - Ez nem a felhasználói válaszra vár.
5. A következők szerint válasszon: `What number to do you want to multiply by 12?`
6. Írjon be egy számot, tetszőleges számú és csak egy számot.
    - Figyelje meg, hogy a szám automatikusan kiosztott címkéje a `number` entitás.
7. Kattintson a `Score Actions` gombra.
8. Válassza ki a `Multiply` művelet.
    - Figyelje meg, hogy a szorzás az eredmény szerint 12.
    - Figyelje meg, hogy a memória továbbra is tartalmaz a megadott érték `number`
9. Válassza ki a `ClearEntities` művelet.
    - Figyelje meg, hogy az entitás értékét `number` törölve lett a memóriából.
10. Kattintson a `Save` gombra.

Most láthatta, API-visszahívások regisztrálása gyakori mintáit, és hogyan argumentumok megadása, és rendelje hozzá az értékeket, és azokat az entitásokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kártyák: 1. rész](./15-cards.md)
