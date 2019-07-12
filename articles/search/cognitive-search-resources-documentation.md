---
title: A kognitív keresés dokumentációja erőforrások – Azure Search
description: Jegyzettel ellátott listája cikkeket, oktatóanyagok, minták és blog kapcsolódó a cognitive search számítási feladatokat az Azure Search tesz közzé.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 41637fae5592ac292da22303071d51b43116c78b
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671911"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentációs forrásanyagot cognitive search számítási feladatokhoz

Cognitive search, már általánosan elérhető, amely egy új felderítési bővítést az Azure Search-indexelő, amely segít a rejtett információkat talál az nem szöveges forrásokból és magánháztartás szöveg, átalakítja őket az Azure Search teljes szöveges kereshető tartalmakká.

A következő cikkekben talál olyan cognitive search teljes dokumentációját.

## <a name="getting-started"></a>Első lépések
+ [Mi a cognitive search?](cognitive-search-concept-intro.md)
+ [Rövid útmutató: A portálon a kognitív keresés kipróbálása](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg, a cognitive search API-k](cognitive-search-tutorial-blob.md)
+ [Példa: Cognitive Search egyéni műveleteket létrehozása](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Útmutatók
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Hogyan lehet hivatkozni a képességek alkalmazási lehetőségét a jegyzetek](cognitive-search-concept-annotations-syntax.md)
+ [Mezők leképezése egy index](cognitive-search-output-field-mapping.md)
+ [Hogyan dolgozza fel, és információt nyerhet ki képekből](cognitive-search-concept-image-scenarios.md)
+ [Hogyan lehet Azure Search-index újraépítése](search-howto-reindex.md)
+ [Egy egyéni képesség felületet definiálása](cognitive-search-custom-skill-interface.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Hivatkozás

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Képességcsoport létrehozása (api-version = a 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Indexelő létrehozása (api-version = a 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Lásd még

+ [Az Azure Search REST API-val](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexelők az Azure Search szolgáltatásban](search-indexer-overview.md)
+ [Mi az az Azure Search?](search-what-is-azure-search.md)
