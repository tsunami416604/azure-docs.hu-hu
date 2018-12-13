---
title: Beépített adatkinyerés, természetes nyelv és képfeldolgozás – Azure Search
description: Adatok kinyerése, természetes nyelv és kognitív képességeket képfeldolgozó szemantika és a struktúra hozzá nyers tartalom az Azure Search-folyamatban.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bc1353ffb4514622ce0ef6e5c3ced76adc7f999f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314788"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>A tartalom Adatbővítés (Azure Search) előre megadott képesség

Ebben a cikkben megismerkedhet az Azure Search szolgáltatással a megadott, a kognitív képességeket. A *cognitive szakértelem* egy művelet, amely valamilyen módon tartalom alakítja át. Gyakran nem egy összetevő, amely kinyeri az adatokat vagy kikövetkezteti a struktúrát, és ezért úgy bővíti a bemeneti adatok az ismertetése. Szinte mindig kimenete szöveges. A *indexmezők* gyűjteménye, amelyek meghatározzák a Adatbővítés folyamat képességek. 

> [!NOTE]
> December 21, 2018-as, lesz egy Cognitive Services-erőforrás társítása egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon is megkezdjük a dokumentumfeltörést fázis részeként a lemezkép kinyerési díjszabási. A szövegek dokumentumokból való kinyerése továbbra is ingyenesen használható.
>
> A végrehajtás beépített képességek díjat számítunk fel a meglévő [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services/) . Kép kinyerési díjszabás az előzetes verziók díjszabása díjat számítunk fel, és a leírt a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Ismerje meg, [további](cognitive-search-attach-cognitive-services.md).

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