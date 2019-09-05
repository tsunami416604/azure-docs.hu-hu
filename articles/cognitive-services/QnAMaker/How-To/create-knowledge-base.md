---
title: 'Gyors útmutató: Tudásbázis létrehozása – QnA Maker'
titleSuffix: Azure Cognitive Services
description: A QnA Maker API Service Portal használatával hozzáadhat egy tudásbázist a Chit-Chat segítségével. Így az alkalmazás bevonása zajlik. Adja hozzá a Top Chit-Chat előre feltöltött készletét a KB-hoz, amely kiindulási pontként szolgál a robot Chit-csevegéséhez, és megtakarítja az időt és a költségeket a semmiből való íráshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 798bbb725d4764c5ec7a1d69770e9508af0fdf5a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376214"
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

1. Adja meg a szolgáltatás **nevét** , `my first kb`például:. Ismétlődő nevek és speciális karakterek támogatottak.

1. Adja hozzá a QnA Maker hibaelhárítási oldalt URL- `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`címként:, `+ Add URL`majd válassza a elemet. További információ a támogatott adatforrásokról típusú [Itt](../Concepts/data-sources-supported.md). Ebben a rövid útmutatóban **ne töltsön fel fájlokat** a kinyerni kívánt adatokra. Tekintse meg a [díjszabási információk](https://aka.ms/qnamaker-pricing) megtekintéséhez, hogy hány dokumentumok adhat hozzá.

1. Vegyen fel  **_professzionális_ Chit-csevegést** a kb-ra. 

1. Válassza ki **létrehozása a KB-os**.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Az adat kinyerése néhány percet is igénybe vehet.

    ![Kinyerés](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. A Tudásbázis sikeres létrehozása után a rendszer átirányítja a **Tudásbázis** oldalára.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a tudásbázissal, távolítsa el a QnA Maker portálon.

## <a name="next-steps"></a>További lépések

A költségmegtakarítási mértékek esetében [megoszthat](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker) néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást.

> [!div class="nextstepaction"]
> [Metaadatokkal kapcsolatos kérdések hozzáadása](../quickstarts/add-question-metadata-portal.md)
