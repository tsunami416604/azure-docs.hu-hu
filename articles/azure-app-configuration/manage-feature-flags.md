---
title: 'Oktatóanyag: Az Azure App Configuration használatával kezelheti a szolgáltatásjelzőket'
titleSuffix: Azure App Configuration
description: Ebben az oktatóanyagban megtudhatja, hogyan kezelheti a funkciójelzőket az alkalmazástól elkülönítve az Azure App Configuration használatával.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: ccab8014000f9f684249bf2c1f800f74c92e7369
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76899367"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Oktatóanyag: Szolgáltatásjelzők kezelése az Azure App konfigurációjában

Az Azure App Configuration alkalmazásban az összes funkciójelzőt tárolhatja, és egyetlen helyről felügyelheti őket. Az Alkalmazáskonfiguráció egy **Szolgáltatáskezelő** nevű portálfelhasználói felületet tartalmaz, amelyet kifejezetten a szolgáltatásjelzőkhöz terveztek. Az Alkalmazáskonfiguráció natív módon támogatja a .NET Core szolgáltatásjelző adatsémát is.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Szolgáltatásjelzők definiálása és kezelése az alkalmazás konfigurációjában.
> * Hozzáférés szolgáltatásjelzők az alkalmazásból.

## <a name="create-feature-flags"></a>Jellemzőjelzők létrehozása

Az Azure Portal for App Configuration szolgáltatáskezelő szolgáltatáskezelője felhasználói felületet biztosít az alkalmazásokban használt szolgáltatásjelzők létrehozásához és kezeléséhez.

Új szolgáltatásjelző hozzáadása:

1. A **Szolgáltatáskezelő** > **+Hozzáadás** lehetőséget választva hozzáadhat egy szolgáltatásjelzőt.

    ![Szolgáltatásjelzők listája](./media/azure-app-configuration-feature-flags.png)

1. Adja meg a szolgáltatásjelző egyedi kulcsnevét. Erre a névre szüksége van ahhoz, hogy a kódban szereplő zászlóra hivatkozzon.

1. Ha szeretné, adjon leírást a funkciójelzőnek.

1. Állítsa be a szolgáltatásjelző kezdeti állapotát. Ez az állapot általában *Ki* vagy *Be*. Ha szűrőt ad hozzá a szolgáltatásjelzőhöz, a *Be* állapot *feltételesre* változik.

    ![Jellemzőjelző létrehozása](./media/azure-app-configuration-feature-flag-create.png)

1. Ha az állapot be van *kapcsolva,* válassza a **+Szűrő hozzáadása** lehetőséget az állapot minősítéséhez szükséges további feltételek megadásához. Adjon meg egy beépített vagy egyéni szűrőkulcsot, majd válassza a **+Add paraméter** lehetőséget egy vagy több paraméter nek a szűrőhöz társításához. A beépített szűrők a következők:

    | Kulcs | JSON-paraméterek |
    |---|---|
    | Microsoft.Százalék | {"Érték": 0-100 százalék} |
    | Microsoft.TimeWindow ablak | {"Start": UTC idő, "Befejezés": UTC idő} |

    ![Jellemzőjelző szűrője](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Szolgáltatásjelző állapotának frissítése

A jellemzőjelző állapotértékének módosítása:

1. Válassza **a Szolgáltatáskezelő**lehetőséget.

1. A módosítani kívánt jellemzőjelzőjobb oldalán jelölje ki a három pontot (**...**), majd a **Szerkesztés parancsot.**

1. Állítson be egy új állapotot a szolgáltatásjelzőhez.

## <a name="access-feature-flags"></a>Az Access szolgáltatásjelzői

A Szolgáltatáskezelő által létrehozott szolgáltatásjelzők tárolása és beolvasása rendszeres kulcsértékként lesz tárolva és beolvasva. Egy speciális névtér előtag `.appconfig.featureflag`alatt vannak. Az alapul szolgáló kulcsértékek megtekintéséhez használja a Configuration Explorer t. Az alkalmazás lekérheti ezeket az értékeket az alkalmazáskonfiguráció-szolgáltatók, SDK-k, parancssori bővítmények és REST API-k használatával.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan kezelheti a funkciójelzőket és azok állapotát az Alkalmazáskonfiguráció használatával. Az alkalmazáskonfiguráció és a ASP.NET Core szolgáltatáskezelési támogatásáról az alábbi cikkben olvashat bővebben:

* [Szolgáltatásjelzők használata ASP.NET Core alkalmazásban](./use-feature-flags-dotnet-core.md)
