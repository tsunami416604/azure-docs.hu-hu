---
title: A kifejezés sorolja fel, entitás észlelési növelése érdekében
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) használja, amely javítja az észlelésük vagy szándékok és entitások előrejelzését funkciók hozzáadása, a kategóriák és minták
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 32ef8ba2f6416e1b59fc98595f1b204e94bd2ead
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830990"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Használja a kifejezés boost jelre a word lista sorolja fel

A pontosság javítása érdekében a LUIS-alkalmazás funkciókat adhat hozzá. Szolgáltatások révén a LUIS mutatók azáltal, hogy egyes szavak és kifejezések egy alkalmazás tartomány szókincsből eredőket részét képezik. 

## <a name="add-phrase-list"></a>A kifejezés lista hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **hozhat létre**, majd kattintson a **listák kifejezés** az alkalmazás bal oldali panelen. 

2. Az a **listák kifejezés** kattintson **új lista létrehozása a kifejezés**. 
 
3. Az a **kifejezéslista hozzáadása** párbeszédpanelen írja be a "Város" a kifejezés-lista nevét. Az a **érték** mezőbe írja be a kifejezést lista értékeit. Írjon be egy értéket egy időben, vagy értékek vesszővel elválasztva, és nyomja le az **Enter**.

    ![Lista városok kifejezés hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

4. A LUIS is javasol kapcsolódó értékeket a kifejezés listához való hozzáadásához. Kattintson a **javasoljuk** javasolt értékek a added value(s) szemantikailag kapcsolódó csoport beolvasásához. Kattintson a javasolt értékeket, vagy kattintson **adja hozzá az összes** adja hozzá őket az összes.

    ![A javasolt értékek kifejezéslista](./media/luis-add-features/related-values.png)

5. Kattintson a **ezeket az értékeket felcserélhetők** Ha hozzáadott kifejezés listaértékek alternatívával felcserélhetők.

    ![A javasolt értékek kifejezéslista](./media/luis-add-features/interchangeable.png)

6. Kattintson a **Save** (Mentés) gombra. A "Város" kifejezés helyett szerepel lista adnak hozzá a **listák kifejezés** lap.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Törölheti, vagy a kifejezést a lista a környezetfüggő eszköztár inaktiválása a **listák kifejezés** lapot.

## <a name="pattern-regular-expression-feature"></a>Minta (reguláris kifejezés) szolgáltatás 
**Ez a funkció elavult**. A LUIS nem adható hozzá a minta új funkciókat. Bármely meglévő minta-szolgáltatások 2018. május ig támogatottak. Standard LUIS reguláris kifejezés egy lekéréses kérelmet a megfelelő hozzájárulnak a [felismerő szöveges Github-adattár](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>További lépések

Utána hozzáadása, szerkesztése, törlése vagy kifejezés listáját inaktiválása [betanítása és az alkalmazás tesztelése](luis-interactive-test.md) ismételt használatával ellenőrizheti, ha növeli a teljesítményt.
