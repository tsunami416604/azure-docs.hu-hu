---
title: Beépített adatok kinyerése, természetes nyelv, képfeldolgozás – Azure Search
description: Az adatok kinyerése, természetes nyelve, képfeldolgozása kognitív szaktudása szemantikai és strukturálatlan tartalmat ad egy Azure Search folyamat nyers tartalmához.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 061d969269eb526b8f02f14de58e2da20c459a38
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265495"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Tartalom-gazdagítás előre definiált képességei (Azure Search)

Ebben a cikkben megismerheti a Azure Search által biztosított kognitív képességeket. A *kognitív képességek* egy olyan művelet, amely valamilyen módon átalakítja a tartalmat. Gyakran előfordul, hogy ez egy olyan összetevő, amely adatokat vagy kikövetkeztetett struktúrát gyűjt, és így fokozza a bemeneti adatok megértését. Szinte mindig a kimenet Text-alapú. A *készségkészlet* olyan szaktudás gyűjteménye, amely meghatározza a dúsítási folyamatot. 

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="predefined-skills"></a>Előre megadott képesség

Az általuk felhasznált vagy előállított képességek számos képességgel rendelkeznek. Általánosságban elmondható, hogy a legtöbb ismeret az előre betanított modelleken alapul, ami azt jelenti, hogy a modellt nem lehet a saját betanítási adataival betanítani. A következő táblázat felsorolja és leírja a Microsoft által biztosított képességeket. 

| Ügyességi | Leírás |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ez a képesség egy előképzett modellt használ a fontos kifejezések elhelyezésére, a nyelvi szabályokra, a más feltételekhez való közelségre, valamint arra, hogy a kifejezés milyen szokatlanul legyen a forrásadatok között. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ez a képesség egy előképzésen alapuló modellt használ a használt nyelv észleléséhez (dokumentum-azonosító egy nyelven). Ha az azonos szöveges szegmensekben több nyelv is használatos, a kimenet a túlnyomórészt használt nyelv LCID-je.|
| [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md) | Egy mezőkből álló gyűjteményből származó szöveget egyesít egyetlen mezőbe.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ez a képesség egy előre betanított modellt használ a rögzített kategóriákhoz tartozó entitások létrehozásához: személyek, hely, szervezet, e-mailek, URL-címek, datetime-mezők. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Ez a képesség egy előre betanított modellt használ a rekordok pozitív vagy negatív ismeretének megértékelésére a rekord alapján. A pontszám 0 és 1 között van. Semleges pontszámok fordulnak elő mind a null eset esetében, ha a rendszer nem észleli a véleményét, és a semlegesnek minősülő szöveg.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Feldarabolja a szöveget a lapokra, így Növekményesen bővítheti vagy kibővítheti a tartalmat. |
| [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Ez a képesség egy előképzett modellt használ a bemeneti szöveg különböző nyelvekre való lefordításához a normalizálás vagy a honosítás használati eseteihez. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ez a képesség egy képészlelési algoritmust használ a rendszerkép tartalmának azonosításához és szöveges leírás létrehozásához. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optikai karakterfelismerés. |
| [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Lehetővé teszi a szűrést, az alapértelmezett érték hozzárendelését és az adategyesítést egy feltétel alapján.|
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Leképezi a kimenetet egy összetett típusra (egy többrészes adattípusra, amely a teljes név, a többsoros címek vagy a vezetéknév és a személyes azonosító kombinációja esetén használható) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Lehetővé teszi a kognitív keresési folyamat bővíthetőségét azáltal, hogy HTTP-hívást végez egy egyéni webes API-n |


Az [egyéni képességek](cognitive-search-custom-skill-web-api.md)létrehozásával kapcsolatos útmutatásért tekintse meg az [Egyéni felület definiálása](cognitive-search-custom-skill-interface.md) és [példa: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Lásd még

+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Egyéni képességek kezelőfelületének definíciója](cognitive-search-custom-skill-interface.md)
+ [Oktatóanyag: Gazdagított indexelés kognitív kereséssel](cognitive-search-tutorial-blob.md)
