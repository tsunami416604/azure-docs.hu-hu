---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/17/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: cd118def8b9caf97f26aaa5ad5dc1f6fb015316d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73469228"
---
1. A Azure Portal menüben válassza az **erőforrás létrehozása**lehetőséget.

    ![Erőforrás hozzáadása a Azure Portal menü használatával](./media/functions-create-function-app-portal/create-function-app-resource.png)

1. Az **új** lapon válassza a **számítás** > **függvényalkalmazás**lehetőséget.

1. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    ![Alapvető beállítások](./media/functions-create-function-app-portal/function-app-create-basics.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes karakterek `a-z` (kis-és nagybetűk megkülönböztetése), `0-9`és `-`.  |
    |**Közzététel**| Kód | Kód vagy Docker-tároló közzétételének lehetősége. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**Régió**| Előnyben részesített régió | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |

    Válassza a **következő: > üzemeltetése** gombot.

1. Adja meg a következő beállításokat az üzemeltetéshez.

    ![Üzemeltetés](./media/functions-create-function-app-portal/function-app-create-hosting.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Tárfiók](../articles/storage/common/storage-quickstart-create-account.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [Storage-fiókra vonatkozó követelményeknek](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **[Csomag](../articles/azure-functions/functions-scale.md)** | Használatalapú csomag | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **használatalapú csomagban** az erőforrások hozzáadása dinamikusan történik a függvények igényeinek megfelelően. Ebben a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) üzemeltetésben, csak azért az időért fizet, amikor futnak a függvényei. Ha App Service-csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../articles/azure-functions/functions-scale.md).  |

    Kattintson a **Tovább: figyelés >** gombra.

1. Adja meg a következő beállításokat a figyeléshez.

    ![Figyelés](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights erőforrást ugyanahhoz az *alkalmazáshoz* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét** , vagy kiválaszthat egy másik **helyet** egy olyan [Azure-földrajzban](https://azure.microsoft.com/global-infrastructure/geographies/) , ahol az adatait tárolni szeretné. |

    Válassza a **felülvizsgálat + létrehozás** lehetőséget az alkalmazás-konfiguráció kiválasztásának áttekintéséhez.

1. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

    ![Üzembe helyezési értesítés](./media/functions-create-function-app-portal/function-app-create-notification2.png)

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a rögzítés az irányítópulton**lehetőséget is. A rögzítéssel egyszerűbbé válik a Function app-erőforráshoz való visszatérés az irányítópultról.
