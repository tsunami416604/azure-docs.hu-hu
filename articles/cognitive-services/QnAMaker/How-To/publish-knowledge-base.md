---
title: A Tudásbázis - Microsoft kognitív szolgáltatások közzététele |} Microsoft Docs
titleSuffix: Azure
description: A Tudásbázis közzététele
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348587"
---
# <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

Utolsó lépésként elérhetővé teszi a Tudásbázis kérdés megválaszolásával végpontként közzététele a Tudásbázis. 

A Tudásbázis közzétételekor a Tudásbázis kérdések és válaszok tartalmát a teszt indexből egy éles index az Azure search helyezi.

![Termék indexe közzététele](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Tudásbázis közzététele

1. Ha végzett a KB-ban a változtatásokat, válassza ki a **közzététel** a felső navigációs sávban. Az Azure Search közzéteheti a meghatározott időn belül Tudásbázis körrel pótolni. 

    ![Tudásbázis közzététele](../media/qnamaker-how-to-publish-kb/publish.png)

2. Válassza ki **közzététel** ismételt használatával ellenőrizheti a végpont adatait az alkalmazás vagy a kód használható.

    ![Tudásbázis közzététele](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Tudásbázis analytics letöltése](./get-analytics-knowledge-base.md)
