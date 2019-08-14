---
title: Tudásbázis létrehozása – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker API Service Portal használatával hozzáadhat egy tudásbázist a Chit-Chat segítségével. Így az alkalmazás bevonása zajlik. Adja hozzá a Top Chit-Chat előre feltöltött készletét a KB-hoz, amely kiindulási pontként szolgál a robot Chit-csevegéséhez, és megtakarítja az időt és a költségeket a semmiből való íráshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b2cacc8cc6d7c22a93f46007e8150a4e55dc7650
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967716"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Gyors útmutató: Tudásbázis létrehozása a QnA Maker API Service Portal használatával

A QnA Maker API Service Portal használatával egyszerűen hozzáadhatja meglévő adatforrásait Tudásbázis létrehozásakor. A QnA Maker új Tudásbázis hozhat létre a következő dokumentum-típusok közül:

<!-- added for scanability -->
* Gyakori kérdéseket tartalmazó oldalak
* Termékek kézikönyvek
* Strukturált dokumentumok

Egy Chit-Chat-személyiséggel is elvégezheti a tudását, így jobban megtarthatja a felhasználókat.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="create-a-new-knowledge-base"></a>Új tudásbázis létrehozása

1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai) az Azure hitelesítő adatait, és válassza a **Tudásbázis létrehozása**.

1. Ha még nem hozott létre a QnA Maker szolgáltatást, válassza ki a **hozzon létre egy kérdés-válasz szolgáltatás**. 

1. Válassza ki az Azure-bérlőhöz, Azure-előfizetés nevét és a QnA Maker szolgáltatást a listákból társított Azure-erőforrás neve **2. lépés** a QnA Maker Portal. Válassza ki az Azure QnA Maker szolgáltatást, amely üzemelteti a Tudásbázis.

    ![Kérdések és válaszok szolgáltatás beállítása](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Adja meg az új Tudásbázis a tudásbázist és az adatforrás nevét.

    ![Set-adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Adja meg a szolgáltatás egy **nevét.** Ismétlődő nevek és speciális karakterek támogatottak.
    - Adja hozzá az URL-címek kinyert adatok számára. További információ a támogatott adatforrásokról típusú [Itt](../Concepts/data-sources-supported.md).
    - A kívánt kinyert adatokat tölt fel. Tekintse meg a [díjszabási információk](https://aka.ms/qnamaker-pricing) megtekintéséhez, hogy hány dokumentumok adhat hozzá.
    - Ha szeretné manuálisan adja hozzá a QnA-tudásbázisok, akkor kihagyhatja **4. lépés** az előző képen látható.

1. Adjon hozzá **Chit csevegési** a KB. Válassza ki az egyik személyiségét, és adja hozzá a robot-csevegés támogatását. 

    ![Csevegés hozzáadása KB-hoz](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Válassza ki **létrehozása a KB-os**.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Az adatok kinyerhetők néhány percet vesz igénybe.

    ![Kinyerés](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. A Tudásbázis sikeres létrehozása után a rendszer átirányítja a **Tudásbázis** lapot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a tudásbázissal, távolítsa el a QnA Maker portálon.

## <a name="next-steps"></a>További lépések

A költségmegtakarítási mértékek esetében megoszthat néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást. [](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker)

> [!div class="nextstepaction"]
> [Csevegési chit személyes hozzáadása](./chit-chat-knowledge-base.md)
