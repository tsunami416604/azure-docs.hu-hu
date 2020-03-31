---
title: Dokumentációs hivatkozások a ai-dúsításhoz
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search a i-bővítési számítási feladatokkal kapcsolatos cikkek, oktatóanyagok, minták és blogbejegyzések jegyzetekkel elkészült listája.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616198"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Dokumentációs erőforrások a a i-dúsításhoz az Azure Cognitive Search-ben

A i bővítés az Azure Cognitive Search indexelés, amely megkeresi a látens információkat a nem szöveges forrásokés a nem differenciált szöveget, átalakítva azt teljes szöveges kereshető tartalom az Azure Cognitive Search.

A következő cikkek a a ai-dúsítás teljes dokumentációját tartalmazza.

## <a name="getting-started"></a>Első lépések
+ [Bevezetés a a a mi az Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Rövid útmutató: Hozzon létre egy kognitív képességeket az Azure Portalon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: AI-val bővített indexelés](cognitive-search-tutorial-blob.md)
+ [Példa: Egyéni szakértelem létrehozása a ai-dúsításhoz](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Útmutató útmutató
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Annotációk hivatkozása képességcsoportokban](cognitive-search-concept-annotations-syntax.md)
+ [Mezők hozzárendelése indexhez](cognitive-search-output-field-mapping.md)
+ [Képek feldolgozásának és kinyerése](cognitive-search-concept-image-scenarios.md)
+ [Az Azure Cognitive Search indexének újraépítése](search-howto-reindex.md)
+ [Egyéni képzettségi felület definiálása](cognitive-search-custom-skill-interface.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referencia

+ [Beépített képességek](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Egyéni készségek
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Elavult képességek](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Skillset létrehozása (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Indexelő létrehozása (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Lásd még

+ [Az Azure Cognitive Search REST API-jai](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Mi az az Azure Cognitive Search?](search-what-is-azure-search.md)
