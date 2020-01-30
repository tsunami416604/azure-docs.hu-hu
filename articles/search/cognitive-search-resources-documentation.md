---
title: Dokumentációs hivatkozások AI-bővítéshez
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search mesterséges intelligencia-bővítési munkaterhelésekhez kapcsolódó cikkek, oktatóanyagok, minták és blogbejegyzések jegyzetekkel ellátott listája.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: e73e69f90b1228154d7f209c54c6b52cc03d5eb4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837790"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Az AI-bővítés dokumentációs erőforrásai az Azure-ban Cognitive Search

Az AI-bővítés az Azure Cognitive Search indexelésének egyik funkciója, amely a nem szöveges forrásokban és a nem differenciált szövegekben található látens információkat, valamint a teljes szöveges kereshető tartalmat átalakítja az Azure Cognitive Searchban.

A következő cikkek a mesterséges intelligenciával kapcsolatos teljes dokumentációt tartalmaznak.

## <a name="getting-started"></a>Első lépések
+ [Az AI bemutatása az Azure-ban Cognitive Search](cognitive-search-concept-intro.md)
+ [Gyors útmutató: kognitív készségkészlet létrehozása a Azure Portalban](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: bővített indexelés az AI-vel](cognitive-search-tutorial-blob.md)
+ [Példa: egyéni képesség létrehozása AI-bővítéshez](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Útmutató
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Annotációk hivatkozása képességcsoportokban](cognitive-search-concept-annotations-syntax.md)
+ [Mezők leképezése indexre](cognitive-search-output-field-mapping.md)
+ [Információk feldolgozása és kinyerése képekből](cognitive-search-concept-image-scenarios.md)
+ [Azure Cognitive Search index újraépítése](search-howto-reindex.md)
+ [Egyéni szaktudás-illesztőfelület definiálása](cognitive-search-custom-skill-interface.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Leírások

+ [Beépített szaktudás](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. Skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. Skills. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft. Skills. Text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. Skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. Skills. vízió. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. Skills. vízió. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. Skills. util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. Skills. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Egyéni készségek
  + [Microsoft. Skills. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Elavult képességek](cognitive-search-skill-deprecated.md)
  + [Microsoft. Skills. Text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Készségkészlet létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Indexelő létrehozása (API-Version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Lásd még:

+ [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Mi az Azure Cognitive Search?](search-what-is-azure-search.md)
