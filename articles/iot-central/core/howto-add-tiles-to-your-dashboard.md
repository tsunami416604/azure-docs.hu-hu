---
title: Csempék hozzáadása az irányítópulthoz | Microsoft Docs
description: A Builder használatával megtudhatja, hogyan konfigurálhatja az alapértelmezett Azure IoT Central alkalmazás-irányítópultot.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b13349ae4293f6377429e9dc72b6c2cb43f92348
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435113"
---
# <a name="configure-the-application-dashboard"></a>Az alkalmazás irányítópultjának konfigurálása

Az **irányítópult** az a lap, amely akkor töltődik be, amikor az alkalmazáshoz hozzáférő felhasználók megnyitják az alkalmazás URL-címét. Ha az alkalmazást az egyik **alkalmazás-sablonból**hozta létre, akkor az alkalmazás előre definiált irányítópultot fog kezdeni. Ha az alkalmazást az **örökölt Application** Application-sablon alapján hozta létre, az irányítópult üresen fog indulni.

> [!NOTE]
> A felhasználók az alapértelmezett alkalmazás-irányítópulton kívül [több irányítópultot is létrehozhatnak](howto-create-personal-dashboards.md) . Ezek az irányítópultok csak a felhasználó személyes tagjai, vagy az alkalmazás összes felhasználója között megoszthatók. 

## <a name="add-tiles"></a>Csempék hozzáadása

Az alábbi képernyőfelvételen az **egyéni alkalmazás** sablonjában létrehozott alkalmazás irányítópultja látható. Az alkalmazás alapértelmezett irányítópultjának testreszabásához válassza a lap bal felső sarkában található **Szerkesztés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![irányítópult a "contoso" sablonon alapuló alkalmazásokhoz](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

A **Szerkesztés** lehetőség kiválasztásával megnyílik az irányítópult könyvtára panel. A függvénytár tartalmazza az irányítópult testreszabásához használható csempéket és irányítópult-primitíveket.

> [!div class="mx-imgBorder"]
> ![irányítópult-függvénytár](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Hozzáadhat például egy **telemetria** csempét az eszköz aktuális hőmérsékletéhez. Ehhez tegye a következőket:
1. Válassza ki az **eszköz sablonját**
1. Válasszon ki egy, az irányítópult csempén megjeleníteni kívánt eszköz **példányát** . Ezután megjelenik az eszköz azon tulajdonságainak listája, amelyeket a csempén lehet használni.
1. Ha a csempét az irányítópulton szeretné létrehozni, kattintson a **hőmérséklet** elemre, és húzza azt az irányítópult-területen. Kattintson a **hőmérséklet** elem melletti jelölőnégyzetre, majd kattintson az **összekapcsolás**elemre. Az alábbi képernyőfelvételen egy eszköz sablonjának és az eszköz példányának kiválasztását, majd az irányítópulton egy hőmérséklet-telemetria-csempét kell létrehoznia.
1. A bal felső sarokban található **Mentés** elemre kattintva mentheti a csempét az irányítópulton.

> [!div class="mx-imgBorder"]
> !["az eszköz részleteinek konfigurálása" űrlap a beállítások és a tulajdonságok részleteivel](media/howto-add-tiles-to-your-dashboard/device-details.png)

Most, amikor egy operátor megtekinti az alapértelmezett alkalmazás-irányítópultot, az új csempe jelenik meg, amely az eszköz **hőmérsékletét** tartalmazza. Az egyes csempék előre kiválasztott gráftal, diagrammal, valamint a csempék létrehozásakor megjelenő táblázattal rendelkeznek. A felhasználók azonban módosíthatják és módosíthatják a vizualizációt. 

> [!div class="mx-imgBorder"]
> ![az "irányítópult" fület a csempe megjelenített beállításaival és tulajdonságaival](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)


## <a name="edit-tiles"></a>Csempék szerkesztése

Az irányítópulton található csempe szerkesztéséhez először kattintson a lap bal felső részén található **Szerkesztés** gombra, amely megnyitja az irányítópult szerkesztési módját és az összes csempéjét. 

> [!div class="mx-imgBorder"]
> ![irányítópult-képernyő, amelyen a szerkesztési mód aktiválva van egy kiválasztott csempén](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Ezután kattintson a **fogaskerék** ikonra a szerkeszteni kívánt csempe jobb felső sarkában. Itt szerkesztheti a csempék aspektusait, beleértve a címét, a vizualizációját, az összesítést stb.

> [!div class="mx-imgBorder"]
> a csempék összesítési beállításainak ![legördülő lista](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

A diagram vizualizációját úgy is megváltoztathatja, hogy a csempén a **vonalzó** ikonra kattint.

> [!div class="mx-imgBorder"]
> ![legördülő lista a csempe vizualizációs beállításaihoz](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Csempe típusai

Az alábbi táblázat összefoglalja a csempék használatát az Azure IoT Centralban:
 
| Tile | Irányítópult | Leírás
| ----------- | ------- | ------- |
| Tartalom | Alkalmazások és eszközök beállítása irányítópultok |A Markdown által támogatott csempék a fejléc és a Leírás szövegét megjelenítő csempék. Ezt a csempét hivatkozás csempéként is használhatja, hogy a felhasználó az alkalmazáshoz kapcsolódó URL-címhez navigáljon.|
| Lemezkép | Alkalmazások és eszközök beállítása irányítópultok |A képcsempék egy egyéni rendszerképet jelenítenek meg, és megadhatók. Egy képcsempe használatával képeket adhat hozzá egy irányítópulthoz, és opcionálisan engedélyezheti a felhasználónak az alkalmazáshoz kapcsolódó URL-cím megkeresését.|
| Címke | Alkalmazás-irányítópultok |A felirat csempék egyéni szöveget jelenítenek meg az irányítópulton. Megadhatja a szöveg méretét. A címke csempével fontos információkat adhat hozzá az irányítópulthoz, például a leírásokat, a kapcsolattartási adatokat vagy a súgót.|
| Térkép | Alkalmazások és eszközök beállítása irányítópultok |A Térkép csempéi az eszközök helyét és állapotát jelenítik meg. Megjelenítheti például az eszköz helyét, illetve azt, hogy a ventilátor be van-e kapcsolva.|
| Vonaldiagram | Alkalmazás-és eszköz-irányítópultok |A vonalas diagram csempéi egy adott időszakra vonatkozóan egy adott eszköz összesített mértékének diagramját jelenítik meg. Megjeleníthet például egy olyan diagramot, amely az elmúlt órában egy eszköz átlagos hőmérsékletét és terhelését jeleníti meg.|
| Sávdiagram | Alkalmazás-és eszköz-irányítópultok |A oszlopdiagram csempéi egy adott időszakra vonatkozó összesített mérési diagramot jelenítenek meg. Megjeleníthet például egy olyan sávdiagram, amely az elmúlt órában egy eszköz átlagos hőmérsékletét és terhelését jeleníti meg.|
| Tortadiagram | Alkalmazások és eszközök beállítása irányítópultok |A tortadiagram csempéi egy adott időszakra vonatkozó összesített mérési diagramot jelenítenek meg.|
| Hőtérkép | Alkalmazások és eszközök beállítása irányítópultok |A Heat Map csempék megjelenítik az eszközbeállítások adatait, amelyek színként jelennek meg.|
| Eseményelőzmények | Alkalmazás-és eszköz-irányítópultok |Az esemény előzményei csempék az adott időszakon belül megjelenítik az adott eszköz eseményeit. Használhatja például az eszköz összes hőmérséklet-változásának megjelenítésére az elmúlt órában.|
| Állapotelőzmények | Alkalmazás-és eszköz-irányítópultok |Az állapot előzményei csempék egy adott időszak mérési értékeit jelenítik meg. Használhatja például az eszköz hőmérsékleti értékének megjelenítésére az elmúlt órában.|
| KPI | Alkalmazás-és eszköz-irányítópultok | A KPI-csempék összesített telemetria vagy az esemény mértékét jelenítik meg egy adott időszakra vonatkozóan. Használhatja például az eszköz utolsó órájában elérhető maximális hőmérséklet megjelenítését.|
| Utolsó ismert érték | Alkalmazás-és eszköz-irányítópultok |Az utolsó ismert érték csempék a telemetria vagy az állapot mérésének legújabb értékét jelenítik meg. Ezzel a csempével például megjelenítheti az eszközök hőmérsékletének, nyomásának és nedvességtartalmának legutóbbi méréseit.|

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan konfigurálhatja az Azure IoT Central alapértelmezett alkalmazás-irányítópultját, [megtudhatja, hogyan készítheti elő és töltheti fel a lemezképeket](howto-prepare-images.md).