---
title: Azure IoT Központi személyes irányítópultok létrehozása | Microsoft dokumentumok
description: Felhasználóként megtudhatja, hogyan hozhat létre és kezelhet személyes irányítópultokat.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: db886006ff5b9adf3de0932951f6cce4958e8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158129"
---
# <a name="create-and-manage-multiple-dashboards"></a>Több irányítópult létrehozása és kezelése

Az **irányítópult** az a lap, amely az alkalmazáshoz való első navigáláskor töltődik be. Az alkalmazás **egyik szerkesztője** határozza meg az összes felhasználó alapértelmezett alkalmazás-irányítópultját. Ezt az alapértelmezett irányítópultot lecserélheti a saját, személyre szabott alkalmazás-irányítópultjára. Több irányítópultja is lehet, amelyek különböző adatokat jelenítenek meg, és váltanak közöttük. 

Ha Ön az alkalmazás **rendszergazdája,** legfeljebb 10 alkalmazásszintű irányítópultot is létrehozhat, amelyeket megoszthat az alkalmazás többi felhasználójával. Csak **a rendszergazdák** hozhatnak létre, szerkeszthetik és törölhetik az alkalmazásszintű irányítópultokat. 

## <a name="create-dashboard"></a>Irányítópult létrehozása

A következő képernyőképen az **egyéni alkalmazássablonból** létrehozott alkalmazás irányítópultja látható. Az alapértelmezett alkalmazás-irányítópultot lecserélheti egy személyes irányítópultra, vagy ha ön rendszergazda, egy másik alkalmazásszintű irányítópultra. Ehhez válassza a lap bal felső részén található **+ Új** lehetőséget.
 
> [!div class="mx-imgBorder"]
> ![Az "Egyéni alkalmazás" sablonon alapuló alkalmazások irányítópultja](media/howto-create-personal-dashboards/dashboard-custom-app.png)

A **+ Új** lehetőség kiválasztásával megnyílik a dashboard szerkesztő. A szerkesztőben nevet adhat az irányítópultnak, és a tárból kiválasztott elemeket választhat. A könyvtár tartalmazza az irányítópult testreszabásához használható csempéket és irányítópult-primitíveket.

> [!div class="mx-imgBorder"]
> ![Irányítópult-könyvtár](media/howto-create-personal-dashboards/dashboard-library.png)

Ha Ön az alkalmazás **rendszergazdája,** akkor lehetősége van a váltásra, ha személyes szintű irányítópultot vagy alkalmazásszintű irányítópultot szeretne létrehozni. Ha személyes szintű irányítópultot hoz létre, csak Ön láthatja azt. Ha létrehoz egy alkalmazásszintű irányítópultot, az alkalmazás minden felhasználója láthatja azt. Miután megadta a címet, és kiválasztotta a létrehozni kívánt irányítópult típusát, később mentheti és hozzáadhatja a csempéket. Vagy ha most már készen áll, és hozzáadott egy eszközsablont és egy eszközpéldányt, akkor előre és létrehozhatja az első csempét. 

> [!div class="mx-imgBorder"]
> ![Eszközrészletek konfigurálása" űrlap a Hőmérséklet részleteivel](media/howto-create-personal-dashboards/device-details.png)

Hozzáadhat például egy **telemetriai** csempét az eszköz aktuális hőmérsékletéhez. Ehhez tegye a következőket:
1. **Eszközsablon** kiválasztása
1. Jelöljön ki egy **eszközpéldányt** az irányítópult csempéjén megtekinteni kívánt eszközhöz. Ezután megjelenik az eszköz csempén használható tulajdonságainak listája.
1. Ha létre szeretné hozni a csempét az irányítópulton, kattintson a **Hőmérséklet** gombra, és húzza az irányítópult területre. A **Hőmérséklet** gomb melletti jelölőnégyzetre, majd az **Egyesítés**gombra kattinthat. A következő képernyőképen egy eszközsablon és egy eszközpéldány kiválasztása látható, majd egy hőmérséklet-telemetriai csempe létrehozása az irányítópulton.
1. Válassza a **Mentés** gombot a bal felső sarokban, ha a csempét az irányítópultra szeretné menteni.

> [!div class="mx-imgBorder"]
> ![Irányítópult" lap a Hőmérséklet csempe részleteivel](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Most, amikor megtekinti a személyes irányítópultot, megjelenik az új csempe az eszköz **Hőmérséklet** beállításával:

> [!div class="mx-imgBorder"]
> ![Irányítópult" lap a Hőmérséklet csempe részleteivel](media/howto-create-personal-dashboards/temperature-tile-complete.png)

A tárban található más csempetípusokat is felfedezheti, így derítheti ki, hogyan szabhatja testre a személyes irányítópultokat.

Ha többet szeretne tudni arról, hogyan használhatja a csempéket az Azure IoT Centralban, olvassa el a [Csempék hozzáadása az irányítópulthoz](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Irányítópultok kezelése

Több személyes irányítópultja is lehet, és válthat közöttük, vagy választhat az alapértelmezett alkalmazás-irányítópultok közül:

> [!div class="mx-imgBorder"]
> ![Váltás az irányítópultok között](media/howto-create-personal-dashboards/switch-dashboards.png)

Szerkesztheti személyes irányítópultjait, és törölheti azokat az irányítópultokat, amelyekre már nincs szüksége. Ha Ön **rendszergazda**, akkor is képes arra, hogy szerkesztse vagy törölje az alkalmazásszintű irányítópultokat is.

> [!div class="mx-imgBorder"]
> ![Irányítópultok törlése](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan hozhat létre és kezelhet személyes irányítópultokat, [megtudhatja, hogyan kezelheti az alkalmazásbeállításait](howto-manage-preferences.md)
