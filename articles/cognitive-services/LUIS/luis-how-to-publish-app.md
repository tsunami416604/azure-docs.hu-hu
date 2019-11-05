---
title: Alkalmazás közzététele – LUIS
titleSuffix: Azure Cognitive Services
description: Ha befejezte az aktív LUIS-alkalmazás létrehozását és tesztelését, tegye elérhetővé az ügyfélalkalmazás számára a végpontra való közzétételsel.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 0d3a413249cb9058e4098f2836131494670a1727
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491322"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Aktív, betanított alkalmazás közzététele átmeneti vagy éles végponton

Ha befejezte az aktív LUIS-alkalmazás létrehozását és tesztelését, tegye elérhetővé az ügyfélalkalmazás számára a végpontra való közzétételsel. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Közzététel

1. A végponton való közzétételhez kattintson a jobb oldali panelen a **Közzététel** elemre. 

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

Ha például egy, a [www.Luis.ai](https://www.luis.ai)-on létrehozott alkalmazáshoz létrehoz egy Luis-erőforrást két régióban, a **westus** és a **eastus**, és hozzáadja ezeket az alkalmazáshoz erőforrásként, az alkalmazás mindkét régióban közzé lesz téve. A LUIS régiókkal kapcsolatos további információkért lásd: [régiók](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Közzétételi beállítások konfigurálása

Miután kiválasztotta a tárolóhelyet, konfigurálja a közzétételi beállításokat a következőhöz:

* Hangulatelemzés
* Helyesírás-javítás
* Beszéd alapozó 

A közzététel után ezek a beállítások a **kezelés** szakasz **közzétételi beállítások** lapján érhetők el. A beállításokat minden közzététel lehetőséggel módosíthatja. Ha megszakít egy közzétételt, a közzététel során végrehajtott módosítások is megszakadnak. 

### <a name="when-your-app-is-published"></a>Az alkalmazás közzétételekor

Az alkalmazás sikeres közzétételekor a böngésző tetején megjelenik egy zöld sikert jelző értesítés. A zöld értesítési sáv a végpontokra mutató hivatkozást is tartalmaz. 

![Előugró ablak közzététele a végpontra mutató hivatkozással](./media/luis-how-to-publish-app/publish-success.png)

Ha a végpont URL-címére van szüksége, válassza ki a hivatkozást. A végpont URL-címeihez a felső menüben a **kezelés** lehetőségre kattintva, majd a bal oldali menüben válassza az **Azure-erőforrások** lehetőséget. 

## <a name="sentiment-analysis"></a>Hangulatelemzés

<a name="enable-sentiment-analysis"></a>

A hangulat elemzése lehetővé teszi a LUIS számára a [text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) integrálását az érzelmek és a kulcsfontosságú kifejezések elemzésének biztosítása érdekében. 

Nem kell megadnia Text Analytics kulcsot, és a szolgáltatáshoz nem tartozik számlázási díj az Azure-fiókhoz. 

Az érzelmi adatmennyiség egy 1 és 0 közötti pontszám, amely az adatmennyiség pozitív (közelebbről 1) vagy negatív (közelebbről 0) hangulatát jelzi. A `positive`, `neutral`és `negative` hangulati címkéje támogatott kultúrán alapul. Jelenleg csak az angol támogatja a hangulati címkéket. 

A JSON-végponti válaszról a [hangulat elemzése című témakörben](luis-concept-data-extraction.md#sentiment-analysis) talál további információt.

## <a name="spelling-correction"></a>Helyesírás-javítás

A helyesírási javítások a LUIS-felhasználó teljes előrejelzése előtt történnek. A válaszban megtekintheti az eredeti szöveg összes módosítását, beleértve a helyesírást is.

## <a name="speech-priming"></a>Beszéd alapozó

A beszédfelismerési folyamat célja a LUIS modell elküldése a beszédfelismerési szolgáltatásoknak a szöveg beszédbe való átalakítása előtt. Ez lehetővé teszi, hogy a beszédfelismerési szolgáltatás pontosabban biztosítson beszédfelismerési konverziót a modell számára. Ez lehetővé teszi, hogy a bot beszédét és a LUIS kérelmeit és válaszait egy hívással egy beszédes hívást indítson, és egy LUIS-választ kérjen vissza. Összességében kevesebb késést biztosít.

## <a name="next-steps"></a>További lépések

* Lásd: [kulcsok kezelése](./luis-how-to-azure-subscription.md) az Azure-előfizetési kulcshoz való hozzáadásához a Luis-hez, valamint a Bing Spell Check kulcs beállításához, és az összes leképezést az eredmények között.
* A közzétett alkalmazás tesztelési konzolon történő tesztelésével kapcsolatos útmutatásért lásd: [az alkalmazás betanítása és tesztelése](luis-interactive-test.md) .

