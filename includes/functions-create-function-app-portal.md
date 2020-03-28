---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c590b61ee1424d32d83dc5f758682fde37492c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057136"
---
1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

1. Az **Új** lapon válassza a **Számítási** > **függvény alkalmazás lehetőséget.**

1. Az **Alapok** lapon használja az alábbi táblázatban megadott függvényalkalmazás-beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes `a-z` karakterek (kis- és nagybetűk et nem nem figyelembe vevő), `0-9`. `-`  |
    |**Közzététele**| Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET Core lehetőséget** a C# és Az F# függvényekhez. |
    |**Verzió**| Verziószám | Válassza ki a telepített futásidejű verziót.  |
    |**Régió**| Preferált régió | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |

    ![Alapvető beállítások](./media/functions-create-function-app-portal/function-app-create-basics.png)

1. Válassza a **Tovább : Hosting lehetőséget.** A **Tárhely** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Tárfiók](../articles/storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfióknevek nek 3 és 24 karakter közötti hosszúságúak lehetnek, és csak számokat és kisbetűket tartalmazhatnak. Meglévő fiókot is használhat, amelynek meg kell felelnie a [tárfiók követelményeinek.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **[Terv](../articles/azure-functions/functions-scale.md)** | **Felhasználás (kiszolgáló nélküli)** | Szolgáltatási csomag, amely meghatározza az erőforrások lefoglalását a függvényalkalmazáshoz. Az alapértelmezett **felhasználási** tervben az erőforrások dinamikusan kerülnek hozzáadásra a függvények által megkövetelt módon. Ebben a [kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) tárhely, akkor fizetni csak az idő a funkciók futnak. Ha App Service-csomagot használ, foglalkoznia kell a [függvényalkalmazás méretezésével](../articles/azure-functions/functions-scale.md).  |

    ![Hosting](./media/functions-create-function-app-portal/function-app-create-hosting.png)

1. Válassza a **Tovább lehetőséget : Figyelés**. A **Figyelés** lapon adja meg a következő beállításokat.

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Alkalmazáselemzési adatok](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights-erőforrást ugyanazzal az *alkalmazásnévvel* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét,** vagy választhat egy másik **helyet** egy [Azure-földrajzi területen,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol tárolni szeretné az adatokat. |

    ![Figyelés](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Válassza **a Véleményezés + létrehozás** lehetőséget az alkalmazáskonfigurációs beállítások áttekintéséhez.

1. A **Véleményezés + létrehozás** lapon tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.

1. Válassza a portál jobb felső sarkában található Értesítések ikont, és várja meg az **üzembe helyezés sikerességét** jelző üzenetet.

1. Az új függvényalkalmazás megtekintéséhez válassza az **Erőforrás megnyitása** lehetőséget. Kiválaszthatja **a Rögzítés az irányítópulton**lehetőséget is. A rögzítés megkönnyíti a függvényalkalmazás-erőforráshoz való visszatérést az irányítópultról.

    ![Telepítési értesítés](./media/functions-create-function-app-portal/function-app-create-notification2.png)