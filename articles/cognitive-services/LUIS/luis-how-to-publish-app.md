---
title: Alkalmazás közzététele – LUIS
titleSuffix: Azure Cognitive Services
description: Ha befejezte a elkészítését és tesztelését a LUIS alkalmazás aktív, tegye elérhetővé számára az ügyfélalkalmazás közzétesszük azt a végpontot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257814"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Aktív, betanított alkalmazás közzététele átmeneti vagy éles végponton

Ha befejezte a elkészítését és tesztelését a LUIS alkalmazás aktív, tegye elérhetővé számára az ügyfélalkalmazás közzétesszük azt a végpontot. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Közzététel

1. A végponthoz való közzétételéhez válassza **közzététel** a képernyő felső részén kattintson a panel jobb. 

    ![Közzététel gomb a fent, jobb oldali navigációs sávon](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Válassza ki a közzétett előrejelzési végpont beállításait, majd válassza a **Közzététel**lehetőséget.

    ![Válassza a közzétételi beállítások lehetőséget, majd válassza a közzététel gombot](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Közzétételi résidők

Válassza ki a megfelelő tárolóhelyet, amikor megjelenik az előugró ablak: 

* Átmeneti.
* Éles. 

Ha mindkét közzétételi tárolóhelyet használja, ez lehetővé teszi, hogy az alkalmazás két különböző verzióját elérhetővé tegye a közzétett végpontokon vagy ugyanazon a verzión két különböző végponton. 

### <a name="publishing-regions"></a>Közzétételi régiók

Az alkalmazás közzé van téve a Luis-előrejelzési végpont erőforrásaihoz társított összes régióban a LUIS-portálon. 

Ha például egy, a [www.Luis.ai](https://www.luis.ai)-on létrehozott alkalmazáshoz létrehoz egy Luis-erőforrást két régióban, a **westus** és a **eastus**, és hozzáadja ezeket az alkalmazáshoz erőforrásként, az alkalmazás mindkét régióban közzé lesz téve. A LUIS régiók kapcsolatos további információkért lásd: [régiók](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Nastavení publikování konfigurálása

Miután kiválasztotta a tárolóhelyet, konfigurálja a közzétételi beállításokat a következőhöz:

* Hangulatelemzés
* Helyesírás-javítás
* Beszéd alapozó 

A közzététel után ezek a beállítások a **kezelés** szakasz **közzétételi beállítások** lapján érhetők el. A beállításokat minden közzététel lehetőséggel módosíthatja. Ha megszakít egy közzétételt, a közzététel során végrehajtott módosítások is megszakadnak. 

### <a name="when-your-app-is-published"></a>Az alkalmazás közzétételekor

Ha az alkalmazás sikeresen közzé lett téve, egy zöld, sikeres értesítés jelenik meg, a böngésző tetején. A zöld értesítési sáv a végpontok egy hivatkozást is tartalmaz. 

![Előugró ablak közzététele a végpontra mutató hivatkozással](./media/luis-how-to-publish-app/publish-success.png)

Ha a végpont URL-címe van szüksége, válassza ki a hivatkozást. A végpont URL-címeihez a felső menüben a **kezelés** lehetőségre kattintva, majd a bal oldali menüben válassza az **Azure-erőforrások** lehetőséget. 

## <a name="sentiment-analysis"></a>Hangulatelemzés

<a name="enable-sentiment-analysis"></a>

Hangulatelemzés lehetővé teszi, hogy a LUIS integrálása [Szövegelemzés](https://azure.microsoft.com/services/cognitive-services/text-analytics/) a vélemény és a kulcs kifejezés elemzést. 

Nem kell adnia a Text Analytics kulcsot, és nem jár költségekkel számlázási ezt a szolgáltatást az Azure-fiókjába. 

Véleményadatok egy 1 és 0 a pozitív jelző közötti pontszámot (közelebb 1) vagy negatív (0 közelebb) az adatok a róluk szóló véleményeket. `positive`A, `neutral`a, a és `negative` a a által támogatott kulturális környezet. Jelenleg csak az angol támogatja a hangulati címkéket. 

A JSON-végpont választ véleményelemzéssel kapcsolatos további információkért lásd: [hangulatelemzés](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Helyesírás-javítás

A helyesírási javítások a LUIS-felhasználó teljes előrejelzése előtt történnek. A válaszban megtekintheti az eredeti szöveg összes módosítását, beleértve a helyesírást is.

## <a name="speech-priming"></a>Beszéd alapozó

A beszédfelismerési folyamat célja a LUIS modell elküldése a beszédfelismerési szolgáltatásoknak a szöveg beszédbe való átalakítása előtt. Ez lehetővé teszi, hogy a beszédfelismerési szolgáltatás pontosabban biztosítson beszédfelismerési konverziót a modell számára. Ez lehetővé teszi, hogy a bot beszédét és a LUIS kérelmeit és válaszait egy hívással egy beszédes hívást indítson, és egy LUIS-választ kérjen vissza. Összességében kevesebb késést biztosít.

## <a name="next-steps"></a>További lépések

* Lásd: [kulcsok kezelése](./luis-how-to-azure-subscription.md) kulcsok hozzáadása az Azure-előfizetés LUIS kulcsot, és hogyan állíthatja be, a Bing Spell Check kulcs és a keresési eredmények között szerepeljen az összes szándék fog vonatkozni.
* Lásd: [Train és tesztelje alkalmazását](luis-interactive-test.md) vonatkozó utasításokat a közzétett alkalmazás tesztelése a test-konzolon.

