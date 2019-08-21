---
title: Kognitív keresési dokumentációs erőforrások – Azure Search
description: A Azure Searchban található kognitív keresési munkaterhelésekhez kapcsolódó cikkek, oktatóanyagok, minták és blogbejegyzések jegyzetekkel ellátott listája.
services: search
manager: nitinme
author: HeidiSteen
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.subservice: cognitive-search
ms.openlocfilehash: 88e9db6526733960045e9a7f49e1f73fcaca8cf6
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639107"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentációs erőforrások a kognitív keresési munkaterhelésekhez

Az általánosan elérhető kognitív keresés egy új alkoholtartalom-növelési réteg Azure Search indexelésben, amely látens információt keres a nem szöveges forrásokban és a nem differenciált szövegeket, és átalakítja a teljes szöveges kereshető tartalmat a Azure Searchban.

A következő cikkek a kognitív keresés teljes dokumentációját jelentik.

## <a name="getting-started"></a>Első lépések
+ [Mi a kognitív keresés?](cognitive-search-concept-intro.md)
+ [Rövid útmutató: A kognitív keresés kipróbálása a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg a kognitív keresési API-kat](cognitive-search-tutorial-blob.md)
+ [Példa: Egyéni képesség létrehozása a kognitív kereséshez](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Útmutató
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Megjegyzések készségkészlet való hivatkozása](cognitive-search-concept-annotations-syntax.md)
+ [Mezők leképezése indexre](cognitive-search-output-field-mapping.md)
+ [Információk feldolgozása és kinyerése képekből](cognitive-search-concept-image-scenarios.md)
+ [Azure Search index újraépítése](search-howto-reindex.md)
+ [Egyéni szaktudás-illesztőfelület definiálása](cognitive-search-custom-skill-interface.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Hivatkozás

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Egyéni készségek
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Elavult képességek](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Készségkészlet létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Indexelő létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Lásd még

+ [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexelő a Azure Search](search-indexer-overview.md)
+ [Mi az az Azure Search?](search-what-is-azure-search.md)
