---
title: Együttműködés a tudásbázison - QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker lehetővé teszi, hogy több felhasználó együttműködjön egy tudásbázison. Ez a funkció az Azure szerepköralapú hozzáférés-vezérlés.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660749"
---
# <a name="collaborate-on-your-knowledge-base"></a>Közös munka a tudásbázison

A QnA Maker lehetővé teszi, hogy több felhasználó együttműködjön az összes tudásbázison ugyanazon a QnA Maker erőforráson. Ez a funkció az Azure [szerepköralapú hozzáférés-vezérléssel](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)érhető el.

Hajtsa végre az alábbi lépéseket a QnA Maker szolgáltatás megosztásához valakivel:

1. Jelentkezzen be az Azure Portalon, és nyissa meg a QnA Maker erőforrást.

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Nyissa meg a **Hozzáférés-vezérlés (IAM)** lapot.

    ![QnA Készítő IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Válassza a **Hozzáadás** lehetőséget.

    ![QnA Maker IAM hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Válassza ki a **Tulajdonos** vagy a **Közreműködő szerepkört.** A szerepköralapú hozzáférés-vezérléssel nem lehet írásvédett hozzáférést biztosítani. A tulajdonosi és közreműködői szerepkörök olvasási és írási hozzáférési engedéllyel rendelkeznek a QnA Maker szolgáltatáshoz.

    ![QnA Maker IAM szerepkör hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Írja be a felhasználó e-mail címét, és nyomja **le a Mentés gombot.**

    ![QnA Maker IAM e-mail hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Amikor a személy, akkor megosztotta a QnA Maker szolgáltatás, bejelentkezik a [QnA Maker portálon](https://qnamaker.ai) láthatja az összes tudásbázisok az adott szolgáltatásban.

Ne feledje, hogy egy adott tudásbázis t nem oszthat meg egy QnA Maker szolgáltatásban. Ha részletesebb hozzáférés-vezérlést szeretne, fontolja meg a tudásbázisok terjesztését a különböző QnA Maker-szolgáltatások között.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis tesztelése](./test-knowledge-base.md)
