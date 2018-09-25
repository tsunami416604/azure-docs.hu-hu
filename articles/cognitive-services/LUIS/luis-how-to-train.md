---
title: A LUIS-alkalmazás betanítása
titleSuffix: Azure Cognitive Services
description: Képzési az a folyamat, a Language Understanding (LUIS) alkalmazásával a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6ed76e35ce07f2848c67ef007ad7d3f062f465f7
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036762"
---
# <a name="train-your-luis-app"></a>A LUIS-alkalmazás betanítása

Képzési az a folyamat, a Language Understanding (LUIS) alkalmazásával a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Képzés és [tesztelés](luis-concept-test.md) az alkalmazás az iteratív folyamat. Miután a LUIS-alkalmazás betanításához, tesztelje azt az annak ellenőrzéséhez, hogy a szándékok és entitások helyesen ismeri a minta kimondott szöveg. Ha nem, a LUIS-alkalmazásokon, tanítási és tesztelési ellenőrizze újra frissítéseket. 

## <a name="train-your-app"></a>Az alkalmazás betanítása
A iteratív folyamat elindításához először a LUIS-alkalmazás során legalább egyszer betanításához. Ellenőrizze, hogy minden szándékot képzési előtt legalább egy utterance (kifejezés).

1. Az alkalmazás eléréséhez annak nevét választva a **saját alkalmazások** lapot. 

2. Jelölje be az alkalmazásba, **Train** a az ablak tetején. 

3. Képzési befejeződése után a böngésző tetején egy zöld értesítési sáv jelenik meg.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Ha egy vagy több leképezések az alkalmazás, amely nem tartalmaz példa utterances, az alkalmazás nem betanításához. Az összes a leképezések beszédmódok hozzáadása. További információkért lásd: [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>További lépések

* [Az intelligens hangfelismerési szolgáltatással javasolt utterances felirat](luis-how-to-review-endoint-utt.md) 
* [A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata](luis-how-to-add-features.md) 