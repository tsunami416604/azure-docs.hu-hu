---
title: Az LUIS alkalmazás - Azure betanítása |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) segítségével a modell betanításához.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 4593954e9e0a60beaa5ee86df848f908b23c6b20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349571"
---
# <a name="train-your-luis-app"></a>A LUIS app képzése

Képzési a nyelvi ismertetése (LUIS) alkalmazás a természetes nyelvű megértése oktatási során a rendszer. A LUIS app betanítása hozzáadása, szerkesztése, címkézés vagy törlése entitások, leképezések vagy utterances például a modellbe való frissítés után. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Képzési és [tesztelés](luis-concept-test.md) egy alkalmazás áll iteratív folyamat. Miután az LUIS alkalmazás betanításához tesztelni azt a minta utterances megjelenítéséhez, ha a leképezések és entitások helyesen ismeri. Ha még nem, a LUIS app, tanítási és tesztelési végezze újra frissítéseket. 

## <a name="train-your-app"></a>Az alkalmazás képzése
A iteratív folyamat elindításához először az LUIS alkalmazást legalább egyszer képzése érdekében. Ellenőrizze, hogy minden leképezés képzési előtt legalább egy utterance.

1. Az alkalmazás eléréséhez választva módosíthatja a nevet a **saját alkalmazások** lap. 

2. Válassza ki az alkalmazás **vonat** a az ablak tetején. 

    ![Vonat gomb](./media/luis-how-to-train/train-button.png)

3. Ha képzési befejeződött, egy zöld értesítési sáv jelenik meg a böngészőablak.

    ![Tanítási és tesztelési App lapon](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Ha egy vagy több leképezések az alkalmazás például utterances nem tartalmazó, akkor az alkalmazás nem betanításához. Adja hozzá a leképezések az utterances. További információkért lásd: [adja hozzá például utterances](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>További lépések

* [Javasolt utterances LUIS rendelkező címke](Label-Suggested-Utterances.md) 
* [A szolgáltatások használata a LUIS alkalmazás teljesítmény javítása érdekében](luis-how-to-add-features.md) 