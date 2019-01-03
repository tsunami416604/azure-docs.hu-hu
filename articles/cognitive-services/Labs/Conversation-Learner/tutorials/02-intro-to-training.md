---
title: Egy Beszélgetéstanuló modell – a Microsoft Cognitive Services bemutatása |} A Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan többek között az elágaztatási és -szerkesztő keresztül Beszélgetéstanuló előző képzési modell betanításához.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e7405583a04699ce0481c95b22912e717f76743a
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796798"
---
# <a name="introduction-to-training"></a>Képzések bemutatása

Ebben az oktatóanyagban egy modell tanítása esetén egy előző képzés és a szerkesztési Bot választ annak érdekében, hogy módosítsa alapján új képzési ki elágaztatás kapcsolatos alapfogalmakat ismerteti.

## <a name="video"></a>Videó

[![Bevezetés az oktatóanyag előzetes képzés](https://aka.ms/cl_Tutorial_v3_IntroTraining_Preview)](https://aka.ms/cl_Tutorial_v3_IntroTraining)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz, hogy fut-e az általános oktatóanyag robotot

    npm run tutorial-general

## <a name="details"></a>Részletek

- Műveletek: Felhasználói bevitel Bot választ.
- Train: A módszer azt tanít robotprogramok válaszolni a felhasználói bevitel.
- Elágaztatási: Egy mentett Train párbeszédpanel, amely egy új Train párbeszédpanel létrehozása céljából belüli felhasználói bevitel módosítása ugyanaz, mint az eredeti elindításakor, de a beszélgetés tart egy másik irányban.

## <a name="steps"></a>Lépések

### <a name="create-a-new-model"></a>Új modell létrehozása

1. A webes felhasználói felületén kattintson az új modell
2. Írja be a "Name", "Inspire Bot". Ezután kattintson a Létrehozás gombra.

### <a name="create-an-action"></a>Hozzon létre egy műveletet

1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
2. A "Bot a válaszban" mezőbe írja be a "Hi! Szeretne még ma ötletek? ".
    - Minden más mezők és jelölőnégyzetet hagyja meg az alapértelmezett beállítás.
3. Kattintson a Létrehozás gombra.

### <a name="first-training-and-creating-another-action-while-training"></a>Először képzési és a egy másik művelet közben képzési létrehozása

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. A Csevegés panelen, ahol allocated "Írja be az üzenetet...", "hello" típusra. 
    - Ez szimulálja a beszélgetést a felhasználó oldalán.
3. Kattintson a "Score műveletek".
4. Válassza ki a választ, a "Hi! Szeretne még ma ötletek? ".
5. A felhasználó válaszol, az "Igen".
6. Kattintson a "Score műveletek".
7. Kattintson a "+ művelet" gombra. 
    - Ekkor a jól ismert (an Action létrehozása) párbeszédpanelt.
8. Írja be a robot válaszként, a "Készen nagyszerű!"
9. Kattintson a Létrehozás gombra.
10. Figyelje meg, hogy a robot azonnal válaszol-e.
11. A "Mentés" gombra.

### <a name="branch-a-second-training-off-of-the-first-training"></a>Az első képzési minden második képzési ág
1. Kattintson a rács sort, amely összefoglalja az első képzést. 
    - Ez lehetővé teszi, hogy megtekintheti és szerkesztheti a meglévő betanítási.
2. Kattintson az "Igen" felhasználói válaszra. 
    - Ez lesz teszik elérhetővé a szerkesztési vezérlők.
3. Kattintson az ág ikonra. 
    - Megjelenik egy új beszélgetés a különböző felhasználói bevitel kérése.
4. Írja be a "nem", nyomja le adja meg, vagy kattintson a "Létrehozás" gombra. 
    - Ekkor kap egy új példányát egy Train párbeszédpanel, változatlan marad az eredeti kapcsolatot.
5. Kattintson a "Score műveletek".
6. Kattintson a robot helytelen választ, amely meg is jelentek meg.
7. Kattintson a "+ művelet" gomb 
    - így is létrehozunk egy új műveletet is, a robot.
8. Írja be a robot válaszként, "semmi gond. Van egy nagyszerű nap!"
9. Kattintson a Létrehozás gombra
10. Figyelje meg, hogy a robot azonnal válaszol-e.
11. A "Mentés" gombra.

### <a name="test-the-trainings"></a>Tesztelje a Betanítások
1. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez", majd a "Log párbeszédpanel".
2. Írja be az üzenetet, a "hi". 
3. Figyelje meg, hogy a robot a módon, hogy betanított automatikusan válaszol.
4. Írja be a felhasználói válasz "yes".
5. Figyelje meg, hogy a robot válasz jelenik meg, hogy az első képzési működik.
6. A "Időtúllépés" gombra. Ezzel arra utasítja a Beszélgetéstanuló szeretnénk ismét, figyelmen kívül hagyja a természetes nyelvi bekapcsolja, amely csak a sor került.
7. Írja be az üzenetet, a "hi". 
8. Figyelje meg, hogy a robot a módon, hogy betanított automatikusan válaszol.
9. Írja be a felhasználói válasz "no".
10. Figyelje meg, hogy a robot válasz jelenik meg, hogy a második képzési működik.
11. Kattintson a "Végzett tesztelése" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Várjon, és nem várakozási művelet](./03-wait-vs-nonwait-actions.md)
