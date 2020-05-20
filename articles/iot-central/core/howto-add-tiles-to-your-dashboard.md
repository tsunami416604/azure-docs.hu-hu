---
title: Csempék hozzáadása az irányítópulthoz | Microsoft Docs
description: A Builder használatával megtudhatja, hogyan konfigurálhatja az alapértelmezett Azure IoT Central alkalmazás-irányítópultot.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659053"
---
# <a name="configure-the-application-dashboard"></a>Az alkalmazás irányítópultjának konfigurálása

Az **irányítópult** az a lap, amely akkor töltődik be, amikor az alkalmazáshoz hozzáférő felhasználók megnyitják az alkalmazás URL-címét. Ha az alkalmazást az egyik **alkalmazás-sablonból**hozta létre, akkor az alkalmazás előre definiált irányítópultot fog kezdeni. Ha **egyéni alkalmazás** -sablonból hozta létre az alkalmazást, az irányítópulton néhány tipp jelenik meg az első lépésekhez.

> [!NOTE]
> A felhasználók az alapértelmezett alkalmazás-irányítópulton kívül [több irányítópultot is létrehozhatnak](howto-create-personal-dashboards.md) . Ezek az irányítópultok csak a felhasználó személyes tagjai, vagy az alkalmazás összes felhasználója között megoszthatók.  

## <a name="add-tiles"></a>Csempék hozzáadása

Az alábbi képernyőfelvételen az **egyéni alkalmazás** sablonjában létrehozott alkalmazás irányítópultja látható. Az alkalmazás alapértelmezett irányítópultjának testreszabásához válassza a lap bal felső sarkában található **Szerkesztés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Az alkalmazások irányítópultja a "minta contoso" sablon alapján](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

A **Szerkesztés** lehetőség kiválasztásával megnyílik az irányítópult könyvtára panel. A függvénytár tartalmazza az irányítópult testreszabásához használható csempéket és irányítópult-primitíveket.

> [!div class="mx-imgBorder"]
> ![Irányítópult-könyvtár](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Hozzáadhat például egy **telemetria** csempét az eszköz aktuális hőmérsékletéhez. Ehhez tegye a következőket:

1. Válassza ki az **eszköz sablonját**
1. Válasszon ki egy eszközt az **eszközök** közül az irányítópult csempén megjeleníteni kívánt eszközhöz. Ezután megjelenik az eszköz azon tulajdonságainak listája, amelyeket a csempén lehet használni.
1. Ha a csempét az irányítópulton szeretné létrehozni, kattintson a **hőmérséklet** elemre, és húzza azt az irányítópult-területen. Kattintson a **hőmérséklet** elem melletti jelölőnégyzetre, majd kattintson a **csempe hozzáadása**lehetőségre. Az alábbi képernyőfelvételen egy eszköz sablonjának és az eszköz példányának kiválasztását, majd az irányítópulton egy hőmérséklet-telemetria-csempét kell létrehoznia.
1. A bal felső sarokban található **Mentés** elemre kattintva mentheti a csempét az irányítópulton.

> [!div class="mx-imgBorder"]
> !["Az eszköz részleteinek konfigurálása" űrlap a beállítások és a tulajdonságok részleteivel](media/howto-add-tiles-to-your-dashboard/device-details.png)

Most, amikor egy operátor megtekinti az alapértelmezett alkalmazás-irányítópultot, az új csempe jelenik meg, amely az eszköz **hőmérsékletét** tartalmazza. Az egyes csempék előre kiválasztott gráftal, diagrammal, valamint a csempék létrehozásakor megjelenő táblázattal rendelkeznek. A felhasználók azonban módosíthatják és módosíthatják a vizualizációt.  

> [!div class="mx-imgBorder"]
> !["Irányítópult" lap, amely megjeleníti a csempén látható beállításokat és tulajdonságokat](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Csempék szerkesztése

Az irányítópulton található csempe szerkesztéséhez először kattintson a lap bal felső részén található **Szerkesztés** gombra, amely megnyitja az irányítópult szerkesztési módját és az összes csempéjét.  

> [!div class="mx-imgBorder"]
> ![Az irányítópult képernyője, amely a szerkesztési módot aktiválta egy kiválasztott csempén](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Ezután kattintson a **fogaskerék** ikonra a szerkeszteni kívánt csempe jobb felső sarkában. Itt szerkesztheti a csempék aspektusait, beleértve a címét, a vizualizációját, az összesítést stb.

> [!div class="mx-imgBorder"]
> ![Csempe-összesítési beállítások legördülő listája](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

A diagram vizualizációját úgy is megváltoztathatja, hogy a csempén a **vonalzó** ikonra kattint.

> [!div class="mx-imgBorder"]
> ![Csempe vizualizációs beállításainak legördülő listája](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Csempe típusai

Az alábbi táblázat összefoglalja a csempék használatát az Azure IoT Centralban:

| Csempe | Irányítópult | Description
| ----------- | ------- | ------- |
| Tartalom | Alkalmazások és eszközök beállítása irányítópultok |A Markdown által támogatott csempék a fejléc és a Leírás szövegét megjelenítő csempék. Ezt a csempét hivatkozás csempéként is használhatja, hogy a felhasználó az alkalmazáshoz kapcsolódó URL-címhez navigáljon.|
| Kép | Alkalmazások és eszközök beállítása irányítópultok |A képcsempék egy egyéni rendszerképet jelenítenek meg, és megadhatók. Egy képcsempe használatával képeket adhat hozzá egy irányítópulthoz, és opcionálisan engedélyezheti a felhasználónak az alkalmazáshoz kapcsolódó URL-cím megkeresését.|
| Címke | Alkalmazás-irányítópultok |A felirat csempék egyéni szöveget jelenítenek meg az irányítópulton. Megadhatja a szöveg méretét. A címke csempével fontos információkat adhat hozzá az irányítópulthoz, például a leírásokat, a kapcsolattartási adatokat vagy a súgót.|
| Térkép | Alkalmazás-és eszköz-irányítópultok |A Térkép csempéi megjelenítik egy eszköz helyét egy térképen. Akár 100 pontot is megjeleníthet egy eszköz tartózkodási előzményeiről. Megjelenítheti például a mintavételes útvonalat, ahol egy eszköz az elmúlt héten van.|
| Vonalas diagram | Alkalmazás-és eszköz-irányítópultok |A vonalas diagram csempéi egy adott időszakra vonatkozóan egy adott eszköz összesített mértékének diagramját jelenítik meg. Megjeleníthet például egy olyan diagramot, amely az elmúlt órában egy eszköz átlagos hőmérsékletét és terhelését jeleníti meg.|
| Oszlopdiagram | Alkalmazás-és eszköz-irányítópultok |A oszlopdiagram csempéi egy adott időszakra vonatkozó összesített mérési diagramot jelenítenek meg. Megjeleníthet például egy olyan sávdiagram, amely az elmúlt órában egy eszköz átlagos hőmérsékletét és terhelését jeleníti meg.|
| Tortadiagram | Alkalmazások és eszközök beállítása irányítópultok |A tortadiagram csempéi egy adott időszakra vonatkozó összesített mérési diagramot jelenítenek meg.|
| Hőtérkép | Alkalmazások és eszközök beállítása irányítópultok |A Heat Térkép csempéi a színként jelölt eszközről jelenítenek meg információkat.|
| Események előzményei | Alkalmazás-és eszköz-irányítópultok |Az esemény előzményei csempék az adott időszakon belül megjelenítik az adott eszköz eseményeit. Használhatja például az eszköz összes hőmérséklet-változásának megjelenítésére az elmúlt órában.|
| Korábbi állapotok | Alkalmazás-és eszköz-irányítópultok |Az állapot előzményei csempék egy adott időszak mérési értékeit jelenítik meg. Használhatja például az eszköz hőmérsékleti értékének megjelenítésére az elmúlt órában.|
| KPI | Alkalmazás-és eszköz-irányítópultok | A KPI-csempék összesített telemetria vagy az esemény mértékét jelenítik meg egy adott időszakra vonatkozóan. Használhatja például az eszköz utolsó órájában elérhető maximális hőmérséklet megjelenítését.|
| Utolsó ismert érték | Alkalmazás-és eszköz-irányítópultok |Az utolsó ismert érték csempék a telemetria vagy az állapot mérésének legújabb értékét jelenítik meg. Ezzel a csempével például megjelenítheti az eszközök hőmérsékletének, nyomásának és nedvességtartalmának legutóbbi méréseit. |
| Tulajdonság | Alkalmazás-és eszköz-irányítópultok | A tulajdonságok csempéi az eszköz tulajdonságainak és Felhőbeli tulajdonságainak aktuális értékét jelenítik meg. Ezt a csempét használhatja például az eszközök gyártói vagy belső vezérlőprogram-verziójának megjelenítéséhez. |

### <a name="customizing-visualizations"></a>Vizualizációk testreszabása

A vonalas diagramok, a sávdiagramok és a tortadiagramok esetében testre szabhatja a diagramon a különböző telemetriáiról megjelenített színeket. Ehhez válassza ki a testre szabni kívánt telemetria melletti szájpadlás ikont, és válasszon egy színt.

> [!div class="mx-imgBorder"]
> ![Telemetria Színmegjelenítési beállításainak legördülő listája](media/howto-add-tiles-to-your-dashboard/color-customization.png)

A telemetriáiról vagy a karakterlánc típusú tulajdonságok esetében kiválaszthatja, hogyan szeretné megjeleníteni a szöveget. Ha például az eszköz karakterlánc-telemetria küld egy URL-címet, az URL-címet kattintható hivatkozásként jelenítheti meg. Ha az URL-cím egy képre hivatkozik, az utolsó ismert érték vagy tulajdonság csempén jelenítheti meg a képet. A telemetria neve melletti fogaskerék kiválasztásával megváltoztathatja a karakterlánc telemetria megjelenítésének módját. Így szövegként, hivatkozásként vagy képként jelenítheti meg a szöveget.

> [!div class="mx-imgBorder"]
> ![Karakterlánc-vizualizációs beállítások legördülő listája](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan konfigurálhatja az Azure IoT Central alapértelmezett alkalmazás-irányítópultját, [megtudhatja, hogyan hozhat létre személyes irányítópultot](howto-create-personal-dashboards.md).
