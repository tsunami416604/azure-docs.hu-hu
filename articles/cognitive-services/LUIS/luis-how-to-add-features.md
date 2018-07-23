---
title: Funkciók hozzáadása a LUIS-alkalmazások |} A Microsoft Docs
description: Language Understanding (LUIS) használja, amely javítja az észlelésük vagy szándékok és entitások előrejelzését funkciók hozzáadása, a kategóriák és minták
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: v-geberr
ms.openlocfilehash: 3b225780240d772564e55d3bfa4b85eec2a20963
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170735"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>A LUIS-alkalmazás a teljesítmény javítása szolgáltatások használata  

A pontosság javítása érdekében a LUIS-alkalmazás funkciókat adhat hozzá. Szolgáltatások révén a LUIS mutatók azáltal, hogy egyes szavak és kifejezések kategória részét képezik. A LUIS megtanulja, hogyan ismerhetik fel a kategória egy tagot, ha azt is kezelheti a többi hasonló módon.

## <a name="add-phrase-list"></a>A kifejezés lista hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **hozhat létre**, majd kattintson a **listák kifejezés** az alkalmazás bal oldali panelen. 

    ![A kifejezés lista navigáció](./media/luis-add-features/phrase-list-nav.png)

2. Az a **listák kifejezés** kattintson **új lista létrehozása a kifejezés**. 
 
    ![Hozzon létre új kifejezéslista](./media/luis-add-features/create-new-phrase-list.png)
    
3. Az a **kifejezéslista hozzáadása** párbeszédpanelen írja be a "Város" a kifejezés-lista nevét. Az a **érték** mezőbe írja be a kifejezést lista értékeit. Írjon be egy értéket egy időben, vagy értékek vesszővel elválasztva, és nyomja le az **Enter**.

    ![Lista városok kifejezés hozzáadása](./media/luis-add-features/add-phrase-list-cities.png)

4. A LUIS is javasol kapcsolódó értékeket a kifejezés listához való hozzáadásához. Kattintson a **javasoljuk** javasolt értékek a added value(s) szemantikailag kapcsolódó csoport beolvasásához. Kattintson a javasolt értékeket, vagy kattintson **adja hozzá az összes** adja hozzá őket az összes.

    ![A javasolt értékek kifejezéslista](./media/luis-add-features/related-values.png)

5. Kattintson a **ezeket az értékeket felcserélhetők** Ha hozzáadott kifejezés listaértékek alternatívával felcserélhetők.

    ![A javasolt értékek kifejezéslista](./media/luis-add-features/interchangeable.png)

6. Kattintson a **Save** (Mentés) gombra. A "Város" kifejezés helyett szerepel lista adnak hozzá a **listák kifejezés** lap.

    ![Hozzáadott kifejezéslista](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Kifejezés szerkesztése

Kattintson a kifejezés lista neve az a **listák kifejezés** lap. Az a **kifejezéslista szerkesztése** párbeszédpanel megnyílik, hogy minden szükséges szerkesztése a módosításokat, majd kattintson **mentése**.

 ![Hozzáadott kifejezéslista](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Kifejezéslista törlése 

Kattintson a három pont (***...*** ) gombra a sor végén található, és válassza **törlése**.

 ![Hozzáadott tartalom törlése](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Kifejezéslista inaktiválása 

Kattintson a három pont (***...*** ) gombra a sor végén található, és válassza **inaktiválás**.

 ![Hozzáadott lista inaktiválása](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Minta (reguláris kifejezés) szolgáltatás 
**Ez a funkció elavult**. A LUIS nem adható hozzá a minta új funkciókat. Bármely meglévő minta-szolgáltatások 2018. május ig támogatottak. Standard LUIS reguláris kifejezés egy lekéréses kérelmet a megfelelő hozzájárulnak a [felismerő szöveges Github-adattár](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>További lépések

Utána hozzáadása, szerkesztése, törlése vagy kifejezés listáját inaktiválása [betanítása és az alkalmazás tesztelése](luis-interactive-test.md) ismételt használatával ellenőrizheti, ha növeli a teljesítményt.
