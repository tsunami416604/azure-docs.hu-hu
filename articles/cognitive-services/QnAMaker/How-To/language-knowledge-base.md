---
title: Nem angol nyelvű Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker számos nyelven támogatja a Tudásbázis-tartalmakat. Az egyes QnA Maker szolgáltatásokat azonban egyetlen nyelvre kell lefoglalni. Egy adott QnA Maker-szolgáltatást célzó első Tudásbázis az adott szolgáltatás nyelvét állítja be.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961473"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>A QnA Maker Tudásbázis-tartalmának nyelvi támogatása

A QnA Maker számos nyelven támogatja a Tudásbázis-tartalmakat. Az egyes QnA Maker szolgáltatásokat azonban egyetlen nyelvre kell lefoglalni. Egy adott QnA Maker-szolgáltatást célzó első Tudásbázis az adott szolgáltatás nyelvét állítja be. A támogatott nyelvek listáját [itt](../Overview/languages-supported.md) találja.

A rendszer automatikusan felismeri a nyelvet a kinyert adatforrások tartalmából. Miután létrehozta az új QnA Maker szolgáltatást és egy új tudásbázist a szolgáltatásban, ellenőrizheti, hogy a nyelv helyesen van-e beállítva.

1. Lépjen az [Azure Portalra](https://portal.azure.com/).

1. Válassza az **erőforráscsoportok** lehetőséget, és navigáljon ahhoz az erőforráscsoporthoz, ahol a QnA Maker szolgáltatás telepítve van, és válassza ki a **Azure Search** erőforrást.

    ![Azure Search erőforrás kiválasztása](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Válassza az **indexek**lehetőséget, majd válassza ki a **testkb** indexet. Ez az első Azure Search-index, amely tartalmazza az adott szolgáltatás összes tudásbázisának mentett tartalmát. 

1. Válassza ki a **mezőket** az index mezőinek megjelenítéséhez.

1. A `questions` és`answer` a mezők Analyzer oszlopa egy adott nyelvre van beállítva. Ezt a nyelvet a rendszer automatikusan észlelte a Tudásbázis létrehozási lépése során az importált fájlokból és URL-címekből. Az erőforrás létrehozása után ez a nyelv nem módosítható.

    ![Kiválasztott elemző](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA-robot létrehozása Azure Bot Service](../Tutorials/create-qna-bot.md)
