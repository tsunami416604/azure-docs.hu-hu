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
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021118"
---
1. Az Azure-portál megnyitása[https://portal.azure.com](https://portal.azure.com)

1. Jelölje be az **Erőforrás létrehozása** gombot

    ![Erőforrás létrehozása](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Válassza **a Számítási** > **függvény alkalmazást**.

    ![Függvényalkalmazás létrehozása az Azure Portalon](./media/functions-premium-create/function-app-create-start.png)

1. Használja az ábra alatti táblázatban megadott függvényalkalmazás-beállításokat.

    ![Alapvető beállítások](./media/functions-premium-create/function-app-create-basics.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **Előfizetés** | Az Ön előfizetése | Az előfizetés, amelyben létrehozta az új függvényalkalmazást. |
    | **[Erőforráscsoport](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Az új erőforráscsoport neve, amelyben létrehozza a függvényalkalmazást. |
    | **Függvényalkalmazás neve** | Globálisan egyedi név | Az új függvényalkalmazást azonosító név. Az érvényes `a-z` karakterek (kis- és nagybetűk et nem nem figyelembe vevő), `0-9`. `-`  |
    |**Közzététele**| Kód | Kódfájlok közzétételét teszi lehetővé egy Docker-tárolóban. |
    | **Futtatókörnyezet verme** | Elsődleges nyelv | Válasszon egy olyan futtatókörnyezetet, amely támogatja a kedvenc függvényprogramozási nyelvét. Válassza a **.NET** lehetőséget a C# és az F# függvényekhez. |
    |**Régió**| Preferált régió | Válasszon egy [régiót](https://azure.microsoft.com/regions/) a közelben, vagy a függvények által elért más szolgáltatások közelében. |

    Válassza a **Tovább : >** gombot.

1. Adja meg a következő üzemeltetési beállításokat.

    ![Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Tárfiók](../articles/storage/common/storage-account-create.md)** |  Globálisan egyedi név |  Hozzon létre egy tárfiókot a függvényalkalmazás számára. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat, amelynek meg kell felelnie a [tárfiók követelményeinek.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Operációs rendszer**| Előnyben részesített operációs rendszer | Az operációs rendszer előre ki van választva a futásidejű verem kiválasztása alapján, de szükség esetén módosíthatja a beállítást. |
    | **[Terv](../articles/azure-functions/functions-scale.md)** | Prémium | A Csomagtípus mezőben válassza a **Prémium (előzetes verzió)** lehetőséget, és válassza ki a *Windows-csomag,* valamint a *Termékváltozat és a méret* beállítások alapértelmezett beállításait. |

    Válassza a **Tovább : >figyelése** gombot.

1. Adja meg a következő figyelési beállításokat.

    ![Figyelés](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Beállítás      | Ajánlott érték  | Leírás |
    | ------------ | ---------------- | ----------- |
    | **[Alkalmazáselemzési adatok](../articles/azure-functions/functions-monitoring.md)** | Alapértelmezett | Létrehoz egy Application Insights-erőforrást ugyanazzal az *alkalmazásnévvel* a legközelebbi támogatott régióban. A beállítás kibontásával módosíthatja az **új erőforrás nevét,** vagy választhat egy másik **helyet** egy [Azure-földrajzi területen,](https://azure.microsoft.com/global-infrastructure/geographies/) ahol tárolni szeretné az adatokat. |

    Válassza **a Véleményezés + Létrehozás** lehetőséget az alkalmazáskonfigurációs beállítások áttekintéséhez.

1. Kattintson a **Létrehozás** elemre a függvényalkalmazás kiépítéséhez és üzembe helyezéséhez.