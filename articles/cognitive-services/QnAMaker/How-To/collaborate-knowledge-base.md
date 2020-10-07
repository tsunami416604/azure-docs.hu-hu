---
title: Együttműködés a Tudásbázisban – QnA Maker
description: QnA Maker lehetővé teszi, hogy több személy is működjenek együtt a Tudásbázisban. Ez a szolgáltatás az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) van ellátva.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 8adc02d4b397c2b0bdaf5d3998cfbe229c896d41
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776850"
---
# <a name="collaboration-with-authors-and-editors"></a>Együttműködés a szerzőkkel és a szerkesztővel

Az együttműködés a QnA Maker erőforrás szintjén biztosítható, hogy a közreműködő szerepköre alapján korlátozza a közreműködők hozzáférését. További információ a QnA Maker közreműködő hitelesítési [fogalmakról](../Concepts/role-based-access-control.md).

## <a name="add-azure-role-based-access-control-azure-rbac-to-your-qna-maker-resource"></a>Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) hozzáadása a QnA Maker-erőforráshoz

QnA Maker lehetővé teszi, hogy több személy is működjenek együtt az összes Tudásbázisban ugyanabban a QnA Maker erőforrásban. Ez a szolgáltatás az [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md)van ellátva.

## <a name="access-at-the-qna-maker-resource-level"></a>Hozzáférés a QnA Maker erőforrás szintjén

QnA Maker szolgáltatásban nem oszthat meg egy adott tudásbázist. Ha részletesebb hozzáférés-vezérlést szeretne, érdemes lehet a tudásbázist különböző QnA Maker erőforrásokon keresztül kiosztania, majd szerepköröket hozzáadnia az egyes erőforrásokhoz.

## <a name="add-role-to-resource"></a>Szerepkör hozzáadása az erőforráshoz

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Felhasználói fiók hozzáadása a QnA Maker erőforráshoz

A következő lépések a közreműködő szerepkört használják, de a [szerepkörök](../reference-role-based-access-control.md) bármelyike felvehető a következő lépések használatával

1. Jelentkezzen be az [Azure](https://portal.azure.com/) Portalra, és lépjen a QnA Maker-erőforráshoz.

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Lépjen a **Access Control (iam)** lapra.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Válassza a **Hozzáadás** lehetőséget.

    ![QnA Maker IAM Hozzáadás](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Válasszon ki egy szerepkört a következő listából:

    |Szerepkör|
    |--|
    |Tulajdonos|
    |Közreműködő|
    |Cognitive Services QnA Maker olvasó|
    |Cognitive Services QnA Maker szerkesztő|
    |Cognitive Services felhasználó|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker a szerepkör hozzáadása.":::

1. Adja meg a felhasználó e-mail-címét, és kattintson a **Mentés**gombra.

    ![QnA Maker e-mail-cím hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>QnA Maker tudásbázisok megtekintése

Ha az a személy, akivel a QnA Maker szolgáltatását a [QnA Maker portálra](https://qnamaker.ai)osztotta, az adott szolgáltatás összes tudásbázisát láthatja a szerepkörük alapján.

Ha kijelölnek egy tudásbázist, az adott QnA Maker erőforráson aktuális szerepkörük látható a Tudásbázis neve mellett.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker a szerepkör hozzáadása.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis tesztelése](./test-knowledge-base.md)

További információ az együttműködésről:
* [Azure](../../../active-directory/role-based-access-control-configure.md) szerepköralapú hozzáférés-vezérlés
* QnA Maker szerepköralapú hozzáférés-vezérlési [fogalmak](../Concepts/role-based-access-control.md)
