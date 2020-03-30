---
title: Szolgáltatásszűrők használata a felhasználók egy részhalmazának engedélyezéséhez
titleSuffix: Azure App Configuration
description: Útmutató a szolgáltatásszűrők használatáról a felhasználók egy részhalmazának engedélyezésére
ms.service: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 181c97615985283011834dcf9145810b1563fb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057003"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Szolgáltatásszűrők használata a felhasználók egy részhalmazának engedélyezéséhez

A funkciójelzők lehetővé teszik az alkalmazás funkcióinak aktiválását vagy inaktiválását. Egy egyszerű funkciójelző be van kapcsolva vagy ki van kapcsolva. Az alkalmazás mindig ugyanúgy viselkedik. Például egy új funkciót is bevezethet egy funkciójelző mögött. Ha a szolgáltatásjelző engedélyezve van, minden felhasználó láthatja az új funkciót. A jellemzőjelző letiltása elrejti az új funkciót.

Ezzel szemben a _feltételes szolgáltatásjelző_ lehetővé teszi a szolgáltatásjelző dinamikus engedélyezését vagy letiltását. Az alkalmazás a jellemzőjelző-feltételektől függően eltérően viselkedhet. Tegyük fel, hogy először a felhasználók egy kis részhalmazának szeretné megjeleníteni az új funkciót. A feltételes szolgáltatásjelző lehetővé teszi, hogy egyes felhasználók számára engedélyezze a szolgáltatásjelzőt, miközben mások számára letiltja azt. _A szolgáltatásszűrők_ határozzák meg a szolgáltatásjelző állapotát minden alkalommal, amikor kiértékelik.

A `Microsoft.FeatureManagement` könyvtár két jellemzőszűrőt tartalmaz:

- `PercentageFilter`engedélyezi a szolgáltatásjelzőt százalék alapján.
- `TimeWindowFilter`engedélyezi a szolgáltatásjelzőt egy megadott időalatt.

Létrehozhat saját szolgáltatásszűrőt is, amely megvalósítja a [Microsoft.FeatureManagement.IFeatureFilter felületet.](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)

## <a name="registering-a-feature-filter"></a>Szolgáltatásszűrő regisztrálása

A szolgáltatásszűrőt a `AddFeatureFilter` metódus hívásával, a szolgáltatásszűrő nevének megadásával regisztrálhatja. Például a következő kódregiszterek: `PercentageFilter`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Szolgáltatásszűrő konfigurálása az Azure App konfigurációjában

Egyes szolgáltatásszűrők további beállításokkal rendelkeznek. Például `PercentageFilter` egy tulajdonságot egy százalék alapján aktivál. Van egy beállítása, amely meghatározza a használni használandó százalékot.

Ezeket a beállításokat az Azure App konfigurációjában definiált szolgáltatásjelzők konfigurálhatja. Az alábbi lépésekkel `PercentageFilter` például engedélyezheti a webalkalmazásba irányuló kérelmek 50%-ának funkciójelzőjét:

1. Kövesse a [rövid útmutató utasításait: Szolgáltatásjelzők hozzáadása egy ASP.NET Core alkalmazáshoz](./quickstart-feature-flag-aspnet-core.md) egy funkciójelzővel rendelkező webalkalmazás létrehozásához.

1. Az Azure Portalon nyissa meg a konfigurációs tárolót, és kattintson a **Szolgáltatáskezelő**elemre.

1. Kattintson a rövid útmutatóban létrehozott *Béta* funkciójelző helyi menüjére. Kattintson a **Szerkesztés** gombra.

    > [!div class="mx-imgBorder"]
    > ![Béta funkciójelző szerkesztése](./media/edit-beta-feature-flag.png)

1. A **Szerkesztés** képernyőn jelölje be a **Be választógombot,** ha még nincs kijelölve. Ezután kattintson a **Szűrő hozzáadása** gombra. (A **Bekapcsol** választógomb címkéje **feltételesre**változik.)

1. A **Kulcs** mezőbe írja be a *Microsoft.Percentage*értéket.

    > [!div class="mx-imgBorder"]
    > ![Szolgáltatásszűrő hozzáadása](./media/feature-flag-add-filter.png)

1. Kattintson a jellemzőszűrőkulcs melletti helyi menüre. Kattintson **a Paraméterek szerkesztése gombra.**

    > [!div class="mx-imgBorder"]
    > ![Jellemzőszűrő-paraméterek szerkesztése](./media/feature-flag-edit-filter-parameters.png)

1. Mutasson a **Név** fejléc alatt, hogy a szövegdobozok megjelenjenek a rácsban. Adjon meg egy *érték* **és** egy 50 **értékű értéket.** Az **Érték** mező azt jelzi, hogy a kérelmek hány százaléka engedélyezi a jellemzőszűrőt.

    > [!div class="mx-imgBorder"]
    > ![Szolgáltatásszűrő paramétereinek beállítása](./media/feature-flag-set-filter-parameters.png)

1. Kattintson az **Alkalmaz** gombra, ha vissza szeretne térni a **Szerkesztés funkciójelző** képernyőre. Ezután kattintson ismét az **Alkalmaz** gombra a szolgáltatásjelző beállításainak mentéséhez.

1. A **szolgáltatásjelző állapota** *mostantól feltételesként*jelenik meg. Ez az állapot azt jelzi, hogy a szolgáltatásjelző kérésenként engedélyezve lesz vagy le lesz tiltva a szolgáltatásszűrő által kényszerített feltételek alapján.

    > [!div class="mx-imgBorder"]
    > ![Feltételes szolgáltatás jelzője](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Szolgáltatásszűrők működés közben

A funkciójelző hatásának megtekintéséhez indítsa el az alkalmazást, és nyomja meg többször a **Frissítés** gombot a böngészőben. Látni fogja, hogy a *Béta* elem az idő 50%-ában megjelenik az eszköztáron. Az idő többi részében el van `PercentageFilter` rejtve, mert a *béta* funkció inaktívvá válik a kérelmek egy részhalmazához. Az alábbi videó ezt a működésközbeni viselkedést mutatja be.

> [!div class="mx-imgBorder"]
> ![PercentageFilter működés közben](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szolgáltatáskezelés – áttekintés](./concept-feature-management.md)
