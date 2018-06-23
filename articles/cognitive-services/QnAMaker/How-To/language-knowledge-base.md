---
title: A nem angol nyelvű Tudásbázis - kérdések és válaszok készítő - kognitív Azure-szolgáltatások létrehozása |} Microsoft Docs
description: Tudnivalók a nem angol nyelvű Tudásbázis.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348651"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Kérdések és válaszok készítő Tudásbázis tartalom nyelvi támogatás
Kérdések és válaszok készítő Tudásbázis tartalom támogatja a különböző nyelvű. Azonban minden egyes készítő kérdések és válaszok szolgáltatás számára lefoglalt egyetlen nyelvet. Az első Tudásbázis létrehozott célzó egy adott készítő kérdések és válaszok szolgáltatáshoz az adott szolgáltatás nyelvének beállítása. Lásd: [Itt](../Overview/languages-supported.md) támogatott nyelvek listáját.

A nyelvet a rendszer automatikusan felismeri a tartalomról az adatforrások kibontása közben. Miután létrehoz egy új kérdések és válaszok készítő szolgáltatás és egy új Tudásbázis szolgáltatás, ellenőrizheti, hogy a nyelvi helyesen van beállítva.

1. Keresse meg a [Azure-portálon](https://portal.azure.com/).

2. Válassza ki **erőforráscsoportok** , és keresse meg az erőforráscsoportot, ahol a kérdések és válaszok készítő szolgáltatás-e a telepített, és válassza ki a **Azure Search** erőforrás.

    ![Válassza ki az Azure Search erőforrás](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Válassza ki a **testkb** index. Az Azure Search-index mindig az elsőt létrehozott, a mentett tartalmat, hogy a szolgáltatás a Tudásbázis alapok tartalmazza. 

    ![Válassza ki a teszt KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Válassza ki **mezők** testkb részleteit megjelenítő szakasz.

    ![Mezők kiválasztása](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Jelölje be a **Analyzer** nyelvre vonatkozó részletek megjelenítéséhez.

    ![Válassza ki az elemző eszköz](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Keresse meg, hogy az elemző eszközt egy adott nyelvre van beállítva. Ezen a nyelven automatikusan észlelt a Tudásbázis létrehozása lépés során. Ezt a nyelvet nem lehet módosítani, az erőforrás létrehozása után.

    ![Kijelölt elemző eszköz](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy kérdés-válasz botot Azure Botot szolgáltatással](../Tutorials/create-qna-bot.md)
