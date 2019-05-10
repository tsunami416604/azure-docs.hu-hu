---
title: Útmutató az alkalmazások konfigurálása az Azure segítségével a szolgáltatás jelölők kezelése |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan funkció jelzők külön-külön kezelheti az alkalmazás használatával az Azure-alkalmazások konfigurálása
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d995a2e9f0d05dc3b0853036e8fb3c04ccdfab96
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412342"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Oktatóanyag: Az Azure-alkalmazások konfigurálása a szolgáltatás jelölők kezelése

Minden funkció jelző tárolhatja az Azure-alkalmazások konfigurálása és egyetlen helyről felügyelheti őket. Felhasználói Felületet, a portál nevű rendelkezik **funkció Manager**, vagyis funkció jelzők tervezve. Ezenkívül Alkalmazáskonfiguráció támogatja a .NET Core-szolgáltatás jelző Adatséma natív módon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Határozza meg, és az alkalmazás konfigurációját a szolgáltatás jelölők kezelése.
> * Adatelérési szolgáltatás jelzők az alkalmazásból.

## <a name="create-feature-flags"></a>A szolgáltatás jelzők létrehozása

A **funkció Manager** az Azure Portalon az alkalmazás konfigurációjának létrehozása egy felhasználói Felületet biztosít, és a szolgáltatás kezelése megőrzendő tartalomként jelöli meg, amely használja az alkalmazásban. Kövesse az alábbi lépéseket egy új funkció jelölője hozzáadásához.

1. Válassza ki **funkció Manager** > **+ létrehozás** hozzáadása a szolgáltatás azt a jelzőt.

    ![A szolgáltatás jelző listája](./media/azure-app-configuration-feature-flags.png)

2. Adja meg a szolgáltatás jelző egyedi kulcs nevét. Ez a név a kódban a jelző hivatkoznia kell.

3. A funkció jelölője igény szerint adjon meg több emberi rövid leírást.

4. A funkció jelölője kezdeti állapotának beállításához. Hogy ez általában csak *a* vagy *ki*.

    ![A szolgáltatás jelző létrehozása](./media/azure-app-configuration-feature-flag-create.png)

5. Ha az állapot az *a*, igény szerint adja meg a további feltétel ahhoz, hogy az **szűrő hozzáadása**. Adjon meg egy beépített vagy egyéni szűrő kulcsot, és társítsa a paraméter(ek) használatával történt. Beépített szűrők a következők:

    | Kulcs | JSON-paraméterek |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 – 100 %-os} |
    | Microsoft.TimeWindow | {"Start": UTC szerinti idő esetén "Vége": UTC idő} |

    ![A szolgáltatás jelző szűrő](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Szolgáltatásállapotok jelző frissítése

Kövesse az alábbi lépéseket egy funkció jelző állapot értékének módosításához.

1. Válassza ki **funkció Manager**.

2. Kattintson a **...**   >  **Szerkesztése** jobb oldalán a szolgáltatás azt a jelzőt a módosítani kívánt.

3. Állítsa be a szolgáltatás jelző egy új állapotát.

## <a name="access-feature-flags"></a>Adatelérési szolgáltatás jelzők

A szolgáltatás által létrehozott jelzőket a **funkció Manager** tárolása és értelmezi a rendszeres kulcs-értékeket. Speciális névtérelőtag alatt tartják *. appconfig.featureflag*. Az alapul szolgáló kulcs-értékeket használatával megtekintheti a **konfigurációs Explorer**. Az alkalmazás le lehet kérdezni őket az Alkalmazáskonfigurációt konfigurációszolgáltatók, SDK-k, parancssori bővítmények és REST API-k használatával.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan kezelheti a szolgáltatás jelzők és azok használatával Alkalmazáskonfiguráció állapota. Az alábbi cikkekben talál további információt az Alkalmazáskonfigurációt és az ASP.NET Core management támogatásáról.

* [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md)
