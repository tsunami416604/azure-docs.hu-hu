---
title: Szolgáltatás-szűrők használata a szolgáltatások egy részhalmaza számára történő engedélyezéséhez
titleSuffix: Azure App Configuration
description: Ismerje meg, hogyan használhatók a funkciók a felhasználók egy részhalmaza számára a funkciók használatával
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/9/2020
ms.author: lcozzens
ms.openlocfilehash: b01a22d5a7068ee49e718a66432f52a8f6ef02ac
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126508"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>Szolgáltatás-szűrők használata a szolgáltatások egy részhalmaza számára történő engedélyezéséhez

A funkciók jelzői lehetővé teszik az alkalmazás funkcióinak aktiválását vagy inaktiválását. Egy egyszerű funkció jelzője be vagy ki van kapcsolva. Az alkalmazás mindig ugyanúgy viselkedik. Létrehozhat például egy új funkciót a szolgáltatás jelölője mögött. Ha a funkció jelzője engedélyezve van, az összes felhasználó látja az új funkciót. A funkció jelző letiltása elrejti az új funkciót.

Ezzel szemben a _feltételes funkciók jelzője_ lehetővé teszi, hogy a szolgáltatás jelölője dinamikusan legyen engedélyezve vagy letiltva. Az alkalmazás a szolgáltatás jelző feltételeitől függően eltérően működhet. Tegyük fel, hogy először a felhasználók kis részhalmazára szeretné megjeleníteni az új funkciót. A feltételes funkciók jelzője lehetővé teszi, hogy egyes felhasználók számára engedélyezze a szolgáltatás jelölőjét, miközben mások számára letiltja azt. A _szolgáltatási szűrők_ határozzák meg a szolgáltatás jelölője állapotát minden egyes kiértékeléskor.

A `Microsoft.FeatureManagement`-függvénytár két szolgáltatási szűrőt tartalmaz:

- `PercentageFilter` engedélyezi a szolgáltatás jelölőjét a százalék alapján.
- `TimeWindowFilter` engedélyezi a szolgáltatás jelzőjét egy adott időszakban.

Létrehozhat egy saját szolgáltatáskészlet-szűrőt is, amely megvalósítja a [Microsoft. FeatureManagement. IFeatureFilter felületet](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Szolgáltatás szűrőjének regisztrálása

A szolgáltatás szűrőjét a `AddFeatureFilter` metódus meghívásával regisztrálhatja, és megadhatja a szolgáltatáskészlet nevét. A következő kód például regisztrálja `PercentageFilter`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Szolgáltatás-szűrő konfigurálása az Azure-alkalmazás konfigurációjában

Egyes szolgáltatási szűrők további beállításokkal rendelkeznek. `PercentageFilter` például a szolgáltatás egy százalék alapján aktiválja a szolgáltatást. Ez a beállítás határozza meg a használni kívánt százalékos arányt.

Ezeket a beállításokat konfigurálhatja az Azure-alkalmazások konfigurációjában definiált funkciók jelzői számára. Az alábbi lépéseket követve például a következő `PercentageFilter` lépésekkel engedélyezheti a szolgáltatások 50%-át a webalkalmazások számára:

1. Kövesse a gyors üzembe helyezési útmutató [: szolgáltatás-jelzők hozzáadása egy ASP.net Core alkalmazáshoz](./quickstart-feature-flag-aspnet-core.md) webalkalmazás létrehozásához a szolgáltatás jelölőjét.

1. A Azure Portal nyissa meg a konfigurációs tárolót, és kattintson a **Feature Manager**elemre.

1. Kattintson a rövid útmutatóban létrehozott *béta* -szolgáltatás jelölő helyi menüjére. Kattintson a **Szerkesztés** gombra.

    > [!div class="mx-imgBorder"]
    > ![a bétaverzió funkciójának jelölőjét](./media/edit-beta-feature-flag.png)

1. A **Szerkesztés** képernyőn válassza a on Radio ( **bekapcsolva** ) gombot, ha még nincs kiválasztva. Ezután kattintson a **szűrő hozzáadása** gombra. (Az **on** Radio gomb címkéje olvasási **feltételesre**változik.)

1. A **kulcs** mezőben adja meg a *Microsoft. százalék*értéket.

    > [!div class="mx-imgBorder"]
    > ![szolgáltatás-szűrő hozzáadása](./media/feature-flag-add-filter.png)

1. Kattintson a funkció szűrő kulcs melletti helyi menüre. Kattintson a **Paraméterek szerkesztése**elemre.

    > [!div class="mx-imgBorder"]
    > ![szerkesztheti a szolgáltatási szűrő paramétereit](./media/feature-flag-edit-filter-parameters.png)

1. Vigye a kurzort a **név** fejléc alá, hogy a szövegmezők megjelenjenek a rácsban. Adja meg az *érték* **nevét** és a 50 **értéket** . Az **érték** mező jelzi azon kérelmek százalékos arányát, amelyek esetében engedélyezni szeretné a szolgáltatás szűrőjét.

    > [!div class="mx-imgBorder"]
    > ![a szolgáltatási szűrő paramétereinek beállítása](./media/feature-flag-set-filter-parameters.png)

1. Kattintson az **alkalmaz** gombra a **funkció-jelölő szerkesztése** képernyőre való visszatéréshez. Ezután kattintson ismét az **alkalmaz** gombra a szolgáltatás jelölő beállításainak mentéséhez.

1. A szolgáltatás jelző **állapota** most *feltételesként*jelenik meg. Ez az állapot azt jelzi, hogy a szolgáltatás jelölője a szolgáltatás szűrője által kényszerített feltételek alapján lesz engedélyezve vagy letiltva a kérelmek alapján.

    > [!div class="mx-imgBorder"]
    > ![feltételes szolgáltatás jelzője](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>Funkciós szűrők működés közben

A szolgáltatás jelző hatásainak megtekintéséhez indítsa el az alkalmazást, és a böngészőben többször kattintson a **frissítés** gombra. Láthatja, hogy a *bétaverzió* az idő 50%-ában megjelenik az eszköztáron. A rendszer elrejti a hátralévő időt, mert a `PercentageFilter` inaktiválja a *Beta* funkciót a kérelmek egy részhalmaza számára. A következő videó mutatja ezt a viselkedést működés közben.

> [!div class="mx-imgBorder"]
> ![PercentageFilter a művelet](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A szolgáltatások kezelése – áttekintés](./concept-feature-management.md)