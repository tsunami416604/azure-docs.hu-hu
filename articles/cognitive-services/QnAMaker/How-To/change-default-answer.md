---
title: Alapértelmezett válasz kérése - QnA Maker
description: Az alapértelmezett választ akkor adja vissza a rendszer, ha nincs egyezés a kérdéssel. Előfordulhat, hogy módosítani szeretné az alapértelmezett választ a szokásos alapértelmezett válaszból.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843276"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Alapértelmezett válasz beállítása tudásbázishoz

Az alapértelmezett választ akkor adja vissza a rendszer, ha nincs egyezés a kérdéssel. Előfordulhat, hogy módosítani szeretné az alapértelmezett választ a szokásos alapértelmezett válaszból.

## <a name="change-default-answer"></a>Alapértelmezett válasz módosítása

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com) és keresse meg a létrehozott QnA Maker-szolgáltatást képviselő erőforráscsoportot.

2. Kattintson ide az **App Service megnyitásához.**

    ![Az Azure Portalon a QnA Maker alkalmazásszolgáltatásának elérése](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson **az Alkalmazásbeállítások gombra,** és a **DefaultAnswer** mezőt a kívánt alapértelmezett válasszal szerkesztheti. Kattintson a **Mentés** gombra.

    ![Válassza az Alkalmazásbeállítások lehetőséget, majd a DefaultAnswer for QnA Maker szerkesztése](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Az alkalmazásszolgáltatás újraindítása

    ![A DefaultAnswer módosítása után indítsa újra a QnA Maker alkalmazásszolgáltatást](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>További lépések

* [Hozzon létre egy robot ot a QnA Maker és a LUIS segítségével](../tutorials/integrate-qnamaker-luis.md)