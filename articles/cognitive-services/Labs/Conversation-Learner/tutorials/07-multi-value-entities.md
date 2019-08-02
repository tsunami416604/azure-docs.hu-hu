---
title: Többértékű entitások használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja a többértékű entitásokat egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7911dd561da0f1e7b62b1b457ae5b059d5d54767
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704088"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Többértékű entitások használata Conversation Learner modellel
Ez az oktatóanyag az entitások multi-Value tulajdonságát mutatja be.

## <a name="video"></a>Videó

[![A többértékű entitások oktatóanyagának előzetes verziója](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek
A többértékű entitások egy listában lévő értékeket gyűjtenek, és nem tárolnak egyetlen értéket.  Ezek az entitások akkor hasznosak, ha a felhasználók egynél több értéket adhatnak meg. Egy pizzán található fehéráru például.

A többszörös értékként megjelölt entitások mindegyike a robot memóriájában lévő listához hozzáfűzi az entitás minden felismert példányát. A következő felismerés az entitás értékéhez fűzi a felülírás helyett.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapot a webes KEZELŐFELÜLETen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza az **új modell**lehetőséget.
2. Adja meg a **MultiValueEntities** **nevet**.
3. Kattintson a **Létrehozás** gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. Válassza az entitások lehetőséget a bal oldali panelen, majd az **új entitás**elemet.
2. Válassza az **Egyéni** betanítva az **entitás típusa**lehetőséget.
3. Adja meg az **entitás nevének**feltöltését.
4. A **többértékű érték** megadásával engedélyezheti, hogy az entitás egy vagy több értéket gyűjtsön.
5. Ellenőrizhető **.**
6. Kattintson a **Létrehozás** gombra.

![](../media/T07_entity_create.png)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Itt adhatja meg **a feltéteket: $toppings** a **bot válaszára..** . A vezető dollár aláírása egy entitásra mutató hivatkozást jelez.
3. Kattintson a **Létrehozás** gombra.

![](../media/T07_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Adja meg **, hogy milyen** előtéteket szeretne? a **bot válasza...**
3. Adja meg a nemmegfelelő jogosultságokat a **feltöltésekhez** .
4. Kattintson a **Létrehozás** gombra.

Most már két művelettel rendelkezik.

![](../media/T07_action_create_2.png)

### <a name="train-the-model"></a>A modell betanítása

1. Válassza a betanítási párbeszédablakok lehetőséget a bal oldali panelen, majd az **új vonat párbeszédablakban**.
2. A bal oldali csevegés panelen adja meg a felhasználó teljes kiírásának a **Hi** értéket.
3. Válassza a **pontszám műveletek**lehetőséget.
4. Válassza ki **, hogy milyen** feltéteket szeretne? a műveletek listából. A százalékos érték 100%, mint az egyetlen érvényes művelet a megkötések alapján.
5. Adja meg a **sajtot és** a gombákat a felhasználó teljes kiírásához a bal oldali csevegési panelen.
6. Jelölje ki a sajtot, majd válassza a **+ feltöltések**lehetőséget.
7. Jelölje ki a gombákat, majd válassza a **+ feltöltések**lehetőséget.
8. Válassza a **pontszám műveletek**lehetőséget.
9. Válassza ki az **itt található fehéráru: $toppings** a műveletek listából.
10. A bal oldali csevegés panelen adja meg a felhasználó következő kiírásához a **Pepper hozzáadása** elemet.
11. Jelölje ki a **paprika** elemet, majd válassza a **+ feltöltések**lehetőséget.
12. Válassza a **pontszám műveletek**lehetőséget.
13. Válassza ki az **itt található fehéráru: $toppings** a műveletek listából.
14. A bal oldali csevegési panelen írja be a **sajt eltávolítása** a felhasználó harmadik kiírására.
15. Jelölje ki a sajtot, majd válassza a **-Öntetek**lehetőséget.
16. Válassza a **pontszám műveletek**lehetőséget.
17. Válassza ki az **itt található fehéráru: $toppings** a műveletek listából.

![](../media/T07_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Előre betanított entitások](./08-pre-trained-entities.md)
