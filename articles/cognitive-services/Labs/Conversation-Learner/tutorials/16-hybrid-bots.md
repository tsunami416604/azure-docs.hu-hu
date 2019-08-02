---
title: A Conversation Learner használata más robot-építési technológiákkal – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan használhatja a Conversation Learnert más robot-építési technológiákkal.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c964c62c34f952a547d077e93e7bb4d0eb7b192d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703674"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>A Conversation Learner használata más robot-építési technológiákkal

Ez az oktatóanyag azt ismerteti, hogyan használhatók a Conversation Learner más robot-építési technológiákkal, és hogyan oszthatók meg a memória (vagy az állapot) a különböző technológiák között. 

## <a name="video"></a>Videó

[![Hybrid bots – oktatóanyag – előzetes verzió](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz a robot-emulátort kell használnia a naplók létrehozásához, nem a webes felhasználói felületet. A bot Framework Emulator beállításával kapcsolatos további információk [itt](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0)érhetők el. 

Ehhez az oktatóanyaghoz a hibrid oktatóanyag robotjának futtatása szükséges:

    npm run tutorial-hybrid

## <a name="details"></a>Részletek

Míg Conversation Learner a vezérlésben van, a Conversation Learner-munkamenethez viszonyított összes állapotot a Conversation Learner memóriájában kell tárolni. Erre azért van szükség, mert a gépi tanulás az állapot alapján határozza meg, hogyan vezesse a beszélgetést. A külső állapot átadható a OnSessionStartCallback Conversation Learner, amelyet a rendszer a munkamenet megkezdése után hív meg. Ha a munkamenet leáll, a OnSessionEndCallback a belső állapotot is visszaküldheti.

Szinte úgy gondolja, hogy a Conversation Learner függvény hívása, amely némi kezdeti állapotba kerül, és értékeket ad vissza.

Ebben a példában egy hibrid robotot fog létrehozni két különböző rendszer használatával:
1. Egy Conversation Learner modell <br/>
    A beszélgetési tanulói modell használatával határozza meg a robot következő műveletét az aktuális munkamenet alapján. A robot ezen része egy kezdeti állapotot `isOpen` vesz igénybe (amely azt jelzi, hogy egy tároló nyitott vagy lezárt), és egy másikat ad vissza `purchaseItem` (a felhasználó által megvásárolt elem neve).

2. Szöveg egyeztetése <br />
    Egyszerűen áttekintheti a bejövő szöveget adott karakterláncokhoz és válaszokhoz. A robot ezen része kezeli a robotok más tárolási mechanizmusait, és felelős a CL-munkamenet elindításához. Konkrétan három változót kezel `usingConversationLearner`: `storeIsOpen`, és `purchaseItem`.

Kezdjük azzal, hogy megvizsgáljuk a bemutatóban használt modellt.

### <a name="open-the-demo"></a>A bemutató megnyitása

A webes felhasználói felületen kattintson az "oktatóanyagok importálása" elemre, és válassza ki a "tutorial-16-HybridBot" nevű modellt.

## <a name="entities"></a>Entitások

Nyissa meg az entitások lapot, és `isOpen` figyelje meg, hogy két entitás: és`purchaseItem`

Az entitások használatának megismeréséhez nyissa meg a következő fájlt `C:\<installedpath>\src\demos\tutorialHybrid.ts` : a visszahívások megjelenítéséhez.

Figyelje meg, hogy a `OnSessionStartCallback` kód az `isOpen` entitás értékeként másolja át a botbuilder témakörben beszélgetési tárterület `storeIsOpen` értékét, hogy az elérhető legyen Conversation Learner számára. Tekintse meg a következő kódot:

![](../media/tutorial17_sessionstart.PNG)

Hasonlóképpen, a kódban `OnSessionEndCallback` (ha a munkamenet egy megtanult tevékenység miatt véget ért, nem csupán időtúllépés), az entitás `purchaseItem` értékét a botbuilder témakörben-tárolóba `purchaseItem`másolja. Tekintse meg a következő kódot:

![](../media/tutorial17_sessionend.PNG)

Most nézzük meg a műveleteket.

## <a name="actions"></a>Műveletek

Figyelje meg, hogy a modell négy művelettel rendelkezik.

A műveletekre vonatkozó tervezett szabályok a következők:

- Ha az `isOpen` entitás be van állítva, a robot megkérdezi, hogy mit szeretne vásárolni? és tárolja azt a `puchaseItem` tárolóhelyen.
- Ha `isOpen` nincs beállítva, a robot azt fogja mondani, hogy "sajnálom, hogy bezártuk".
- A másik két művelet típusa a következő `END_SESSION`:.
- A END_SESSION művelet azt jelzi, hogy a beszélgetés befejezésének ConversationLearner.

### <a name="overall-bot-logic"></a>Teljes bot-logika

Először azt láthatja, hogy ha a bot `usingConversationLearner` állapotának jelzője be van állítva, átadjuk a vezérlést Conversation Learner. Ha nem, a vezérlést átadjuk másra.  Ebben a példában egyszerű szöveges megfeleltetést mutatunk be, de ez lehet bármilyen más bot-technológia, beleértve a LUIS, a QnA Makert, valamint a Conversation Learner egy másik példányát is.

A felhasználónak meg kell nyitnia és be kell állítania a tárolót, ezért a karakterláncot össze kell hasonlítani az "Open Store" és a "Bezárás" tárolóval, és be kell állítani a "storeIsOpen" jelzőt.

A következő lépésként meg kell adnia a vezérlés átadását a Conversation Learner modellre. Ha a "Shop" karakterláncnak felel meg, a következőket tesszük:
- Állítsa be `usingConversationLearner` a jelzőt a robot memóriájában.
- Hívja meg a "StartSession" metódust Conversation Learner modellen.  Ez elindítja a "onSessionStartCallback" karakterláncot, amely az `isOpen` entitás értékének inicializálását végzi.

Lásd alább:

![](../media/tutorial17_useConversationLearner.PNG)

Emellett a "History" szöveg is megegyeznek, amely a legutóbbi vásárlási tételt jeleníti meg.
Végül, ha bármilyen más be van írva, a rendelkezésre álló felhasználói parancsok jelennek meg.

## <a name="train-dialog"></a>Betanítás párbeszédpanel

Ebben az oktatóanyagban a modell már előre betanítva van.  Tesztelni fogjuk a teljes robotot, hogy meglássuk, milyen hatással van a kezdő és a záró munkamenet-visszahívás a gyakorlatban.

## <a name="testing-the-bot"></a>A robot tesztelése

Az egybeszélgetéses karcsúbb modell botoktól eltérően a Conversation Learner felhasználói felületen nem fogja tudni tesztelni ezt, mert csak a Conversation Learner modell által kezelt tartalmakat tudja megjeleníteni.

### <a name="install-the-bot-framework-emulator"></a>A bot Framework Emulator telepítése

- Nyissa meg a következőt: [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Töltse le és telepítse az emulátort.

### <a name="configure-the-emulator"></a>Az emulátor konfigurálása

- Nyissa meg az emulátort, és győződjön meg arról, hogy az URL-cím ugyanazt a portot célozza meg, amelyen a robot fut. Valószínűleg`http://localhost:3978/api/messages`

### <a name="test"></a>Tesztelés 

#### <a name="scenario-1-store-is-closed"></a>forgatókönyv 1: A tároló be van zárva
1. Adja meg a "Shop" értéket. Ezt a szöveg-egyeztetés kezeli, és a Conversation Learner modell vezérlését fogja biztosítani.
2. Adja meg a "Hello" értéket.  Mivel `isOpen` az érték nincs beállítva, a robot a következőt fogja mondani: "sajnálom, hogy bezártuk", és befejezjük a munkamenetet.

#### <a name="scenario-2-store-is-open"></a>2\. forgatókönyv: A tároló nyitva van
1. Adja meg a "nyitott tároló" értéket.  Ez a `isOpen` érték TRUE (igaz) értékre lesz állítva.
1. Adja meg a "Shop" értéket.
1. Adja meg a "Hello" értéket.  Mivel `isOpen` az érték TRUE (igaz), a robot azt fogja mondani, hogy "mit szeretne vásárolni?"
1. Adja meg a "szék" értéket. az "elnök" a CL-memóriába lesz mentve `purchaseItem`az entitásként. A rendszer meghívja a munkamenet befejezésének visszahívását, amely a beszélgetési tárolóba másolja ezt az értéket.
1. Adja meg a "History" értéket.  A robot azt fogja mondani, hogy "megvásárolta a széket `purchaseItem`", mivel ez volt az utolsó.

## <a name="conclusion"></a>Összegzés

A fentiekben ismertetett módon képesnek kell lennie kombinálni a Conversation Learnert bármely más robot-építési technológiával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Elágazás és visszavonás](./17-branch-undo.md)
