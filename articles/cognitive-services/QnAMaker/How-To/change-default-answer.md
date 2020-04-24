---
title: Alapértelmezett válasz beolvasása – QnA Maker
description: A rendszer az alapértelmezett választ adja vissza, ha nincs egyezés a kérdésben. Érdemes lehet módosítani az alapértelmezett választ a normál alapértelmezett válasz alapján.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097098"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker erőforrás alapértelmezett válaszának módosítása

A rendszer az alapértelmezett választ adja vissza, ha nincs egyezés a kérdésben. Érdemes lehet módosítani az alapértelmezett választ a normál alapértelmezett válasz alapján.

## <a name="change-default-answer-in-the-azure-portal"></a>Az alapértelmezett válasz módosítása a Azure Portalban

1. Lépjen a [Azure Portal](https://portal.azure.com) , és navigáljon a létrehozott QnA Maker szolgáltatást jelölő erőforráscsoporthoz.

2. Kattintson ide a **app Service**megnyitásához.

    ![A Azure Portal az App Service for QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Kattintson az **Alkalmazásbeállítások** elemre, és szerkessze a **DefaultAnswer** mezőt a kívánt alapértelmezett válaszra. Kattintson a **Save** (Mentés) gombra.

    ![Válassza ki az Alkalmazásbeállítások elemet, majd szerkessze a DefaultAnswer QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Indítsa újra az App Service-t

    ![A DefaultAnswer módosítása után indítsa újra a QnA Maker appservice](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>További lépések

* [Tudásbázis létrehozása](../How-to/manage-knowledge-bases.md)