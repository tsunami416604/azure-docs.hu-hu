---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 911db285d1ca885142e8a80345926ce76379e3bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958612"
---
1. A Azure Portal megnyitása [https://portal.azure.com](https://portal.azure.com)

1. Válassza az **erőforrás létrehozása** gombot

    ![Erőforrás létrehozása](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Válassza a **számítási** > **függvényalkalmazás**lehetőséget.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-premium-create/function-app-create-start.png)

1. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    ![Alapvető beállítások](./media/functions-premium-create/function-app-create-basics.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek `a-z` (kis-és nagybetűk megkülönböztetése), `0-9`és `-`.  |
    |**Közzététel**| Kód | Kód vagy Docker-tároló közzétételének lehetősége. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**Régió**| Előnyben részesített régió | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |

    Válassza a **következő: > üzemeltetése** gombot.

1. Adja meg a következő üzemeltetési beállításokat.

    ![Üzemeltetés](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Tárfiók](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **[Csomag](../articles/azure-functions/functions-scale.md)** | Prémium | A csomag típusa beállításnál válassza a **prémium (előzetes verzió)** lehetőséget, majd válassza a *Windows-csomag* és az *SKU és a méret* kiválasztásának alapértelmezett értékeit. |

    Kattintson a **Tovább: figyelés >** gombra.

1. Adja meg a következő figyelési beállításokat.

    ![Monitoring](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** egy olyan [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné. |

    Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.