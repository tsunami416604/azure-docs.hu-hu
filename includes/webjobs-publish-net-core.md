---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4860532e59227618ce819772887556719ecb53fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020958"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** párbeszédpanelen válassza a **Microsoft Azure app Service**lehetőséget, válassza az **új létrehozása**lehetőséget, majd válassza a **Közzététel**lehetőséget.

   ![Közzététel célhelyének kiválasztása](./media/webjobs-publish-netcore/pick-publish-target.png)

1. A **app Service létrehozása** párbeszédpanelen a rendszerkép alatti táblázatban megadott üzemeltetési beállításokat használhatja:

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[Üzemeltetési csomag](../articles/app-service/overview-hosting-plans.md)** | App Service-csomag | Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../articles/app-service/overview-hosting-plans.md) határozza meg. Költségeket takaríthat meg, ha beállítja, hogy a webalkalmazások egyetlen közös App Service-csomag használatával több alkalmazást tároljanak. App Service csomagok határozzák meg a régiót, a példány méretét, a méretezési darabszámot és az SKU-t (ingyenes, közös, alapszintű, standard vagy prémium). Új App Service terv létrehozásához válassza az **új** lehetőséget. |

1. A **Létrehozás** gombra kattintva létrehozhat egy webjobs és kapcsolódó erőforrásokat az Azure-ban ezekkel a beállításokkal és a projekt kódjának üzembe helyezésével.