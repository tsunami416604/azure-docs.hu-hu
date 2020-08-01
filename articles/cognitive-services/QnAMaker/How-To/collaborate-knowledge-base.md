---
title: Együttműködés a Tudásbázisban – QnA Maker
description: QnA Maker lehetővé teszi, hogy több személy is működjenek együtt a Tudásbázisban. Ez a szolgáltatás az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) van ellátva.
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 20eb58f346322cb78bff85af3d6a0d366090763d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446950"
---
# <a name="collaboration-with-authors-and-editors"></a>Együttműködés a szerzőkkel és a szerkesztővel

Az együttműködés a QnA Maker erőforrás szintjén biztosítható, hogy a közreműködő szerepköre alapján korlátozza a közreműködők hozzáférését. További információ a QnA Maker közreműködő hitelesítési [fogalmakról](../Concepts/role-based-access-control.md).

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Szerepköralapú hozzáférés (RBAC) hozzáadása a QnA Maker erőforráshoz

QnA Maker lehetővé teszi, hogy több személy is működjenek együtt az összes Tudásbázisban ugyanabban a QnA Maker erőforrásban. Ez a szolgáltatás az Azure [szerepköralapú Access Control](../../../active-directory/role-based-access-control-configure.md)biztosít.

## <a name="access-at-the-qna-maker-resource-level"></a>Hozzáférés a QnA Maker erőforrás szintjén

QnA Maker szolgáltatásban nem oszthat meg egy adott tudásbázist. Ha részletesebb hozzáférés-vezérlést szeretne, érdemes lehet a tudásbázist különböző QnA Maker erőforrásokon keresztül kiosztania, majd szerepköröket hozzáadnia az egyes erőforrásokhoz.

## <a name="add-role-to-resource"></a>Szerepkör hozzáadása az erőforráshoz

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Felhasználói fiók hozzáadása a QnA Maker erőforráshoz

A következő lépések a közreműködő szerepkört használják, de a [szerepkörök](../reference-role-based-access-control.md) bármelyike felvehető a következő lépések használatával

1. Jelentkezzen be az [Azure](https://portal.azure.com/) Portalra, és lépjen a QnA Maker-erőforráshoz.

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Lépjen a **Access Control (iam)** lapra.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Válassza a **Hozzáadás** elemet.

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

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Képernyőkép a Tudásbázisban a weblap bal felső sarkában, a Tudásbázis neve mellett zárójelben lévő, szerkesztési módban található Tudásbázisban.":::

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis tesztelése](./test-knowledge-base.md)

További információ az együttműködésről:
* [Azure](../../../active-directory/role-based-access-control-configure.md) szerepköralapú hozzáférés-vezérlés
* QnA Maker szerepköralapú hozzáférés-vezérlési [fogalmak](../Concepts/role-based-access-control.md)
