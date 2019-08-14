---
title: A QnA Maker szolgáltatás – QnA Maker beállítása
titleSuffix: Azure Cognitive Services
description: Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967670"
---
# <a name="create-a-qna-maker-service"></a>A QnA Maker szolgáltatás létrehozása

Mielőtt bármilyen QnA Maker tudásbázisok hozhat létre, először be kell állítania a QnA Maker szolgáltatást az Azure-ban. Az új erőforrásokat hozhatnak létre egy előfizetésben engedélyezésével bárki is beállíthatja a QnA Maker szolgáltatást.

## <a name="create-a-new-service"></a>Új szolgáltatás létrehozása

Ez az eljárás üzembe helyez néhány Azure-erőforrást. Ezeket az erőforrásokat együtt kezelheti a Tudásbázis-tartalmat, és kérdés-válasz képességeket biztosít, mintha a végpont.

1. Jelentkezzen be a Azure Portalba, és [hozzon létre egy QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -erőforrást.

1. A feltételek és kikötések elolvasása után válassza a **Létrehozás** lehetőséget.

    ![Hozzon létre egy új QnA Maker szolgáltatást](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. A **QnA Maker**, válassza ki a megfelelő szintek és régiók.

    ![Hozzon létre egy új QnA Maker – tarifacsomag és régiók](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Töltse ki a **neve** a QnA Maker szolgáltatás azonosítására, egyedi névvel. Ez a név is azonosítja a QnA Maker végpontot, amelyhez a tudásbázisok lesz társítva.
    * Válassza ki a **előfizetés** a QnA Maker erőforrás meg lesz telepítve.
    * Válassza ki a QnA Maker felügyeleti szolgáltatások (portál és felügyeleti API-k) **díjszabási szintjét** . Lásd: [Itt](https://aka.ms/qnamaker-pricing) az SKU-k, a díjszabással kapcsolatos információkért.
    * Hozzon létre egy új **erőforráscsoport** (ajánlott), vagy használjon egy meglévőt, melyben szeretné üzembe helyezni a QnA Maker erőforrás. QnA Maker több Azure-erőforrást hoz létre; Amikor létrehoz egy erőforráscsoportot az erőforrások tárolásához, egyszerűen megkeresheti, kezelheti és törölheti ezeket az erőforrásokat az erőforráscsoport neve alapján.
    * Válassza ki az **erőforráscsoport helyét**.
    * Válassza ki a **tarifacsomag keresési** az Azure Search szolgáltatás. Ha látja az ingyenes szint lehetőség szürkén jelenik meg, az azt jelenti, már rendelkezik egy ingyenes Azure Search szolgáltatásréteg, az előfizetésében üzembe helyezve. Ebben az esetben szüksége lesz az alapszintű Azure Search szolgáltatásréteg kezdődhet. Részletek megtekintése az Azure search díjszabása [Itt](https://azure.microsoft.com/pricing/details/search/).
    * Válassza ki a **hely keresése** hol szeretné üzembe helyezni az Azure Search-adatok. Ahol a vásárlói adatokat tárolni kell a korlátozások tájékoztatja a helyet úgy dönt, az Azure search.
    * Nevezze el az App Service- **alkalmazásnév**.
    * Alapértelmezés szerint az App Service-ben alapértelmezés szerint a standard (S1) szintre. Létrehozása után módosíthatja a tervet. További részleteket az App service díjszabását [Itt](https://azure.microsoft.com/pricing/details/app-service/).
    * Válassza ki a **webhely helye** ahol az App Service üzembe helyezve.

        > [!NOTE]
        > A keresés helyét a webhely helye eltérő lehet.

    * Válassza ki, hogy engedélyezni szeretné **Application Insights** vagy sem. Ha **Application Insights** van engedélyezve, QnA Maker telemetriát gyűjt a forgalom, a csevegési naplók és a hibákat.
    * Válassza ki a **alkalmazáselemzések helye** ahol Application Insights-erőforrás üzembe helyezve.
    * A költségmegtakarítási mértékek esetében megoszthat néhányat, de nem minden QnA Makerhoz létrehozott Azure-erőforrást. [](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) 

1. Az összes mező ellenőrzése után a **Létrehozás** gombra kattintva megkezdheti ezeknek a szolgáltatásoknak az előfizetésében való üzembe helyezését. Néhány percet vesz igénybe.

1. Ha végzett az üzembe helyezés, látni fogja az alábbi, az előfizetésben létrehozott erőforrásokat.

    ![Létrehozott egy új QnA Maker szolgáltatás-erőforrást](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Felügyeleti szolgáltatás régiója

A QnA Maker felügyeleti szolgáltatása, amelyet csak a portál & a kezdeti adatfeldolgozáshoz használ, csak az USA nyugati régiójában érhető el. Ebben a Nyugat-amerikai szolgáltatásban nem tárolunk ügyféladatokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre és Tudásbázis közzététele](../Quickstarts/create-publish-knowledge-base.md)
