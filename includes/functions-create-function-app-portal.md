---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: e6387c528bb24a66edb2fa1706adc005980dc90e
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47185807"
---
1. Válassza az Azure Portal bal felső sarkában található **Új** gombot, majd válassza a **Számítás** > **Függvényalkalmazás** lehetőséget.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Beállítás      | Ajánlott érték  | Leírás                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Alkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Érvényes karakterek: `a-z`, `0-9` és `-`.  | 
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. | 
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **OS** | Windows | A kiszolgáló nélküli üzemeltetés jelenleg csak Windows rendszeren történő futtatás esetében érhető el. Linuxon való üzemeltetés esetében: [Az első függvény létrehozása Linux rendszerben az Azure CLI használatával](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Szolgáltatási csomag](../articles/azure-functions/functions-scale.md)** | Használatalapú csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **használatalapú csomagban** az erőforrások hozzáadása dinamikusan történik a függvények igényeinek megfelelően. Ebben a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) üzemeltetésben, csak azért az időért fizet, amikor futnak a függvényei. Ha App Service-csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../articles/azure-functions/functions-scale.md).  |
    | **Hely** | Nyugat-Európa | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    | **[Tárolás](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amennyiben az megfelel a [tárfiókokra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Az Application Insights alapértelmezés szerint engedélyezve van. Válasszon egy, a függvényalkalmazáshoz közeli helyet.  |

3. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

4. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Új függvényalkalmazás-beállítások megadása](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget.

> [!TIP]
> Ha nem találja a portálon a függvényalkalmazásokat, próbáljon [Függvényalkalmazásokat felvenni a kedvencek közé az Azure Portalon](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#favorite).
