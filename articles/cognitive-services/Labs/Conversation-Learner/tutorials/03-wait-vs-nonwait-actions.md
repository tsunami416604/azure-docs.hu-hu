---
title: A várakozási és a nem várt műveletek használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja a várakozási és a nem várt műveleteket egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5d9a67f0b413b624baec369a219547cf18e9dbd2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705608"
---
# <a name="wait-and-non-wait-actions"></a>Várakozás és nem várt műveletek

Ez az oktatóanyag a várakozási műveletek és a Conversation Learner nem várt műveletei közötti különbséget mutatja.

## <a name="video"></a>Videó

[![WAIT vs nem-WAIT oktatóanyag – előzetes verzió](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek

- Várakozási művelet: Miután a rendszer "WAIT" műveletet végez, a művelet leállítja a műveleteket, és megvárja a felhasználói adatbevitelt.
- Nem várt művelet: Miután a rendszer "nem várt" műveletet végez, azonnal kiválaszthat egy másik műveletet (anélkül, hogy a felhasználói adatbevitelre kellene várnia).

## <a name="steps"></a>Lépések

### <a name="create-a-new-model"></a>Új modell létrehozása

1. A webes felhasználói felületen kattintson az új modell elemre.
2. A "név" mezőbe írja be a "WAIT not-WAIT" kifejezést, majd nyomja meg az ENTER billentyűt, vagy kattintson a "létrehozás" gombra.

### <a name="create-the-first-two-wait-actions"></a>Az első két várakozási művelet létrehozása

1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
2. A "bot válaszában..." mezőjébe írja be a "mi pizzát szeretne?" kifejezést.
    - Ez egy várakozási művelet, ezért hagyja bejelölve a "Várakozás a válaszhoz" jelölőnégyzetet.
3. Kattintson a "létrehozás" gombra.
4. Ismételje meg ezeket a lépéseket, hozzon létre egy másik műveletet a "pizza az úton!" a bot válasza.

### <a name="train-using-those-wait-actions"></a>Betanítás a várakozási műveletek használatával

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. A csevegési panelen írja be a következőt: "az üzenet beírása...", írja be a "Hi" kifejezést. 
    - Ez szimulálja a beszélgetés felhasználójának oldalát.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza ki a választ, "mit szeretne a pizza?" kifejezésre.
5. A felhasználó a "Margherita" kifejezéssel válaszol.
6. Kattintson a "pontszám műveletek" gombra.
7. Válassza ki a választ, "pizza az úton!".
8. Kattintson a Save (Mentés) gombra.

### <a name="create-a-non-wait-action-while-training"></a>Nem várakozási művelet létrehozása a betanítás során
Bár létrehozhatja a nem várt műveletet, mint korábban, létrehozhat egy betanítási munkamenetből is.
1. Kattintson az "új vonat párbeszédpanel" gombra.
2. A felhasználó típusa: "Hello".
3. Kattintson a "pontszám műveletek" gombra.
4. Kattintson a "+ művelet" gombra. 
    - Ekkor megnyílik az ismerős "művelet létrehozása" párbeszédpanel.
5. Írja be a robot válaszát a következőre: "Welcome to pizza bot!"
6. Törölje a jelet a "Várakozás a válaszhoz" jelölőnégyzetből.
7. Kattintson a "létrehozás" gombra.
    - Figyelje meg, hogy a bot azonnal válaszol a következővel: "Welcome to pizza bot!" és a rendszer ismét rákérdez egy másik bot-válaszra. Ennek az az oka, hogy a bot válasza volt az imént létrehozott, nem várt művelet.
9. Válassza ki a választ, "mit szeretne a pizza?" kifejezésre.
10. A felhasználó a "Margherita" kifejezéssel válaszol.
11. Kattintson a "pontszám műveletek" gombra.
12. Válassza ki a választ, "pizza az úton!".
13. Kattintson a Save (Mentés) gombra.

> [!NOTE]
> A bot-válaszok sorozata a várakozási és a várakozáson kívüli műveletekre vonatkozóan.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az entitások bemutatása](./04-introduction-to-entities.md)
