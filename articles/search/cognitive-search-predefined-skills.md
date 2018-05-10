---
title: Adatok kinyerése, a természetes nyelvű, a lemezkép feldolgozása (Azure Search) képességek az előre megadott |} Microsoft Docs
description: Adatok kinyerése, természetes nyelvű kognitív képességek feldolgozása kép szemantikáját és struktúra hozzáadása egy Azure címsorának folyamat nyers tartalma.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>A tartalom dúsító (Azure Search) előre definiált képességek

Ebből a cikkből megismerheti az kognitív Search megadott kognitív szakértelem. A *kognitív szakértelem* valamilyen módon tartalom eseményleírókkal művelet. Gyakran a program, amely kinyeri az adatokat vagy struktúra arra következtet, és ezért szabályozva az a ismeretekkel rendelkezik a bemeneti adatok információhasználat összetevő. Szinte mindig eredménye szöveges. A *skillset* gyűjteménye, amelyek meghatározzák a dúsító csővezeték képességek. 

## <a name="predefined-skills"></a>Előre definiált képességek

Több ismeretek rugalmasak mi azok felhasználását vagy eredményez. A legtöbb ismeretek általában előzetes betanított modellek, ami azt jelenti, hogy a modell használatával saját betanítási adata nem betanítása alapul. Egyéni szakértelem létrehozásával kapcsolatos útmutatóért lásd: [hogyan adhat meg egy egyéni felületet](cognitive-search-custom-skill-interface.md) és [példa: egyéni szakértelem létrehozása](cognitive-search-create-custom-skill-example.md). A következő táblázat felsorolja és ismerteti a Microsoft által biztosított képességek. 

| Szakértelem | Leírás |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Szakértelem kifejezés elhelyezését, nyelvi szabályok, más feltételek közelében, és hogyan szokatlan kifejezés belül van az adatok alapján fontos kifejezések észleléséhez pretrained modellt használ. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | A nyelv észleléséhez pretrained modell szakértelem használ (egy nyelvi azonosító dokumentumonként) használt. Több nyelv belül az azonos szöveg szegmensek használata esetén a túlnyomórészt használt nyelv az LCID eredménye.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | A szöveg egy mező mezőinek gyűjteményét összesíti.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Szakértelem pretrained modellt használ az entitások rögzített állítja be a kategóriák létrehozására: személyek, a hely, a szervezet. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Szakértelem pretrained modellt használ, egy rekord által rekord alapon pozitív vagy negatív véleményeket pontozása céljából. Az érték 0 és 1 között. Semleges pontszámok mindkét null esetekben fordulhat elő, ha a céggel kapcsolatos véleményeket nem észlelhető, és a szöveg, amely semleges.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Felosztja a szöveget lapokat, hogy kiegészítése vagy választhasson fokozatosan a tartalmat. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Szakértelem kép észlelési algoritmust használ a kép a tartalmat, és a leírás generálásához. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optikai karakter használatát. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | A Maps kimenet egy összetett típus egy több lépésből álló adatok, a teljes nevet, egy többsoros cím vagy vezetékneve és a személyes azonosító felhasználható. |

## <a name="see-also"></a>Lásd még

+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Egyéni ismeretek felületdefiníció](cognitive-search-custom-skill-interface.md)
+ [Oktatóanyag: Dúsított kognitív Keresés indexelő](cognitive-search-tutorial-blob.md)