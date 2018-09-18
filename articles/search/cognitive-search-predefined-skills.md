---
title: Az előre meghatározott adatok kinyerése, természetes nyelvi, képfeldolgozó képességek (Azure Search) |} A Microsoft Docs
description: Adatok kinyerése, természetes nyelv és kognitív képességeket képfeldolgozó szemantika és a struktúra hozzáadása Azure értesítés keresése folyamatban nyers tartalma.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: c342eca8f27db713e139c187147abddd80eb854e
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734534"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>A tartalom Adatbővítés (Azure Search) előre megadott képesség

Ebben a cikkben megismerkedhet az Azure Search szolgáltatással a megadott, a kognitív képességeket. A *cognitive szakértelem* egy művelet, amely valamilyen módon tartalom alakítja át. Gyakran nem egy összetevő, amely kinyeri az adatokat vagy kikövetkezteti a struktúrát, és ezért úgy bővíti a bemeneti adatok az ismertetése. Szinte mindig kimenete szöveges. A *indexmezők* gyűjteménye, amelyek meghatározzák a Adatbővítés folyamat képességek. 

> [!NOTE]
> A kognitív keresés nyilvános előzetes verzióban érhető el. Képességcsoport végrehajtási, és a lemezkép kinyerése és a normalizálási jelenleg rendelkezésre állnak az ingyenes. Később az ezen funkciók díjszabásáról jelentjük be. 

## <a name="predefined-skills"></a>Előre megadott képesség

Több ismeretek olyan rugalmas, mire azok felhasználását vagy előállításához. Általában a legtöbb képességek előre betanított modellek, ami azt jelenti, hogy Ön nem betanítja a modellt, a saját betanítási adatok használatával alapulnak. Egy egyéni ismeretek létrehozásával kapcsolatos útmutatásért lásd: [egy egyéni felületen definiálása](cognitive-search-custom-skill-interface.md) és [példa: egyéni műveleteket létrehozása](cognitive-search-create-custom-skill-example.md). A következő táblázat felsorolja és ismerteti a Microsoft által biztosított képességek. 

| Szakértelem | Leírás |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Ezen a képzettségi pretrained modellt használ a fontos kifejezések elhelyezése kifejezés, nyelvi szabályok, más feltételek közelében, és hogyan szokatlan kifejezés belül az adatok alapján észleli. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | A képzettségi használ egy pretrained a modell nyelvét észleléséhez használt (egy dokumentum egy nyelvi azonosító). Ha több nyelvet használ az azonos szöveg szegmensen belül, a kimenete a túlnyomórészt használt nyelv az LCID.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Összesíti a szöveget a mezők gyűjteménye egyetlen mezőbe.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Ezen a képzettségi pretrained modellt használ a kategóriák rögzített készletének entitások létrehozására: személyek, tartózkodási hely, szervezeti. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Szakértelem pretrained modellt használ, rekord által rekord alapján pontszámot rendelni az pozitív vagy negatív véleményeket. A pontszám értéke 0 és 1. Semleges pontszámok a NULL értékű mindkét esetben fordulhat elő, ha a vélemény nem észlelhető, és a szöveg, amely tekinthető semleges.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Felosztja a szöveget oldalakat, hogy bővítését, vagy bővítésével fokozatosan a tartalmat. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Szakértelem kép tartalma azonosításához, és a egy leírás létrehozása lemezkép észlelési algoritmust használ. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optikai karakterfelismerés. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | A Maps kimenet a komplex típus (a többrészes adattípusúvá, amely a teljes nevet, egy többsoros cím vagy vezetéknév és a egy személyes azonosító kombinációja használható.) |

## <a name="see-also"></a>Lásd még

+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Egyéni ismeretek interface definition](cognitive-search-custom-skill-interface.md)
+ [Oktatóanyag: Bővített indexelés kognitív kereséssel](cognitive-search-tutorial-blob.md)