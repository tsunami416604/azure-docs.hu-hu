---
title: Beépített szöveg- és képfeldolgozás indexelés közben
titleSuffix: Azure Cognitive Search
description: Az adatok kinyerése, a természetes nyelv, a képfeldolgozáskognitív képességek szemantikát és struktúrát adnak hozzá az Azure Cognitive Search folyamat nyers tartalmaihoz.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e1f62dcdb122726fc1c08b7bea4e4c214ce7906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933365"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Beépített kognitív képességek a szöveg- és képfeldolgozáshoz indexelés közben (Azure Cognitive Search)

Ebben a cikkben megismerheti az Azure Cognitive Search kognitív képességeinek kognitív képességeit, amelyeket egy olyan képességkészletbe is felvehet a tartalom és a struktúra kinyeréséhez. A *kognitív szakértelem* olyan modul vagy művelet, amely valamilyen módon átalakítja a tartalmat. Gyakran előfordul, hogy ez egy olyan összetevő, amely kinyeri az adatokat vagy a rétegszerkezetet, és ezáltal növeli a bemeneti adatok megértését. Szinte mindig, a kimenet szöveg-alapú. A *skillset* a dúsítási folyamatot meghatározó készségek gyűjteménye. 

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="built-in-skills"></a>Beépített képességek

Számos készség rugalmas abban, amit fogyasztanak vagy termelnek. Általában a legtöbb szakértelem előre betanított modelleken alapulnak, ami azt jelenti, hogy nem taníthatja be a modellt a saját betanítási adataival. Az alábbi táblázat a Microsoft által biztosított ismereteket sorolja fel és ismerteti. 

| Ügyességi | Leírás |
|-------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Egyéni, felhasználó által definiált szavak és kifejezések listájából származó szöveget keres.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ez a szakértelem egy előre betanított modell segítségével észleli a fontos kifejezéseket a kifejezéselhelyezés, a nyelvi szabályok, a más kifejezések közelsége és a kifejezés milyen szokatlan kifejezése idoben. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ez a szakértelem egy előre betanított modell segítségével észleli, hogy melyik nyelvet használja (dokumentumonként egy nyelvazonosító). Ha több nyelvet használ ugyanazon a szövegszegmensen belül, a kimenet a túlnyomórészt használt nyelv LCID azonosítója.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Mezők gyűjteményéből származó szöveget egyetlen mezőbe összesíti.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ez a szakértelem egy előre betanított modell segítségével hoz létre entitásokat egy meghatározott kategóriákhoz: személyek, hely, szervezet, e-mailek, URL-címek, datetime mezők. |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Ez a szakértelem egy előre betanított modell segítségével kivonat személyazonosításra alkalmas adatokat egy adott szövegből. A szakértelem különböző lehetőségeket is biztosít az észlelt személyazonosításra alkalmas entitások maszkolására a szövegben.  |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Ez a szakértelem egy előre betanított modellt használ a pozitív vagy negatív hangulat rekordonkénti pontszámához. Az állás 0 és 1 között van. Semleges pontszámok fordulnak elő mind a null esetben, ha a hangulat nem észlelhető, és a szöveg, amely semlegesnek tekinthető.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | A szöveget oldalakra osztja, így fokozatosan bővítheti vagy bővítheti a tartalmat. |
| [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md) | Ez a szakértelem egy előre betanított modell segítségével lefordítja a bemeneti szöveget különböző nyelvekre a normalizálásvagy a honosítás használati esetek. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ez a szakértelem képészlelési algoritmust használ a kép tartalmának azonosítására és szöveges leírás létrehozására. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optikai karakterfelismerés. |
| [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md) | Lehetővé teszi a szűrést, az alapértelmezett érték hozzárendelését és az adatok egy feltétel alapján történő egyesítését.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | Tartalmat bont ki egy fájlból a dúsítási folyamaton belül. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | A kimenetet összetett típusra (többrészes adattípusra) rendeli le, amely teljes névhez, többsoros címhez vagy vezetéknév és személyes azonosító kombinációjához használható.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Lehetővé teszi a a mi-bővítési folyamatok bővíthetőségét http-hívással egyéni webes API-ba |


Az [egyéni szakértelem](cognitive-search-custom-skill-web-api.md)létrehozásához az Egyéni [felület definiálása](cognitive-search-custom-skill-interface.md) és [példa: Egyéni szakértelem létrehozása a ai-bővítéshez](cognitive-search-create-custom-skill-example.md)című témakörben talál útmutatást.

## <a name="see-also"></a>Lásd még

+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Egyéni képzettségi felület definíciója](cognitive-search-custom-skill-interface.md)
+ [Oktatóanyag: AI-val bővített indexelés](cognitive-search-tutorial-blob.md)
