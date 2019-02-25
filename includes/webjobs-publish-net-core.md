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
ms.openlocfilehash: 97387e24d5b55c1438a69da1a1fd0a9bc1720e47
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56751287"
---
1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A a **közzététel** párbeszédpanelen válassza **Microsoft Azure App Service**, válassza a **hozzon létre új**, majd válassza ki **közzététel**.

   ![Válasszon cél közzététele](./media/webjobs-publish-netcore/pick-publish-target.png)

1. Az a **létrehozása App Service** párbeszédablak, használja az ábra alatti táblázatban megadott üzemeltetési beállításokat:

    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/webjobs-publish-netcore/app-service-dialog.png)

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító egyedi név. |
    | **Előfizetés** | Válassza ki az előfizetését | A használandó előfizetés. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Az erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Válassza az **Új** elemet egy új erőforráscsoport létrehozásához.|
    | **[Szolgáltatási csomag](../articles/app-service/overview-hosting-plans.md)** | App Service-csomag | Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../articles/app-service/overview-hosting-plans.md) határozza meg. Költségeket takaríthat meg, ha beállítja, hogy a webalkalmazások egyetlen közös App Service-csomag használatával több alkalmazást tároljanak. App Service-csomagok határozza meg, a régiót, a példányméret, a méretezési szám és a Termékváltozat (ingyenes, közös, alapszintű, Standard vagy prémium). Válasszon **új** hozhat létre egy új App Service-csomag. |

1. Kattintson a **létrehozás** webjobs-feladat és a kapcsolódó erőforrás létrehozásához az Azure-ban ezeket a beállításokat, és üzembe helyezze projektkódját.