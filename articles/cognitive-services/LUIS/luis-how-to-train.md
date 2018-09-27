---
title: A LUIS-verzió betanítása
titleSuffix: Azure Cognitive Services
description: Képzési az a folyamat, a Language Understanding (LUIS) Alkalmazásverzió javítása a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: f27716cc416b162a5b2df5542d709058f3b3e903
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182036"
---
# <a name="train-your-luis-app-version"></a>A LUIS Alkalmazásverzió betanítása

Képzési az a folyamat, a Language Understanding (LUIS) alkalmazásával a beszédfelismerés oktatási. A LUIS-alkalmazás betanításához például hozzáadása, szerkesztése, címkézés vagy entitásokat, a leképezések és a kimondott szöveg törlése a modellhez való frissítés után. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Képzés és [tesztelés](luis-concept-test.md) az alkalmazás az iteratív folyamat. Miután a LUIS-alkalmazás betanításához, tesztelje azt az annak ellenőrzéséhez, hogy a szándékok és entitások helyesen ismeri a minta kimondott szöveg. Ha nem, a LUIS-alkalmazásokon, tanítási és tesztelési ellenőrizze újra frissítéseket. 

## <a name="how-to-train"></a>Hogyan betanítása
A iteratív folyamat elindításához először a LUIS-alkalmazás során legalább egyszer betanításához. Ellenőrizze, hogy minden szándékot képzési előtt legalább egy utterance (kifejezés).

1. Az alkalmazás eléréséhez annak nevét választva a **saját alkalmazások** lapot. 

2. Jelölje be az alkalmazásba, **Train** a az ablak tetején. 

3. Képzési befejeződése után a böngésző tetején egy zöld értesítési sáv jelenik meg.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Ha egy vagy több leképezések az alkalmazás, amely nem tartalmaz példa utterances, az alkalmazás nem betanításához. Az összes a leképezések beszédmódok hozzáadása. További információkért lásd: [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Az összes adat betanítása
Képzési egy kis csoportja negatív mintavételt használ. Ha szeretné az összes adat használata helyett a kisméretű negatív mintavételt, használja a [verzió beállítások API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) az a `UseAllTrainingData` Ez a funkció kikapcsolásához igaz értékre állítva. 

## <a name="next-steps"></a>További lépések

* [Az intelligens hangfelismerési szolgáltatással javasolt utterances felirat](luis-how-to-review-endoint-utt.md) 
* [A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata](luis-how-to-add-features.md) 