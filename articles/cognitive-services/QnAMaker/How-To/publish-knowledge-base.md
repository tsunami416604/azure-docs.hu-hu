---
title: Tudásbázis közzététele
titleSuffix: QnA Maker - Azure Cognitive Services
description: Az utolsó lépés a kérdés megválaszolásával-végpontként elérhetővé a Tudásbázis közzététele. Tudásbázis közzététele, amikor a kérdések és válaszok tartalmát a Tudásbázis áthelyezi a teszt indexből egy éles indexet az Azure Search szolgáltatásban.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: deeebd99425bc1ec1ed2ae76ef4852119a9c10ea
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342442"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>A QnA Maker portal használatával Tudásbázis közzététele

Az utolsó lépés a elérhetővé teszi a Tudásbázis kérdés megválaszolásával-végpontként ügyfélalkalmazás közzététele. 

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
