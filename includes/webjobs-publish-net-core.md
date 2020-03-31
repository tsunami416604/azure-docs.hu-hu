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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76020958"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** párbeszédpanelen válassza a **Microsoft Azure App Service**( Új **létrehozása**) lehetőséget, majd a **Közzététel**lehetőséget.

   ![Közzétételi cél kivétele](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Az **App Service létrehozása** párbeszédpanelen használja a kép alatti táblázatban megadott üzemeltetési beállításokat:

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[Tárhelyterv](../articles/app-service/overview-hosting-plans.md)** | App Service-csomag | Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../articles/app-service/overview-hosting-plans.md) határozza meg. Költségeket takaríthat meg, ha beállítja, hogy a webalkalmazások egyetlen közös App Service-csomag használatával több alkalmazást tároljanak. Az App Service-csomagok határozzák meg a régiót, a példány méretet, a méretezési számot és a termékváltozatot (ingyenes, megosztott, alapszintű, standard vagy prémium). Új App Service-csomag létrehozásához válassza az **Új** lehetőséget. |

1. Kattintson **a Létrehozás** gombra, ha webfeladatot és kapcsolódó erőforrásokat hozhat létre az Azure-ban ezekkel a beállításokkal, és üzembe helyezheti a projektkódot.