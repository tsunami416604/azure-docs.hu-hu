---
title: A LUIS-alkalmazás – Azure betanítása |} A Microsoft Docs
description: Language Understanding (LUIS) használja a modell betanításához.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: diberry
ms.openlocfilehash: e947df20141b0b9870f318f410488aea23bafcf5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223184"
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

    ![Betanítás gomb](./media/luis-how-to-train/train-button.png)

3. Képzési befejeződése után a böngésző tetején egy zöld értesítési sáv jelenik meg.

    ![Tanítási és tesztelési lap](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Ha egy vagy több leképezések az alkalmazás, amely nem tartalmaz példa utterances, az alkalmazás nem betanításához. Az összes a leképezések beszédmódok hozzáadása. További információkért lásd: [példa beszédmódok hozzáadása](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>További lépések

* [Az intelligens hangfelismerési szolgáltatással javasolt utterances felirat](luis-how-to-review-endoint-utt.md) 
* [A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata](luis-how-to-add-features.md) 