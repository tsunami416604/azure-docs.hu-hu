---
title: Beszélgetéstanuló használata más technológiák – a Microsoft Cognitive Services létrehozása bot |} A Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan Beszélgetéstanuló használata más bot technológiák létrehozásához.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: 0e2a9918108012e0d0ef1defbff044d7ab9bd83d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796808"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Beszélgetéstanuló használata más bot technológiák létrehozásához

Ez az oktatóanyag bemutatja a Beszélgetéstanuló használata más bot technológiák létrehozásához, és hogyan memória (vagy állam) megoszthatók között ezek a technológiák. 

## <a name="video"></a>Videó

[![Hibrid Robotokat oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz log párbeszédpanelek, nem a napló párbeszédpanel webes felhasználói felület létrehozása a robot emulator használatával. A Bot Framework-emulátor beállításával kapcsolatos további információk érhetők el [Itt](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Ehhez az oktatóanyaghoz, hogy fut-e a hibrid oktatóanyag robot:

    npm run tutorial-hybrid

## <a name="details"></a>Részletek

Beszélgetéstanuló vezérlőelem pedig képest a beszélgetés Learner munkamenet összes állapota a Beszélgetéstanuló memóriakezelő kell tárolni. Erre azért szükség, mint a machine learning a állapot segítségével határozza meg, hogyan lehet a beszélgetés meghajtó. Külső állapotuk is kell megkérnie az Beszélgetéstanuló a OnSessionStartCallback, amelynek a neve, amikor a munkamenet megkezdése a. Belső állapot adhatók vissza ki a OnSessionEndCallback szerint amikor a munkamenet leáll.

Majdnem felfoghatók Beszélgetéstanuló, a függvény hívásához szükséges néhány kezdeti állapota, és értékeit adja vissza.

Az ebben a példában létre fog hozni egy hibrid bot használatával két különböző rendszereken:
1. A beszélgetés Learner modell <br/>
    Beszélgetés learner modell segítségével határozza meg a következő elvégzendő művelet a jelenlegi munkamenet alapján a robot. Ez a rész a robot vesz igénybe egy darab kezdeti állapot `isOpen` (amely azt jelzi, hogy egy nyitott vagy lezárt) és a egy másik részének állapotát adja vissza `purchaseItem` (vásárol egy elemet a felhasználó nevét).

2. Egyező szöveg <br />
    Egyszerűen megvizsgálja a bejövő szöveges adott karakterláncokat, és válaszol. Ez a rész a robot kezeli a robotok más tárolási mechanizmus és a felelős a CL-munkamenet indítása. Kifejezetten az általa felügyelt mindhárom változóval: `usingConversationLearner`, `storeIsOpen`, és `purchaseItem`.

Először a bemutató során használt modell forgalomnövekedésre.

### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen kattintson a "Oktatóanyagokban importálása", és válassza a "Oktatóanyag – 16-HybridBot" nevű modell.

## <a name="entities"></a>Entitások

Az entitások lap megnyitásához, és figyelje meg, hogy a két entitás: `isOpen` és `purchaseItem`

Szeretné megtudni, hogyan használja ezeket az entitásokat, nyissa meg a fájlt: `C:\<installedpath>\src\demos\tutorialHybrid.ts` és tekintse meg a visszahívások.

Figyelje meg, hogy a kód `OnSessionStartCallback` másolja az értéket a `storeIsOpen` értékeként BotBuilder beszélgetés storage-ból a `isOpen` entitás, hogy Beszélgetéstanuló számára elérhető legyen. Tekintse meg a következő kódot:

![](../media/tutorial17_sessionstart.PNG)

Hasonlóképpen, a kód `OnSessionEndCallback` (Ha a munkamenet egy megismert tevékenységet, és nem csupán egy időtúllépés miatt befejeződött) másolja az értéket, entitás `purchaseItem` BotBuilder Storage out `purchaseItem`. Tekintse meg a következő kódot:

![](../media/tutorial17_sessionend.PNG)

Most nézzük meg a műveletet.

## <a name="actions"></a>Műveletek

Figyelje meg a modellnek négy művelet.

Az importálni kívánt szabályok műveletek a következők:

- Ha a `isOpen` entitás van beállítva, a Bot kérni fogja a "Mit szeretne vásárolni?" és tárolására, hogy a `puchaseItem` tárolóhely.
- Ha `isOpen` nincs beállítva, a Bot tudatja Önnel "Vagyok Sajnáljuk, hogy már lezárt".
- A másik két műveletet típusúak legyenek `END_SESSION`.
- A END_SESSION művelet jelzi a ConversationLearner, hogy a beszélgetés befejeződött.

### <a name="overall-bot-logic"></a>Általános Bot logika

Először láthatja, hogy ha a robot állapot `usingConversationLearner` jelző van beállítva, a vezérlő Beszélgetéstanuló átadott. Ha nem, megoldott vezérlő adjuk át.  Ebben a példában azt megegyező egyszerű szöveg van jelenítve, de ez lehet bármely más Bot technológia LUIS, beleértve a QnA maker és Beszélgetéstanuló akár egy másik példánya.

Nyissa meg a felhasználó úgy kell, és zárja be az áruházba, így egy karakterlánc végzünk hasonlítsa össze az "open store" és "Bezárás store", és állítsa be a "storeIsOpen" jelző.

Következő lépésként oly módon való kezelési vezérlő keresztül a beszélgetés Learner modellre. Amikor azt megfelelnek a "üzemi" karakterlánc azt tegye a következőket:
- Állítsa be a `usingConversationLearner` jelzőt a robot a memóriában.
- A "StartSession" metódust hívja meg a beszélgetési Learner modell.  Ezzel elindítja a "onSessionStartCallback" amely inicializálja a `isOpen` entitás érték

Lásd az alábbi:

![](../media/tutorial17_useConversationLearner.PNG)

Azt is megteheti, szöveges egyezést "előzményei", amely beszerzési legutóbbi elem jelenik meg.
Végül ha bármi más van megjelenítjük a rendelkezésre álló felhasználói parancsokat

## <a name="train-dialog"></a>Train párbeszédpanel

Ebben az oktatóanyagban a modell már előre betanított.  Teszteljük, hogy a teljes robot számára hatásának a kezdő és záró munkamenet visszahívások a gyakorlatban.

## <a name="testing-the-bot"></a>A robot tesztelése

Egyetlen beszélgetés Leaner modell robotokat eltérően azonosra, csak látható, hogy mi történik, a beszélgetés Learner modell teszteléséhez Ez a beszélgetés Learner felhasználói felületén.

### <a name="install-the-bot-framework-emulator"></a>Telepítse a Bot framework-emulátor

- Nyissa meg a következőt: [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Töltse le és telepítse az emulátort.

### <a name="configure-the-emulator"></a>Az emulator konfigurálása

- Nyissa meg az emulátorban, és győződjön meg arról, az URL-címet a robot fut ugyanabban a port van-e állítva. Valószínűleg: `http://localhost:3978/api/messages`

### <a name="test"></a>Tesztelés 

#### <a name="scenario-1-store-is-closed"></a>1. forgatókönyv: Store le van zárva.
1. Adja meg a "üzemi". Ez az egyező szöveg kezelje, és lehetővé teszik a Beszélgetéstanuló modellre.
2. Adja meg a "hello".  Mivel `isOpen` érték nincs megadva, a bot tudatja Önnel "Vagyok Sajnáljuk, hogy már lezárt" és a munkamenet befejezéséhez.

#### <a name="scenario-2-store-is-open"></a>2. forgatókönyv: Store nyitva.
3. Adja meg a "áruház megnyitásához".  Ez a `isOpen` igaz értékre.
4. Adja meg a "üzemi".
5. Adja meg a "hello".  Mivel `isOpen` értéke igaz, a bot jelenik meg: "Mi szeretne vásárolni?"
6. Adja meg a "vezeti. "chair" menti a rendszer CL memóriába a entitásként `purchaseItem`. A teljes munkamenet visszahívás meghívásainak ezt az értéket másolja a beszélgetés áruházra mutató.
7. Adja meg a "előzményei".  A robot meg fog jelenni "Vásárolta chair", ez volt az előző `purchaseItem`.

## <a name="conclusion"></a>Összegzés

Bemutatja, hogyan fent az úgy, hogy technológia létrehozásához bármilyen más robottal Beszélgetéstanuló képesnek kell lennie.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Elágaztatási és visszavonás](./17-branch-undo.md)
