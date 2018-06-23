---
title: Tudnivalók a kérdések és válaszok készítő szolgáltatás – Microsoft kognitív szolgáltatások telepítéséről |} Microsoft Docs
titleSuffix: Azure
description: Útmutató a kérdések és válaszok készítő szolgáltatás beállítása
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce452dd686529e017b4eae4717eadb044b389409
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348663"
---
# <a name="create-a-qna-maker-service"></a>Kérdések és válaszok készítő szolgáltatás létrehozása

A kérdések és válaszok készítő Tudásbázis körrel létrehozása előtt először be kell állítania egy kérdés-válasz készítő szolgáltatás az Azure-ban. Bárki, aki létrehozni az új erőforrásokat egy előfizetésben engedélyezési állíthat be egy készítő kérdések és válaszok szolgáltatás.

A telepítő telepít néhány Azure-erőforrások. Ezekkel az erőforrásokkal együtt, a Tudásbázis tartalom kezelése és kérdés megválaszolásával lehetőségeket nyújtson, mintha a végpont.

1. Jelentkezzen be az [Azure portálra](<https://portal.azure.com>).

2.  Kattintson a **új erőforrás hozzáadása**, és írja be a "kérdés-válasz készítő" keresési, és válassza ki a kérdések és válaszok készítő erőforrás

    ![Hozzon létre egy új kérdések és válaszok készítő](../media/qnamaker-how-to-setup-service/create-new-resource.png)

3.  Kattintson a **létrehozása** a használati feltételek elolvasása után.

    ![Hozzon létre egy új kérdések és válaszok készítő](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

4. A **kérdések és válaszok készítő**, válassza ki a megfelelő szinteket, és régiókban.

    ![Hozzon létre egy új kérdések és válaszok készítő](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Töltse ki a **neve** egyedi névvel kell a kérdések és válaszok készítő szolgáltatás azonosítására. Ez a név is azonosítja a kérdések és válaszok készítő végpontot, amelyhez a Tudásbázis körrel lesz társítva.
    * Válassza ki a **előfizetés** a kérdések és válaszok készítő erőforrás meg lesznek telepítve.
    * Válassza ki a **tarifacsomag felügyeleti** a kérdések és válaszok készítő szolgáltatásokhoz (portál és API-k kezelése). Lásd: [Itt](https://aka.ms/qnamaker-pricing) a termékváltozatok árakkal kapcsolatos részletekért.
    * Hozzon létre egy új **erőforráscsoport** (ajánlott), vagy használjon egy meglévőt, amelyben a kérdések és válaszok készítő erőforrás telepítéséhez.
    * Válassza ki a **tarifacsomag keresési** az Azure Search szolgáltatás. Ha látja az ingyenes szint beállítás szürkén jelenik meg, az azt jelenti, már van egy ingyenes Azure Search réteg az előfizetés telepítve. Ebben az esetben szüksége lesz az alapszintű Azure Search réteg kezdődhet. Az Azure search árazási részletek [Itt](https://azure.microsoft.com/en-us/pricing/details/search/).
    * Válassza ki a **keresési helyet** ahová Azure Search adatokat is be kell állítani. Korlátozások a ügyféladatokat kell tárolásához tájékoztatja az Azure Search a kiválasztott hely.
    * Adjon nevet az App service a **alkalmazásnév**.
    * Alapértelmezés szerint az App service alapértelmezés szerint használt érték (S1) standard csomagra. A terv létrehozása után módosíthatja. További részletek az App Service szolgáltatás díjszabása [Itt](https://azure.microsoft.com/en-in/pricing/details/app-service/).
    * Válassza ki a **webhely hely** hova szeretné telepíteni az App Service.

        > [!NOTE]
        > A keresés helyét a webhely helye eltérő lehet.

    * Válassza ki, hogy engedélyezni szeretné **Application Insights** vagy sem. Ha **Application Insights** van engedélyezve, kérdések és válaszok készítő gyűjti telemetriai forgalom, csevegési naplók és a hibák.
    * Válassza ki a **App insights hely** hova szeretné telepíteni az Application Insights-erőforrást.

5. Miután az összes mező érvényesíti, rákattinthat a **létrehozása** ezek a szolgáltatások központi telepítésének elindítása az előfizetésben. Eltarthat néhány percig.

6.  Miután az üzembe helyezés, megjelenik a következő források létrehozni az előfizetésben.

    ![Hozzon létre egy új kérdések és válaszok készítő](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre, és tegye közzé a Tudásbázis](../Quickstarts/create-publish-knowledge-base.md)