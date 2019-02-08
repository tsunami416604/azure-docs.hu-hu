---
title: Együttműködés a Tudásbázis – Qna Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker lehetővé teszi, hogy több személy közösen dolgozzon a Tudásbázis. Ez a funkció az Azure szerepköralapú hozzáférés-vezérlés az van megadva.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857039"
---
# <a name="collaborate-on-your-knowledge-base"></a>A tudásbázist másokkal közös használatához

A QnA Maker lehetővé teszi, hogy több személy közösen dolgozzon a Tudásbázis. Ez a funkció az Azure-ral biztosított [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure). 

A következő lépésekkel másokkal a QnA Maker szolgáltatást:

1. Jelentkezzen be az Azure Portalra, és a QnA Maker erőforrás megnyitása.

    ![Rendszererőforrás-lista a QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Nyissa meg a **hozzáférés-vezérlés (IAM)** fülre.

    ![A QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Válassza a **Hozzáadás** lehetőséget.

    ![A QnA Maker IAM hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Válassza ki a **tulajdonosa** vagy a **közreműködői** szerepkör. Csak olvasható hozzáférést szerepköralapú hozzáférés-vezérlés használatával nem tudja biztosítani. Tulajdonos és közreműködő szerepkörrel rendelkezik olvasási és írási hozzáférése közvetlenül a QnA Maker szolgáltatást.

    ![A QnA Maker IAM-szerepkör hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Adja meg a kívánt megosztásához, és kattintson a Mentés, e-mailre.

    ![A QnA Maker IAM e-mail hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Most már a személynek a QnA Maker szolgáltatást a megosztott, amikor bejelentkezik a [QnA Maker portal](https://qnamaker.ai) meghatározhatják, hogy a szolgáltatás tudásbázisok.

Ne feledje, hogy a QnA Maker szolgáltatást az adott Tudásbázis nem oszthat meg. Ha azt szeretné, hogy a részletes hozzáférés-vezérlés, fontolja meg a tudásbázisok elosztása a különböző QnA Maker szolgáltatás.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis tesztelése](./test-knowledge-base.md)
