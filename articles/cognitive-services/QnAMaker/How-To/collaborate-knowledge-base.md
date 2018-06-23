---
title: A Tudásbázis - kognitív Microsoft-szolgáltatások a dolgoznak |} Microsoft Docs
titleSuffix: Azure
description: Hogyan való együttműködéshez a kérdések és válaszok készítő Tudásbázis
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: e18d656236276595fc5186a6656349bf28974ead
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348586"
---
# <a name="collaborate-on-your-knowledge-base"></a>A Tudásbázis együttműködéshez

Kérdések és válaszok Maker lehetővé teszi, hogy több személyt, hogy a Tudásbázis másokkal közösen szerkeszthet. Ez a funkció az Azure által biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure). 

A következő lépésekkel másokkal a kérdések és válaszok készítő szolgáltatás:

1. Jelentkezzen be az Azure-portálon, és nyissa meg a kérdések és válaszok készítő erőforráshoz.

    ![Kérdések és válaszok készítő erőforrások listája](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Lépjen a **hozzáférés-vezérlés (IAM)** fülre.

    ![Kérdések és válaszok készítő IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Válassza a **Hozzáadás** lehetőséget.

    ![Kérdések és válaszok készítő IAM hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Válassza ki a **tulajdonos** vagy a **közreműködő** szerepkör.

    ![Kérdések és válaszok készítő IAM-szerepkör hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Adja meg az e-mailt kívánt megosztásához, és kattintson a mentés.

    ![Kérdések és válaszok készítő IAM e-mail hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Most már a személy megosztott a kérdések és válaszok készítő szolgáltatást, amikor bejelentkezik az a [kérdések és válaszok készítő portal](https://qnamaker.ai) láthatják, hogy a szolgáltatás a Tudásbázis körrel.

Ne feledje, hogy egy adott Tudásbázis a kérdések és válaszok készítő szolgáltatásban nem lehet megosztani. Ha azt szeretné, hogy részletesebb hozzáférés-vezérlés, érdemes lehet osztja el a Tudásbázis körrel különböző kérdések és válaszok készítő szolgáltatások között.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis tesztelése](./test-knowledge-base.md)
