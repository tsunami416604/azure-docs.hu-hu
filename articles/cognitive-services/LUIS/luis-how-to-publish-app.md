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
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 676c6d15c4f439543a3ed74627001725632fecfa
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220885"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Aktív, betanított alkalmazás közzététele átmeneti vagy éles végponton

Ha befejezte az aktív LUIS-alkalmazás létrehozását, betanítását és tesztelését, tegye elérhetővé az ügyfélalkalmazás számára a végpontra való közzétételsel. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Közzététel

1. A végponton való közzétételhez kattintson a jobb oldali panelen a **Közzététel** elemre. 

    ![Közzététel gomb a fent, jobb oldali navigációs sávon](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Válassza ki a közzétett előrejelzési végpont beállításait, majd válassza a **Közzététel**lehetőséget.

    ![Válassza a közzétételi beállítások lehetőséget, majd válassza a közzététel gombot](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Közzétételi résidők

Válassza ki a megfelelő tárolóhelyet, amikor megjelenik az előugró ablak: 

* Átmeneti
* Éles 

Ha mindkét közzétételi tárolóhelyet használja, ez lehetővé teszi, hogy az alkalmazás két különböző verzióját elérhetővé tegye a közzétett végpontokon vagy ugyanazon a verzión két különböző végponton. 

### <a name="publishing-regions"></a>Közzétételi régiók

Az alkalmazás az **[Azure-erőforrások](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** **kezelése** -> a Luis-portálon a Luis-előrejelzési végpont erőforrásaihoz társított összes régióban megjelenik. 

Ha például egy, a [www.Luis.ai](https://www.luis.ai)-on létrehozott alkalmazáshoz létrehoz egy Luis-erőforrást két régióban, a **westus** és a **eastus**, és hozzáadja ezeket az alkalmazáshoz erőforrásként, az alkalmazás mindkét régióban közzé lesz téve. A LUIS régiókkal kapcsolatos további információkért lásd: [régiók](luis-reference-regions.md).

> [!TIP]
> 3 authoring-régió van. Ahhoz a régióhoz kell tartoznia, amelyet közzé kíván tenni. Ha minden régióban közzé kell tennie az összes régiót, a szerzői folyamatokat és a létrejövő betanított modellt mind a 3 szerzői régióban kell kezelnie. 


## <a name="configuring-publish-settings"></a>Nastavení publikování konfigurálása

Miután kiválasztotta a tárolóhelyet, konfigurálja a közzétételi beállításokat a következőhöz:

* Hangulatelemzés
* Helyesírás-javítás – v2 előrejelzési végpont
* Beszéd alapozó 

A közzététel után ezek a beállítások a **kezelés** szakasz **közzétételi beállítások** lapján érhetők el. A beállításokat minden közzététel lehetőséggel módosíthatja. Ha megszakít egy közzétételt, a közzététel során végrehajtott módosítások is megszakadnak. 

### <a name="when-your-app-is-published"></a>Az alkalmazás közzétételekor

Az alkalmazás sikeres közzétételekor megjelenik egy sikeres értesítés a böngésző tetején. Az értesítés a végpontokra mutató hivatkozást is tartalmaz. 

Ha a végpont URL-címe van szüksége, válassza ki a hivatkozást. A végpont URL-címeihez a felső menüben a **kezelés** lehetőségre kattintva, majd a bal oldali menüben válassza az **Azure-erőforrások** lehetőséget. 

## <a name="sentiment-analysis"></a>Hangulatelemzés

<a name="enable-sentiment-analysis"></a>

A hangulat elemzése lehetővé teszi a LUIS számára a [text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) integrálását az érzelmek és a kulcsfontosságú kifejezések elemzésének biztosítása érdekében. 

Nem kell adnia a Text Analytics kulcsot, és nem jár költségekkel számlázási ezt a szolgáltatást az Azure-fiókjába. 

Véleményadatok egy 1 és 0 a pozitív jelző közötti pontszámot (közelebb 1) vagy negatív (0 közelebb) az adatok a róluk szóló véleményeket. A `positive`, `neutral`és `negative` hangulati címkéje támogatott kultúrán alapul. Jelenleg csak az angol támogatja a hangulati címkéket. 

A JSON-végponti válaszról a [hangulat elemzése című témakörben](luis-concept-data-extraction.md#sentiment-analysis) talál további információt.

## <a name="spelling-correction"></a>Helyesírás-javítás

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

A helyesírási javítások a LUIS-felhasználó teljes előrejelzése előtt történnek. A válaszban megtekintheti az eredeti szöveg összes módosítását, beleértve a helyesírást is.

## <a name="speech-priming"></a>Beszéd alapozó

A beszédfelismerési folyamat célja a LUIS modell elküldése a beszédfelismerési szolgáltatásoknak a szöveg beszédbe való átalakítása előtt. Ez lehetővé teszi, hogy a beszédfelismerési szolgáltatás pontosabban biztosítson beszédfelismerési konverziót a modell számára. Ez lehetővé teszi, hogy a bot beszédét és a LUIS kérelmeit és válaszait egy hívással egy beszédes hívást indítson, és egy LUIS-választ kérjen vissza. Összességében kevesebb késést biztosít.

## <a name="next-steps"></a>További lépések

* Lásd: [kulcsok kezelése](./luis-how-to-azure-subscription.md) az Azure-előfizetési kulcshoz való hozzáadásához a Luis-hez, valamint a Bing Spell Check kulcs beállításához, és az összes leképezést az eredmények között.
* A közzétett alkalmazás tesztelési konzolon történő tesztelésével kapcsolatos útmutatásért lásd: [az alkalmazás betanítása és tesztelése](luis-interactive-test.md) .

