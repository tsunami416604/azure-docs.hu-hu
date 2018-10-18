---
title: A QnA Maker szolgáltatás – QnA Maker beállítása
titleSuffix: Azure Cognitive Services
description: Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: c4175a22cc503113f3b3ef64f03f0ae0913bd20e
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364897"
---
# <a name="create-a-qna-maker-service"></a>A QnA Maker szolgáltatás létrehozása

Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.

A telepítő néhány Azure-erőforrások üzembe helyezése. Ezeket az erőforrásokat együtt kezelheti a Tudásbázis-tartalmat, és kérdés-válasz képességeket biztosít, mintha a végpont.

1. Jelentkezzen be az [Azure Portalra](<https://portal.azure.com>).

2.  Kattintson a **az új erőforrás hozzáadása**, és írja be a "qna maker" kifejezést a keresőmezőbe, és válassza ki a QnA Maker erőforrást

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Kattintson a **létrehozás** a feltételek és kikötések elolvasása után.

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. A **QnA Maker**, válassza ki a megfelelő szintek és régiók.

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Töltse ki a **neve** a QnA Maker szolgáltatás azonosítására, egyedi névvel. Ez a név is azonosítja a QnA Maker végpontot, amelyhez a tudásbázisok lesz társítva.
    * Válassza ki a **előfizetés** a QnA Maker erőforrás meg lesz telepítve.
    * Válassza ki a **felügyeleti tarifacsomag** a QnA Maker szolgáltatásokhoz (a portál és a felügyeleti API-k). Lásd: [Itt](https://aka.ms/qnamaker-pricing) az SKU-k, a díjszabással kapcsolatos információkért.
    * Hozzon létre egy új **erőforráscsoport** (ajánlott), vagy használjon egy meglévőt, melyben szeretné üzembe helyezni a QnA Maker erőforrás.
    * Válassza ki a **tarifacsomag keresési** az Azure Search szolgáltatás. Ha látja az ingyenes szint lehetőség szürkén jelenik meg, az azt jelenti, már rendelkezik egy ingyenes Azure Search szolgáltatásréteg, az előfizetésében üzembe helyezve. Ebben az esetben szüksége lesz az alapszintű Azure Search szolgáltatásréteg kezdődhet. Részletek megtekintése az Azure search díjszabása [Itt](https://azure.microsoft.com/pricing/details/search/).
    * Válassza ki a **hely keresése** hol szeretné üzembe helyezni az Azure Search-adatok. Ahol a vásárlói adatokat tárolni kell a korlátozások tájékoztatja a helyet úgy dönt, az Azure search.
    * Nevezze el az App Service- **alkalmazásnév**.
    * Alapértelmezés szerint az App Service-ben alapértelmezés szerint a standard (S1) szintre. Létrehozása után módosíthatja a tervet. További részleteket az App service díjszabását [Itt](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Válassza ki a **webhely helye** ahol az App Service üzembe helyezve.

        > [!NOTE]
        > A keresés helyét a webhely helye eltérő lehet.

    * Válassza ki, hogy engedélyezni szeretné **Application Insights** vagy sem. Ha **Application Insights** van engedélyezve, QnA Maker telemetriát gyűjt a forgalom, a csevegési naplók és a hibákat.
    * Válassza ki a **alkalmazáselemzések helye** ahol Application Insights-erőforrás üzembe helyezve.

5. Miután az összes mező érvényesítése után kattintson a **létrehozás** ezek a szolgáltatások központi telepítésének elindítása az előfizetésében. Néhány percet vesz igénybe.

6.  Ha végzett az üzembe helyezés, látni fogja az alábbi, az előfizetésben létrehozott erőforrásokat.

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre és Tudásbázis közzététele](../Quickstarts/create-publish-knowledge-base.md)