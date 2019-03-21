---
title: Tudásbázis közzététele
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Az utolsó lépés egy kérdés-válasz végpontként elérhetővé a Tudásbázis közzététele a QnA Maker API szolgáltatással. Tudásbázis közzététele, amikor a kérdések és válaszok tartalmát a Tudásbázis áthelyezi a teszt indexből egy éles indexet az Azure Search szolgáltatásban.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091953"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>A QnA Maker API-portál használatával Tudásbázis közzététele

Az utolsó lépés egy kérdés-válasz végpontként elérhetővé a Tudásbázis közzététele a QnA Maker API szolgáltatással. 

Tudásbázis közzététele, amikor a Tudásbázis kérdés és válasz tartalma áthelyezi a teszt indexből egy éles indexet az Azure Search szolgáltatásban.

![Éles indexe közzététele](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

1. Ha végzett a módosítással a KB-ban, válassza ki a **közzététel** a felső navigációs sávban. Az Azure Search a meghatározott időn belül számú tudásbázisok közzétehet. 

    ![Tudásbázis közzététele](../media/qnamaker-how-to-publish-kb/publish.png)

2. Válassza ki **közzététel** ismételt használatával ellenőrizheti a végpont adatait az alkalmazás vagy robot kódjában használható.

    ![Tudásbázis közzététele sikerült](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor elkészült, a Tudásbázis segítségével, távolítsa el a QnA Maker portálra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis analytics beszerzése](./get-analytics-knowledge-base.md)
