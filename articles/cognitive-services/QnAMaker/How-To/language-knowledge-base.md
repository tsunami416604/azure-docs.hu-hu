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
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876624"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>A QnA Maker Tudásbázis-tartalmának nyelvi támogatása

A QnA Maker számos nyelven támogatja a Tudásbázis-tartalmakat. Az egyes QnA Maker szolgáltatásokat azonban egyetlen nyelvre kell lefoglalni. Egy adott QnA Maker-szolgáltatást célzó első Tudásbázis az adott szolgáltatás nyelvét állítja be. A támogatott nyelvek listáját [itt](../Overview/languages-supported.md) találja.

A rendszer automatikusan felismeri a nyelvet a kinyert adatforrások tartalmából. Miután létrehozta az új QnA Maker szolgáltatást és egy új tudásbázist a szolgáltatásban, ellenőrizheti, hogy a nyelv helyesen van-e beállítva.

1. Lépjen az [Azure Portalra](https://portal.azure.com/).

1. Válassza az **erőforráscsoportok** lehetőséget, és navigáljon ahhoz az erőforráscsoporthoz, ahol a QnA Maker szolgáltatás telepítve van, és válassza ki a **Azure Search** erőforrást.

    ![Azure Search erőforrás kiválasztása](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Válassza ki a **testkb** indexet. Ez a Azure Search index mindig az első, és a szolgáltatásban található összes Tudásbázis mentett tartalmát tartalmazza. 

    ![Válassza ki a teszt KB-ot](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. Válassza ki a **mezők** szakaszt a _testkb_ részleteinek megjelenítéséhez.

    ![Válasszon mezőket](../media/qnamaker-how-to-language-kb/selectfields.png)

1. Jelölje be az **analizátor** jelölőnégyzetét a nyelvi adatok megtekintéséhez.

    ![Elemző kiválasztása](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. Meg kell állapítania, hogy az _analizátor_ egy adott nyelvre van beállítva. Ezt a nyelvet a rendszer automatikusan észlelte a Tudásbázis létrehozási lépése során az importált fájlokból és URL-címekből. Az erőforrás létrehozása után ez a nyelv nem módosítható.

    ![Kiválasztott elemző](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA-robot létrehozása Azure Bot Service](../Tutorials/create-qna-bot.md)
