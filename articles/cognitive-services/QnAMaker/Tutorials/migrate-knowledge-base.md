---
title: Tudásbázisok – QnA Maker áttelepítése
titleSuffix: Azure Cognitive Services
description: Tudásbázis áttelepítése igényel, egy Tudásbázis exportálása, majd egy másik való importálásakor.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: eac5e43c69cc09c5945316827a35f729c158d47a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264318"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Exportálás-importálás segítségével Tudásbázis áttelepítése

Tudásbázis áttelepítése igényel, egy Tudásbázis exportálása, majd egy másik való importálásakor. 

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) megkezdése előtt.
* Helyezzen üzembe egy új [QnA Maker szolgáltatást](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Tudásbázis át a QnA Maker
1. Jelentkezzen be a [QnA Maker portal](https://qnamaker.ai).
1. Válassza ki a migrálni kívánt Tudásbázis.

1. Az a **beállítások** lapon jelölje be **Tudásbázis exportálása** a Tudásbázis - tartalmat .tsv-fájl letöltésére kérdések, válaszok, metaadatokat, és neveket, amelyeket az adatforrás ki kell olvasni.

1. Válassza ki **Tudásbázis létrehozása** a felső menüben majd hozzon létre egy üres Tudásbázis. 

    ![Set-adatforrások](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Adja meg a szolgáltatás egy **nevét.** Duplikált nevek használata támogatott, és speciális karaktereket is támogatottak.

1. Kattintson a **Létrehozás** gombra.

    ![Tudásbázis létrehozása](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Az új Tudásbázis, nyissa meg a **beállítások** lapra, és válassza **importálás Tudásbázis**. Importálja a kérdések, válaszok és metaadatokat, és megőrzi az adatforrásnevek, amelyből könyvtárban találhatók.

   ![Importálás Tudásbázis](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Teszt** az új Tudásbázis a teszt panelt. Ismerje meg, hogyan [a Tudásbázis tesztelése](../How-To/test-knowledge-base.md).
1. **Közzététel** a Tudásbázis. Ismerje meg, hogyan [közzéteheti a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. A végpontot használja az alkalmazás vagy robot kódjában. Itt látható, hogyan [hozzon létre egy QnA robotot](../Tutorials/create-qna-bot.md).

    ![A QnA Maker értékek](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    Ezen a ponton minden a Tudásbázis-tartalmat – kérdések, válaszok és metaadatokat, és a forrásfájlok az URL-címeket, nevét és az új Tudásbázis importálásakor. 

## <a name="chat-logs-and-alterations"></a>Csevegési naplók és változásokból
Kis-és változásokból (szinonimák) nem lesznek automatikusan importálva. Használja a [V2 API-k](https://aka.ms/qnamaker-v2-apis) exportálhatja a módosításokat a régi ismereteket és a [V4 API-k](https://aka.ms/qnamaker-v4-apis) áthelyezése a módosításokat a új Tudásbázis.

Nincs semmilyen módon nem lehet áttelepíteni a csevegési naplók, mivel az új Tudásbázis az Application Insights a csevegési naplók tárolásához. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis szerkesztése](../How-To/edit-knowledge-base.md)
