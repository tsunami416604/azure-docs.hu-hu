---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b96586c801acfc0419b80fa725a468dcb896f79e
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70197140"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

2. A **Közzététel célhelyének** kiválasztása párbeszédpanelen a rendszerkép alatti táblázatban megadott közzétételi beállításokat használhatja: 

    ![Közzétételi cél kiválasztása](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png)

    | Beállítás      | Leírás                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions használati terv** |   Ha a projektet egy [felhasználási](../articles/azure-functions/functions-scale.md#consumption-plan)csomagban futó Function alkalmazásban teszi közzé, csak a functions-alkalmazás végrehajtása után kell fizetnie. Más üzemeltetési csomagok magasabb költségekkel járnak. További információ: [Azure functions skálázás és üzemeltetés](../articles/azure-functions/functions-scale.md). | 
    | **Új létrehozása** | A kapcsolódó erőforrásokkal rendelkező új Function-alkalmazás az Azure-ban jön létre. Ha a **Meglévő kiválasztása** elemet választja, az Azure-beli meglévő függvényalkalmazás összes fájlját felülírják a helyi projekt fájljai. Csak akkor használja ezt a lehetőséget, ha frissítéseket tesz újra közzé egy meglévő függvényalkalmazásba. |
    | **Futtatás a csomagfájl alapján** | A Function alkalmazás üzembe helyezése a [zip](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) [-](../articles/azure-functions/run-functions-from-deployment-package.md) telepítéssel, a csomaghoz való futtatási móddal engedélyezve. A függvények futtatásának ajánlott módja, ami jobb teljesítményt eredményez. Ha nem használja ezt a lehetőséget, ügyeljen arra, hogy a Function app-projekt helyileg fusson, mielőtt közzéteszi az Azure-ban. |


3. Kattintson a **Publish** (Közzététel) elemre. Ha még nem jelentkezett be Azure-fiókjába a Visual studióból, válassza a **Bejelentkezés**lehetőséget. Létrehozhat egy ingyenes Azure-fiókot is.

4. A **app Serviceban: Hozzon** létre új párbeszédpanelt, és használja az alábbi táblázatban megadott **üzemeltetési** beállításokat a rendszerkép alatt:

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. Érvényes karakterek: `a-z`, `0-9` és `-`. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[Üzemeltetési csomag](../articles/azure-functions/functions-scale.md)** | Használatalapú csomag | Az **új** elemre kattintva hozzon létre egy kiszolgáló nélküli tervet, és válassza a **méret** alatti **felhasználás** lehetőséget. Ezenkívül válasszon ki egy **Helyet** egy, a közelben, vagy a függvények által elért más szolgáltatások közelében található [régióban](https://azure.microsoft.com/regions/). Ha nem **használatalapú** csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../articles/azure-functions/functions-scale.md).  |
    | **[Azure Storage](../articles/storage/common/storage-quickstart-create-account.md)** | Általános célú Storage-fiók | A Functions futtatókörnyezetének szüksége van egy Azure Storage-fiókra. Az **új** gombra kattintva hozzon létre egy általános célú Storage-fiókot. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. A **Létrehozás** gombra kattintva létrehozhat egy Function alkalmazást és kapcsolódó erőforrásokat az Azure-ban ezekkel a beállításokkal, és üzembe helyezheti a függvény projekt kódját. 

6. Miután az üzembe helyezés befejeződött, jegyezze fel a **webhely URL-címét**, amely a függvényalkalmazás címe az Azure-ban.

    ![Sikeres közzétételt jelző üzenet](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
