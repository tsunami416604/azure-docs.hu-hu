---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131554"
---
1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Válassza ki **+ erőforrás létrehozása** a bal oldalon, majd válassza ki **függvényalkalmazás**.

1. A **futtatási csomag**, válassza a **App Service-csomag**, majd **App Service-csomag/hely**.

    ![Függvényalkalmazás létrehozása](./media/functions-premium-create/create-function-app-resource.png)

1. Válassza ki **új létrehozása**, írja be egy **App Service-csomag** nevét, válassza ki egy **hely** a egy [régió](https://azure.microsoft.com/regions/) Önhöz vagy más szolgáltatások a függvények eléréséhez, és válassza ki **tarifacsomag**.

    ![App Service-csomag létrehozása](./media/functions-premium-create/new-app-service-plan.png)

1. Válassza ki a **EP1** (rugalmas prémium szintű) csomag, majd válassza a **alkalmaz**.

    ![Prémium adatforgalmi díjcsomag kiválasztása](./media/functions-premium-create/hosting-plan.png) 

1. Válassza ki **OK** a csomag létrehozásához, majd a fennmaradó függvényalkalmazás-beállításokat, használja az ábra alatti táblázatban megadott. 

    ![Befejezett app service-csomag](./media/functions-premium-create/create-function-app.png)  

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. Az ajánlott érték is használható. |
    | **OS** | Windows | Linux jelenleg nem támogatott a prémium szintű csomag. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. Csak azokat a nyelveket támogatja a választott **operációs rendszer** jelennek meg. |
    | **[Tárolás](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights-erőforrást az *alkalmazásnév* található a legközelebbi támogatott régióban. Ezzel a beállítással bővül, módosíthatja a **új-erőforrásnév** , vagy válasszon egy másik **hely** a egy [Azure földrajzi](https://azure.microsoft.com/global-infrastructure/geographies/) ahol az adatokat tárolni szeretné. |

1. Miután a rendszer érvényesíti a beállításokat, válassza ki **létrehozás**.

1. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-premium-create/function-app-create-notification.png)

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Lehetőség kiválasztásával **rögzítés az irányítópulton**. Rögzítés megkönnyíti az irányítópultról térjen vissza a függvény alkalmazás-erőforrást.