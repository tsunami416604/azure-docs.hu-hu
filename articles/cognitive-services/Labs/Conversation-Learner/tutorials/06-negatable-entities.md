---
title: Megtagadási entitások használata Conversation Learner modellel – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatók a megtagadható entitások egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4edbfc396d4ab32b991b084a7b738f04b5205418
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704107"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Megtagadási entitások használata Conversation Learner modellel

Ez az oktatóanyag az entitások "hatástalanítható" tulajdonságát mutatja be.

## <a name="video"></a>Videó

[![Megtagadható entitások – oktatóanyag – előzetes verzió](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="details"></a>Részletek
Az entitások "elutasítható" tulajdonsága lehetővé teszi az entitás normál (pozitív) és negatív példányainak címkézését, a pozitív és negatív modellek alapján történő tanítást, valamint a meglévő entitások értékének törlését. A "megtagadható" tulajdonsággal rendelkező entitásokat a beszélgetési Karcsúbbban megtagadható entitásoknak nevezzük.

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapot a webes KEZELŐFELÜLETen.

### <a name="create-the-model"></a>A modell létrehozása

1. Válassza az **új modell**lehetőséget.
2. Adja meg a **NegatableEntity** **nevet**.
3. Kattintson a **Létrehozás** gombra.

### <a name="entity-creation"></a>Entitás létrehozása

1. Válassza az entitások lehetőséget a bal oldali panelen, majd az **új entitás**elemet.
2. Válassza az **Egyéni** betanítva az **entitás típusa**lehetőséget.
3. Adja meg az **entitás nevének** **nevét** .
4. A megtagadással ellenőrizhető, hogy a felhasználók megadhatnak-e egy entitást, vagy azt, hogy nem az entitás értéke, így *nem* törli a megfelelő entitás értékét.
5. Kattintson a **Létrehozás** gombra.

![](../media/T06_entity_create.png)

### <a name="create-the-first-action"></a>Az első művelet létrehozása

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Adja meg **a nem tudom a nevét.** a **bot válasza...**
3. Adja meg a **jogosultságok kizárásának** **nevét** .
4. Kattintson a **Létrehozás** gombra.

![](../media/T06_action_create_1.png)

### <a name="create-the-second-action"></a>A második művelet létrehozása

1. Válassza a **műveletek** lehetőséget a bal oldali panelen, majd az **új művelet**elemet.
2. Adja **meg a nevét. $Name.** a **bot válasza...**
3. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> A **név** entitást a rendszer automatikusan hozzáadja a **szükséges entitásként** a válasz kiírásakor.

Most már két művelettel rendelkezik.

![](../media/T06_action_create_2.png)

### <a name="train-the-model"></a>A modell betanítása

1. Válassza a betanítási párbeszédablakok lehetőséget a bal oldali panelen, majd az **új vonat párbeszédablakban**.
2. A bal oldali csevegés paneljén adja meg a felhasználó teljes kiírásának a **Hello** értéket.
3. Válassza a **pontszám műveletek**lehetőséget.
4. Válassza **a nem tudom a nevét.** a műveletek listából. A százalékos érték 100%, mint az egyetlen érvényes művelet a megkötések alapján.
5. A bal oldali csevegési panelen adja meg a felhasználó **nevének őszinte nevét** .
6. Válassza a **Frank** , majd a **+ név**elemet. A megtagadható entitások két példányban vannak: (+), valamint az értéket adja hozzá vagy felülírja; (-) mínusz eltávolítja az értéket.
7. Válassza a **pontszám műveletek**lehetőséget. A **Name (név** ) entitás a modell memóriájában már frankként van definiálva **, így a neve. $Name** művelet érhető el.
8. Válassza **a ismerem a nevét. $Name.** a műveletek listából.
9. Adja meg **a saját név nem Frank értéket.** a felhasználó a bal oldali csevegés panelen.
10. A **Frank** kiemeléséhez válassza a **-Name** (név) elemet a **Name (név** ) entitás értékének törléséhez.
11. Válassza a **pontszám műveletek**lehetőséget.
12. Válassza **a nem tudom a nevét.** a műveletek listából.
13. Adja meg **a nevem Susan nevet.** a felhasználó harmadik kihagyása a bal oldali csevegési panelen.
14. **Susan** kiemelése és **neve** 

![](../media/T06_training.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többértékű entitások](./07-multi-value-entities.md)
