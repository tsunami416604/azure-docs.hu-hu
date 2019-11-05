---
title: Alkalmazás tesztelése a LUIS Portalon
titleSuffix: Azure Cognitive Services
description: A Language Understanding (LUIS) használatával folyamatosan dolgozhat az alkalmazáson, és javíthatja annak nyelvi megismerését.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: ebc86d1cf91cf79ab83b0f49d9898a91d8be8a75
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500283"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>A LUIS-alkalmazás tesztelése a LUIS-portálon

Az alkalmazások [tesztelése](luis-concept-test.md) egy iterációs folyamat. A LUIS-alkalmazás betanítása után tesztelje a mintát hosszúságú kimondott szöveg, és ellenőrizze, hogy a szándékok és entitások felismerése helyesen történt-e. Ha nem, akkor frissítse a LUIS alkalmazást, a betanítást és a tesztelést. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Kimondás tesztelése

1. Az alkalmazás eléréséhez válassza ki a saját **alkalmazások** lapján a saját nevét. 

1. A **teszt** kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.

    ![& Tesztelési alkalmazás betanítása oldal](./media/luis-how-to-interactive-test/test.png)

1. Adjon meg egy kiírást a szövegmezőben, és válassza az ENTER billentyűt. Annyi teszt hosszúságú kimondott szöveg is beírhat, amennyit csak szeretne a **tesztben**, de egyszerre csak egy Kimondás.

1. A Kimondás, a felső szándék és a pontszám a szövegmezőben a hosszúságú kimondott szöveg listájára kerül.

    ![Az interaktív tesztelés a helytelen szándékot azonosítja](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Pontszám vizsgálata

A teszt eredményének részleteit a **vizsgálat panelen** tekintheti meg. 
 
1. Nyissa meg a test slide (tesztelés) panelt, és válassza a **vizsgálat** lehetőséget az összehasonlítani kívánt kifejezés kiválasztásához. 

    ![A vizsgálati eredmények további részleteinek megtekintéséhez kattintson a vizsgálat gombra.](./media/luis-how-to-interactive-test/inspect.png)

1. Megjelenik a **vizsgálat** panel. A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.

    ![A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés eredményét jeleníti meg.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Legjobb pontozási szándék javítása

1. Ha a felső pontozási szándék helytelen, kattintson a **Szerkesztés** gombra.

1.  A legördülő listában válassza ki a megfelelő szándékot a teljes beállításhoz.

    ![Helyes leképezés kiválasztása](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>A hangulat eredményeinek megtekintése

Ha a találatok elemzése a **[közzétételi](luis-how-to-publish-app.md#enable-sentiment-analysis)** oldalon van konfigurálva, a teszt eredményei közé tartozik a teljes **kiértékelésben** talált hangulat. 

![A teszt ablaktábla és a hangulat elemzésének képe](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Megfelelő egyezésű minta szándéka

Ha [mintázatokat](luis-concept-patterns.md) használ, és a kihagyás egy mintázatnak felel meg, de a rossz szándékot jósolta meg, válassza a minta alapján a **Szerkesztés** hivatkozást, majd válassza ki a megfelelő szándékot.

## <a name="compare-with-published-version"></a>Összehasonlítás a közzétett verzióval

Tesztelheti az alkalmazás aktív verzióját a közzétett [végpont](luis-glossary.md#endpoint) verziószámával. Az **ellenőrzés** panelen válassza a **összehasonlítás a közzétetttel**lehetőséget. A közzétett modellel kapcsolatos bármilyen vizsgálatot levonják az Azure-előfizetési kvóta egyenlegéről. 

![Összehasonlítás közzétételsel](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Végpont JSON megtekintése a teszt panelen
Az összehasonlításhoz a **JSON-nézet megjelenítése**lehetőség kiválasztásával megtekintheti a végpont által VISSZAadott JSON-t.

![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>További beállítások a teszt panelen

### <a name="luis-endpoint"></a>LUIS-végpont

Ha több LUIS-végponttal rendelkezik, a teszt közzétett paneljén a **További beállítások** hivatkozásra kattintva módosíthatja a teszteléshez használt végpontot. Ha nem biztos abban, hogy melyik végpontot szeretné használni, válassza ki az alapértelmezett **Starter_Key**. 

![A teszt panel további beállítások hivatkozását kiemelve](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Bing Spell Check-javítások megtekintése a teszt panelen

A helyesírási javítások megtekintéséhez szükséges követelmények: 

* Közzétett alkalmazás
* Bing Spell Check a [szolgáltatás kulcsát](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). A szolgáltatás kulcsa nem tárolódik, és minden egyes böngésző-munkamenet esetében vissza kell állítani. 

A következő eljárással belefoglalhatja a [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) szolgáltatást a teszt panel eredményei között. 

1. A **teszt** ablaktáblán adjon meg egy teljes értéket. A teljes érték előrejelzése után válassza az **[ellenőrzés](#inspect-score)** lehetőséget a beírt kifejezés alatt. 

1. Amikor megnyílik a **vizsgálat** panel, válassza az **[összehasonlítás a közzétetttel](#compare-with-published-version)** lehetőséget. 

1. Amikor megnyílik a **közzétett** panel, válassza a **[További beállítások](#additional-settings-in-test-panel)** lehetőséget.

1. Az előugró ablakban jelölje be a **Bing Spell Check engedélyezése** jelölőnégyzetet, és adja meg a kulcsot, majd kattintson a **kész**gombra. 
    ![adja meg Bing Spell Check szolgáltatás kulcsát](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Adjon meg egy helytelen helyesírású lekérdezést, például `book flite to seattle`, és válassza az ENTER billentyűt. A `flite` szó helytelen helyesírását a LUIS-re eljuttatott lekérdezés váltja fel, az eredményül kapott JSON pedig az eredeti lekérdezést, `query`ként és a lekérdezés javított helyesírását mutatja, ahogy az `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg a Batch-tesztelési [fogalmakat](luis-concept-batch-test.md) , és Ismerje meg, [Hogyan](luis-how-to-batch-test.md) tesztelheti a Batch of hosszúságú kimondott szöveg.

## <a name="next-steps"></a>További lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, a további hosszúságú kimondott szöveg címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás pontosságát. 

* [Címke javasolt hosszúságú kimondott szöveg a LUIS-vel](luis-how-to-review-endpoint-utterances.md) 
* [Szolgáltatások használata a LUIS-alkalmazás teljesítményének növeléséhez](luis-how-to-add-features.md) 
