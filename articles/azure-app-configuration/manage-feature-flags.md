---
title: Útmutató az alkalmazások konfigurálása az Azure segítségével a szolgáltatás jelölők kezelése |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan funkció jelölők kezelése külön-külön az alkalmazásból az Azure-alkalmazások konfigurálása.
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
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393574"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Oktatóanyag: Az Azure-alkalmazások konfigurálása a szolgáltatás jelölők kezelése

Minden funkció jelző tárolhatja az Azure-alkalmazások konfigurálása és egyetlen helyről felügyelheti őket. Alkalmazás-konfigurációs van a portál felhasználói felületének nevű **funkció Manager** , amely kifejezetten készült funkció jelzők. Alkalmazáskonfiguráció is natív módon támogatja a a .NET Core szolgáltatás-jelző sémát.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Határozza meg, és az alkalmazás konfigurációját a szolgáltatás jelölők kezelése.
> * Adatelérési szolgáltatás jelzők az alkalmazásból.

## <a name="create-feature-flags"></a>A szolgáltatás jelzők létrehozása

A szolgáltatás-kezelő alkalmazások konfigurálása az Azure Portalon hozhat létre és kezelhet a szolgáltatás jelzőket, amelyek használatával az alkalmazások felhasználói Felületet biztosít.

Egy új funkció jelölője hozzáadása:

1. Válassza ki **funkció Manager** >  **+ Hozzáadás** hozzáadása a szolgáltatás azt a jelzőt.

    ![A szolgáltatás jelző listája](./media/azure-app-configuration-feature-flags.png)

1. Adja meg a szolgáltatás jelző egyedi kulcs nevét. Ez a név a kódban a jelző hivatkoznia kell.

1. Ha azt szeretné, adjon meg leírást a szolgáltatás jelző.

1. A funkció jelölője kezdeti állapotának beállításához. Ebben az állapotban van, általában *ki* vagy *a*. A *a* állapot *feltételes* egy szűrőt a funkció jelölője való felvételekor.

    ![A szolgáltatás jelző létrehozása](./media/azure-app-configuration-feature-flag-create.png)

1. Az állapotban *a*válassza **+ szűrő hozzáadása** , adja meg azokat a további feltételeket ahhoz, hogy az állapot. Adjon meg egy beépített vagy egyéni szűrő kulcsot, és válassza ki **+ paraméter hozzáadása** egy vagy több paraméter társítása a szűrőt. Beépített szűrők a következők:

    | Kulcs | JSON-paraméterek |
    |---|---|
    | Microsoft.Percentage | {"Value": 0 – 100 %-os} |
    | Microsoft.TimeWindow | {"Start": UTC szerinti idő esetén "Vége": UTC idő} |

    ![A szolgáltatás jelző szűrő](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Szolgáltatásállapotok jelző frissítése

A szolgáltatás jelző állapot értékének módosítása:

1. Válassza ki **funkció Manager**.

1. Jobb oldalán a módosítani kívánt funkció azt a jelzőt, válassza a három pontot ( **...** ), majd válassza ki **szerkesztése**.

1. Állítsa be a szolgáltatás jelző egy új állapotát.

## <a name="access-feature-flags"></a>Adatelérési szolgáltatás jelzők

A szolgáltatás Manager által létrehozott funkció jelzők tárolása és értelmezi a rendszeres kulcs értékeit. Ezek egy speciális névtérelőtag van folyamatosan `.appconfig.featureflag`. Az alapul szolgáló értékek megtekintéséhez használja a konfiguráció Explorer. Az alkalmazás az alkalmazáskonfigurációs konfigurációszolgáltatók, SDK-k, parancssori bővítmények és REST API-k használatával kérheti le ezeket az értékeket.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan funkció jelzők és azok állapota Alkalmazáskonfiguráció kezeléséhez. Alkalmazás konfigurációja és az ASP.NET Core-szolgáltatás-felügyeleti támogatással kapcsolatos további információkért tekintse meg a következő cikket:

* [A szolgáltatás jelzők használata az ASP.NET Core-alkalmazás](./use-feature-flags-dotnet-core.md)
