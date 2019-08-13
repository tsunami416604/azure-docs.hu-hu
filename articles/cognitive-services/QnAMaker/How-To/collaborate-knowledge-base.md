---
title: Együttműködés a Tudásbázisban – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker lehetővé teszi, hogy több személy is működjenek együtt a Tudásbázisban. Ez a szolgáltatás az Azure szerepköralapú Access Control biztosít.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 9c5398ff7cb31698db3d4a798b6a082f9e74b99b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955137"
---
# <a name="collaborate-on-your-knowledge-base"></a>Együttműködés a Tudásbázisban

QnA Maker lehetővé teszi, hogy több személy is működjenek együtt a Tudásbázisban. Ez a szolgáltatás az Azure [szerepköralapú Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)biztosít. 

A következő lépések végrehajtásával ossza meg QnA Maker szolgáltatását valakivel:

1. Jelentkezzen be a Azure Portalba, és lépjen a QnA Maker-erőforráshoz.

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Lépjen a **Access Control (iam)** lapra.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Válassza a **Hozzáadás** lehetőséget.

    ![QnA Maker IAM Hozzáadás](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Válassza ki a tulajdonost vagy a **közreműködő** szerepkört. Szerepköralapú Access Controlon keresztül nem engedélyezhető írásvédett hozzáférés. A tulajdonos és a közreműködő szerepkör írási és olvasási jogosultsággal rendelkezik QnA Maker szolgáltatáshoz.

    ![QnA Maker szerepkör hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Adja meg a megosztani kívánt e-mail-címet, majd kattintson a Save (Mentés) gombra.

    ![QnA Maker e-mail-cím hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Most, amikor a QnA Maker szolgáltatásával megosztotta a szolgáltatást, bejelentkezik a [QnA Maker portálra](https://qnamaker.ai) , ahol az adott szolgáltatás összes tudásbázisa látható.

Ne feledje, hogy nem oszthat meg egy adott tudásbázist egy QnA Maker szolgáltatásban. Ha részletesebb hozzáférés-vezérlést szeretne, érdemes lehet a tudásbázist különböző QnA Maker szolgáltatásokban terjeszteni.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis tesztelése](./test-knowledge-base.md)
