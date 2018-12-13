---
title: Tudásbázis – QnA Maker közzététele
titleSuffix: Azure Cognitive Services
description: Az utolsó lépés a kérdés megválaszolásával-végpontként elérhetővé a Tudásbázis közzététele. Tudásbázis közzététele, amikor a kérdések és válaszok tartalmát a Tudásbázis áthelyezi a teszt indexből egy éles indexet az Azure Search szolgáltatásban.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9c3d1009742f9ca0df6dc2f2d1a2d1904397275a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087847"
---
# <a name="publish-a-knowledge-base-in-qna-maker"></a>A QnA Maker Tudásbázis közzététele

Az utolsó lépés a kérdés megválaszolásával-végpontként elérhetővé a Tudásbázis közzététele. 

Tudásbázis közzététele, amikor a kérdések és válaszok tartalmát a Tudásbázis áthelyezi a teszt indexből egy éles indexet az Azure Search szolgáltatásban.

![Éles indexe közzététele](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

1. Ha végzett a módosítással a KB-ban, válassza ki a **közzététel** a felső navigációs sávban. Az Azure Search a meghatározott időn belül számú tudásbázisok közzétehet. 

    ![Tudásbázis közzététele](../media/qnamaker-how-to-publish-kb/publish.png)

2. Válassza ki **közzététel** ismételt használatával ellenőrizheti a végpont adatait az alkalmazás vagy robot kódjában használható.

    ![Tudásbázis közzététele sikerült](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis analytics beszerzése](./get-analytics-knowledge-base.md)
