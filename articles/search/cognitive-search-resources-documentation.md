---
title: Kognitív keresési dokumentációs erőforrásokat (Azure Search) |} Microsoft Docs
description: Cikkek, oktatóanyagok, példák és blog megjegyzésekkel ellátott listáját az Azure Search kognitív kapcsolódó keresési munkaterhelések küldi.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: cee479b81cb5d8b6cd3306e3735d72c9e64e6045
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640353"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentációs forrásanyagokat kognitív keresési munkaterhelések

Kognitív, mostantól nyilvános előzetes verziójában, a művelet új dúsító réteg az Azure Search indexelő, amely nem szöveges források és magánháztartás szöveg átalakítása azt a teljes szöveges kereshető tartalom az Azure Search rejtett információkat talál.

A következő cikkekben talál olyan kognitív keresési teljes dokumentációját.

## <a name="getting-started"></a>Első lépések
+ [Mi az a kognitív keresési?](cognitive-search-concept-intro.md)
+ [Gyors üzembe helyezés: Próbálja kognitív keresési a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: Ismerje meg a kognitív keresési API-k](cognitive-search-tutorial-blob.md)
+ [Példa: egyéni képesség létrehozása](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Útmutató útmutató
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hogyan hivatkozható széljegyzetet egy skillset](cognitive-search-concept-annotations-syntax.md)
+ [Hogyan képezheti mezők index](cognitive-search-output-field-mapping.md)
+ [Hogyan kell feldolgozni információk kinyerése lemezképek](cognitive-search-concept-image-scenarios.md)
+ [How Azure Search-index újraépítése](search-howto-reindex.md)
+ [Egy egyéni ismeretek felületet definiálása](cognitive-search-custom-skill-interface.md)
+ [Hibaelhárítási tippek](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Leírások

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Előzetes REST API-n](search-api-2017-11-11-preview.md)
  + [Hozzon létre Skillset (api-version = 2017-11-11 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Hozzon létre indexelőt (api-version = 2017-11-11 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Lásd még

+ [Az Azure Search REST API-n](https://docs.microsoft.com/rest/api/searchservice/)
+ [Az Azure Search indexelő](search-indexer-overview.md)
+ [Mi az az Azure Search?](search-what-is-azure-search.md)