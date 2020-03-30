---
title: Csempék hozzáadása az irányítópulthoz | Microsoft dokumentumok
description: Építőként megtudhatja, hogyan konfigurálhatja az azure IoT Central alapértelmezett alkalmazásirányítópultját.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 49b41715d95a5f210e6e70faf09aa016d1478728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158755"
---
# <a name="configure-the-application-dashboard"></a>Az alkalmazás-irányítópult konfigurálása

Az **irányítópult** az a lap, amely betöltődik, amikor az alkalmazáshoz hozzáféréssel rendelkező felhasználók az alkalmazás URL-címére navigálnak. Ha az alkalmazást az **alkalmazássablonok**egyikéből hozta létre, az alkalmazás egy előre definiált irányítópultot fog létrehozni. Ha az alkalmazást az **Örökölt alkalmazásalkalmazás** sablonból hozta létre, az irányítópult üres lesz a kezdéshez.

> [!NOTE]
> A felhasználók az alapértelmezett alkalmazás-irányítópulton kívül [több irányítópultot](howto-create-personal-dashboards.md) is létrehozhatnak. Ezek az irányítópultok lehetnek személyes, hogy a felhasználó csak, vagy megosztott az alkalmazás összes felhasználója számára. 

## <a name="add-tiles"></a>Csempék hozzáadása

A következő képernyőképen az **egyéni alkalmazássablonból** létrehozott alkalmazás irányítópultja látható. Az alkalmazás alapértelmezett irányítópultjának testreszabásához válassza a lap bal felső részén található **Szerkesztés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Alkalmazások irányítópultja a "Minta contoso" sablon alapján](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

A **Szerkesztés** lehetőséget választva megnyílik az irányítópult könyvtárpanelje. A könyvtár tartalmazza az irányítópult testreszabásához használható csempéket és irányítópult-primitíveket.

> [!div class="mx-imgBorder"]
> ![Irányítópult-könyvtár](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

Hozzáadhat például egy **telemetriai** csempét az eszköz aktuális hőmérsékletéhez. Ehhez tegye a következőket:
1. **Eszközsablon** kiválasztása
1. Jelöljön ki egy **eszközpéldányt** az irányítópult csempéjén megtekinteni kívánt eszközhöz. Ezután megjelenik az eszköz csempén használható tulajdonságainak listája.
1. Ha létre szeretné hozni a csempét az irányítópulton, kattintson a **Hőmérséklet** gombra, és húzza az irányítópult területre. A **Hőmérséklet** gomb melletti jelölőnégyzetre, majd az **Egyesítés**gombra kattinthat. A következő képernyőképen egy eszközsablon és egy eszközpéldány kiválasztása látható, majd egy hőmérséklet-telemetriai csempe létrehozása az irányítópulton.
1. Válassza a **Mentés** gombot a bal felső sarokban, ha a csempét az irányítópultra szeretné menteni.

> [!div class="mx-imgBorder"]
> !["Az eszközrészletek konfigurálása" űrlap a beállítások és tulajdonságok részleteivel](media/howto-add-tiles-to-your-dashboard/device-details.png)

Most, amikor egy operátor megtekinti az alapértelmezett alkalmazás irányítópultját, az új csempét az eszköz **Hőmérséklet ével** látják. Minden csempéhez előre kiválasztott grafikon, diagram stb. A felhasználók azonban szerkeszthetik és módosíthatják ezt a vizualizációt. 

> [!div class="mx-imgBorder"]
> !["Irányítópult" lap a csempe megjelenített beállításaival és tulajdonságaival](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>Csempék szerkesztése

Az irányítópulton lévő csempe szerkesztéséhez először kattintson a lap bal felső részén található **Szerkesztés** gombra, amely megnyitja az irányítópult és annak csempéi szerkesztési módját. 

> [!div class="mx-imgBorder"]
> ![Irányítópult képernyő a kijelölt csempéhez aktivált szerkesztési móddal](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

Ezután kattintson a **Gear** ikonra a szerkesztendő csempe jobb felső sarkában. Itt szerkesztheti a csempe egyes aspektusait, beleértve a címét, a vizualizációját, az összesítést stb.

> [!div class="mx-imgBorder"]
> ![Legördülő menü a csempeösszesítési beállításokhoz](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

A diagram vizualizációját a mozaik **vonalzó** ikonjára kattintva is módosíthatja.

> [!div class="mx-imgBorder"]
> ![Legördülő menü a mozaikmegjelenítési beállításokhoz](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>Csempetípusok

Az alábbi táblázat összefoglalja a csempék használatát az Azure IoT Centralban:
 
| Csempe | Irányítópult | Leírás
| ----------- | ------- | ------- |
| Tartalom | Alkalmazás- és eszközkészlet-irányítópultok |A markdown által támogatott csempék olyan kattintható csempék, amelyek címsor- és leírásszöveget jelenítenek meg. Ezt a csempét hivatkozáscsempeként is használhatja, hogy a felhasználó az alkalmazáshoz kapcsolódó URL-címre navigálhasson.|
| Kép | Alkalmazás- és eszközkészlet-irányítópultok |A képcsempék egyéni képet jelenítenek meg, és kattinthatók. Képcsempe használatával grafikákat adhat az irányítópulthoz, és lehetővé teszi, hogy a felhasználó az alkalmazásnak megfelelő URL-címre navigáljon.|
| Címke | Alkalmazás-irányítópultok |A címkecsempék egyéni szöveget jelenítenek meg az irányítópulton. Megadhatja a szöveg méretét. Címkecsempével releváns információkat adhat hozzá az irányítópulthoz, például leírásokat, elérhetőségeket vagy súgót.|
| Térkép | Alkalmazás- és eszközkészlet-irányítópultok |A térképcsempék az eszköz helyét és állapotát jelenítik meg a térképen. Megjelenítheti például, hogy hol van az eszköz, és hogy be van-e kapcsolva a ventilátora.|
| Vonaldiagram | Alkalmazás- és eszköz-irányítópultok |A vonaldiagram-csempék egy adott időszakra vonatkozó összesítő mérési diagramot jelenítenek meg az eszközre vonatkozóan. Megjeleníthetünk például egy vonaldiagramot, amely egy eszköz átlagos hőmérsékletét és nyomását mutatja az elmúlt órában.|
| Sávdiagram | Alkalmazás- és eszköz-irányítópultok |A sávdiagram-csempék egy adott időszakra vonatkozó összesítő mérések diagramját jelenítik meg. Megjeleníthetünk például egy sávdiagramot, amely egy eszköz átlagos hőmérsékletét és nyomását mutatja az elmúlt órában.|
| Kördiagram | Alkalmazás- és eszközkészlet-irányítópultok |A kördiagram csempéi egy adott időszakra vonatkozó összesítő mérések diagramját jelenítik meg.|
| Hőtérkép | Alkalmazás- és eszközkészlet-irányítópultok |A Hőtérkép csempék az eszközkészletre vonatkozó, színként ábrázolt információkat jelenítik meg.|
| Eseményelőzmények | Alkalmazás- és eszköz-irányítópultok |Az Eseményelőzmények csempék egy adott időszakban jelenítik meg az eszköz eseményeit. Használhatja például egy eszköz összes hőmérséklet-változásának megjelenítésére az elmúlt órában.|
| Állam története | Alkalmazás- és eszköz-irányítópultok |Az állapotelőzmények csempéi egy adott időszak mérési értékeit jelenítik meg. Használhatja például egy eszköz hőmérsékleti értékeinek megjelenítésére az elmúlt órában.|
| KPI | Alkalmazás- és eszköz-irányítópultok | A KPI-csempék egy adott időszakra vonatkozó összesített telemetriai vagy eseménymérést jelenítenek meg. Segítségével például megjelenítheti az eszköz höz az elmúlt órában elért maximális hőmérsékletet.|
| Utolsó ismert érték | Alkalmazás- és eszköz-irányítópultok |Az utolsó ismert értékcsempék a telemetriai adatok vagy állapotmérés legújabb értékét jelenítik meg. Ezzel a csempével például megjelenítheti az eszköz legfrissebb hőmérséklet-, nyomás- és páratartalom-méréseit.|

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta az Azure IoT Central alapértelmezett alkalmazásirányítópultjának konfigurálását, [megtudhatja, hogyan hozhat létre személyes irányítópultot.](howto-create-personal-dashboards.md)
