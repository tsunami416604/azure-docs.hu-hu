---
title: Alapértelmezett válasz beolvasása – QnA Maker
description: A rendszer az alapértelmezett választ adja vissza, ha nincs egyezés a kérdésben. Érdemes lehet módosítani az alapértelmezett választ a normál alapértelmezett válasz alapján.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843276"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>A Tudásbázis alapértelmezett válaszának beállítása

A rendszer az alapértelmezett választ adja vissza, ha nincs egyezés a kérdésben. Érdemes lehet módosítani az alapértelmezett választ a normál alapértelmezett válasz alapján.

## <a name="change-default-answer"></a>Alapértelmezett válasz módosítása

1. Lépjen a [Azure Portal](https://portal.azure.com) , és navigáljon a létrehozott QnA Maker szolgáltatást jelölő erőforráscsoporthoz.

2. Kattintson ide a **app Service**megnyitásához.

    ![A Azure Portal az App Service for QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson az **Alkalmazásbeállítások** elemre, és szerkessze a **DefaultAnswer** mezőt a kívánt alapértelmezett válaszra. Kattintson a **Mentés** gombra.

    ![Válassza ki az Alkalmazásbeállítások elemet, majd szerkessze a DefaultAnswer QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Indítsa újra az App Service-t

    ![A DefaultAnswer módosítása után indítsa újra a QnA Maker appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Következő lépések

* [Robot létrehozása QnA Maker és LUIS között](../tutorials/integrate-qnamaker-luis.md)