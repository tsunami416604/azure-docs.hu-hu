---
title: 'Rövid útmutató: Létrehozásához egy KB-os – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Létrehozhat saját tartalmak, például – gyakori kérdések vagy kézikönyvek QnA Maker Tudásbázis (KB). A QnA Maker KB ebben a példában egy egyszerű – gyakori kérdések weblap kérdésre, a BitLocker-helyreállítás jön létre.
author: nitinme
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 2b8573da6cc87af39a7681fa369940fa7a0b8eb3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44345233"
---
# <a name="create-train-and-publish-your-knowledge-base"></a>Létrehozását, betanítását és közzéteheti a tudásbázist

Létrehozhat saját tartalmak, például – gyakori kérdések vagy kézikönyvek QnA Maker Tudásbázis (KB). A QnA Maker KB ebben a példában egy egyszerű – gyakori kérdések weblap kérdésre, a BitLocker-helyreállítás jön létre.

## <a name="prerequisite"></a>Előfeltétel

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-qna-maker-knowledge-base"></a>A QnA Maker Tudásbázis létrehozása

1. Jelentkezzen be Azure hitelesítő adataival QnAMaker.ai.

2. A QnA Maker webhelyen válassza ki a **Tudásbázis létrehozása**.

   ![Hozzon létre új KB](../media/qna-maker-create-kb.png)

3. Az a **létrehozás** lapon jelölje be 1. lépésben **hozzon létre egy kérdés-válasz szolgáltatás**. A rendszer átirányítja a [az Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) állíthatja be a QnA Maker szolgáltatást az előfizetésében. Ha az Azure Portalon időkorlátja lejár, válassza ki a **próbálja meg újra** a webhelyen. Miután csatlakozott, megjelenik az Azure-irányítópulton.

4. Miután sikeresen létrehozott egy új QnA Maker szolgáltatást az Azure-ban, térjen vissza qnamaker.ai/create. Válassza ki a kérdések és válaszok szolgáltatást a 2. lépésben a legördülő listából. Ha egy új kérdések és válaszok szolgáltatás hozta létre, ügyeljen arra, frissítse az oldalt.

   ![Válassza ki a kérdések és válaszok szolgáltatást KB](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. A 3. lépésben, nevezze el a KB-os **saját minta QnA KB**.

6. Tartalom hozzáadása a KB, válassza a három típusú adatforrásokat. A 4. lépésben a **töltse fel a KB-os**, adja hozzá a [a BitLocker helyreállítási – gyakori kérdések](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL-címet a **URL-cím** mezőbe.

   ![Válassza ki a kérdések és válaszok szolgáltatást KB](../media/qnamaker-quickstart-kb/add-datasources.png)

7. 5. lépésben pedig válassza ki a **létrehozása a KB-os**.

8. A KB létrehozása folyamatban van, amíg egy előugró ablak jelenik meg. A kinyerési folyamat olvassa el a HTML-oldalt, és azonosíthatja a kérdések és válaszok néhány percet vesz igénybe.

9. Ha a KB sikeresen létrejött, a **Tudásbázis** lap megnyitásakor. Ezen az oldalon a Tudásbáziscikk tartalmát szerkesztheti.

10. Kattintson a jobb felső sarokban, válassza ki a **hozzáadása kérdés-válasz párt** hozzáadása egy új sort a **Szerkesztői** a KB szakaszában. A **kérdés**, adja meg **nagy.** A **válasz**, adja meg **Hello. A bitlocker kérdéseket fel.**

   ![A QnA pár hozzáadása](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. Kattintson a jobb felső sarokban, válassza ki a **mentéséhez és a vonat** a módosítások mentéséhez és a QnA Maker modell betanításához. Módosítások nem őrzi meg, ha a mentést.

   ![Mentés és train](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. Kattintson a jobb felső sarokban, válassza ki a **tesztelése** tesztelése, hogy a végzett módosítások érvénybe tartott-e. Adjon meg **üdv mindenkinek van** a box és a select adjon meg. Megjelenik a létrehozott választ adott válaszként.

13. Válassza ki **vizsgálat** megvizsgálhatja a válasz részletesebben. A Teszt ablak segítségével tesztelheti a módosításokat a KB, mielőtt azok van közzétéve.

   ![Teszt Panel](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Válassza ki **teszt** újra gombra kattintva zárja be a **teszt** előugró.

15. A menüben a **szerkesztése**válassza **közzététel**. Erősítse meg, majd válassza ki a **közzététel** az oldalon.

16. Most már sikeresen közzétette a QnA Maker szolgáltatást. A végpont az alkalmazás vagy robot kódot is használhat.

   ![Közzététel](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](../How-To/create-knowledge-base.md)
