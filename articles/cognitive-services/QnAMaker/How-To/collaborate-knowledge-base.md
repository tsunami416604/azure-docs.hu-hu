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
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660749"
---
# <a name="collaborate-on-your-knowledge-base"></a>Együttműködés a Tudásbázisban

QnA Maker lehetővé teszi, hogy több személy is működjenek együtt az összes Tudásbázisban ugyanabban a QnA Maker erőforrásban. Ez a szolgáltatás az Azure [szerepköralapú Access Control](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)biztosít.

A következő lépések végrehajtásával ossza meg QnA Maker szolgáltatását valakivel:

1. Jelentkezzen be a Azure Portalba, és lépjen a QnA Maker-erőforráshoz.

    ![QnA Maker erőforráslista](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Lépjen a **Access Control (iam)** lapra.

    ![QnA Maker IAM](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Válassza a **Hozzáadás** lehetőséget.

    ![QnA Maker IAM Hozzáadás](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Válassza ki a **tulajdonost** vagy a **közreműködő** szerepkört. Szerepköralapú Access Controlon keresztül nem engedélyezhető írásvédett hozzáférés. A tulajdonosi és közreműködői szerepkörök írási és olvasási hozzáférési engedéllyel rendelkeznek a QnA Maker szolgáltatáshoz.

    ![QnA Maker szerepkör hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Adja meg a felhasználó e-mail-címét, és kattintson a **Mentés**gombra.

    ![QnA Maker e-mail-cím hozzáadása](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Ha az a személy, akivel megosztotta a QnA Maker szolgáltatást a szolgáltatással, bejelentkezik a [QnA Maker portálra](https://qnamaker.ai) , és láthatja a szolgáltatás összes tudásbázisát.

Ne feledje, hogy nem oszthat meg egy adott tudásbázist egy QnA Maker szolgáltatásban. Ha részletesebb hozzáférés-vezérlést szeretne, érdemes lehet a tudásbázist különböző QnA Maker szolgáltatásokban terjeszteni.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis tesztelése](./test-knowledge-base.md)
