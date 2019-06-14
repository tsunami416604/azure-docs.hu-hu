---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 11/02/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5c513a76537eb5b28e85e6289a610e318ab790d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050746"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

2. Válassza az **Azure-függvényalkalmazás**, az **Új létrehozása**, majd a **Közzététel** lehetőséget.

    ![Közzétételi cél kiválasztása](./media/functions-vstools-publish/functions-visual-studio-publish-profile.png) 

    Kattintva **futtatása (ajánlott) csomag fájlból**, a függvényalkalmazás telepíti használatával [Zip üzembe](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) a [Run-a-Package](../articles/azure-functions/run-functions-from-deployment-package.md) mód engedélyezve van. Ez a javasolt módja a függvények fut, és jobb teljesítményt eredményez.

    >[!CAUTION]
    >Ha a **Meglévő kiválasztása** elemet választja, az Azure-beli meglévő függvényalkalmazás összes fájlját felülírják a helyi projekt fájljai. Csak akkor használja ezt a lehetőséget, ha frissítéseket tesz újra közzé egy meglévő függvényalkalmazásba.

3. Ha még nem csatlakoztatta a Visual Studiót az Azure-fiókjához, válassza a **Fiók hozzáadása...** lehetőséget.

4. Az **App Service létrehozása** párbeszédpanelen alkalmazza a kép alatti táblázatban megadott **Üzemeltetési** beállításokat:

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/functions-vstools-publish/functions-visual-studio-publish.png)

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[App Service-csomag](../articles/azure-functions/functions-scale.md)** | Használatalapú csomag | Miután rákattintott az **Új** elemre a csomag létrehozásához, győződjön meg róla, hogy a **Méret** elemnél a **Használatalapú** lehetőséget választja ki egy kiszolgáló nélküli csomag létrehozásához. Ezenkívül válasszon ki egy **Helyet** egy, a közelben, vagy a függvények által elért más szolgáltatások közelében található [régióban](https://azure.microsoft.com/regions/). Ha nem **használatalapú** csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../articles/azure-functions/functions-scale.md).  |
    | **[Storage-fiók](../articles/storage/common/storage-quickstart-create-account.md)** | Általános célú tárfiók | A Functions futtatókörnyezetének szüksége van egy Azure Storage-fiókra. Kattintson a **új** egy általános célú tárfiók létrehozásához. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

5. Kattintson a **Létrehozás** elemre egy függvényalkalmazás és a kapcsolódó erőforrások Azure-ban való létrehozásához ezekkel a beállításokkal, illetve a függvény projektkódjának üzembe helyezéséhez. 

6. Miután az üzembe helyezés befejeződött, jegyezze fel a **webhely URL-címét**, amely a függvényalkalmazás címe az Azure-ban.

    ![Sikeres közzétételt jelző üzenet](./media/functions-vstools-publish/functions-visual-studio-publish-complete.png)
