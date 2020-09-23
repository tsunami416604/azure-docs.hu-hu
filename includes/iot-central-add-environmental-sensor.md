---
title: fájlbefoglalás
description: fájlbefoglalás
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002046"
---
## <a name="create-a-device-template"></a>Eszközsablon létrehozása

Hozzon létre egy nevű mappát a `environmental-sensor` helyi gépen.

Töltse le a [környezeti érzékelő képesség modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON-fájlját, és mentse a `environmental-sensor` mappába.

Szövegszerkesztővel cserélje le a `{YOUR_COMPANY_NAME_HERE}` vállalata nevével a letöltött fájlban lévő két példányt `EnvironmentalSensorInline.capabilitymodel.json` . Csak az a – z, A-Z, 0-9 és aláhúzás karaktereket használja.

Az Azure IoT Central alkalmazásban hozzon létre egy *környezeti érzékelő* nevű sablont az `EnvironmentalSensorInline.capabilitymodel.json` eszköz képességi modell fájljának importálásával:

![Eszköz sablonja az importált eszköz képességeinek modelljével](./media/iot-central-add-environmental-sensor/device-template.png)

Az eszköz képességeinek modellje két felületet tartalmaz: a szabványos **eszköz információs** felületét és az egyéni **környezeti érzékelő** felületét. A **környezeti érzékelő** felülete a következő képességeket határozza meg:

| Típus | Megjelenítendő név | Leírás |
| ---- | ------------ | ----------- |
| Tulajdonság | Eszközállapot     | Az eszköz állapota. Két állapot érhető el online/offline állapotban. |
| Tulajdonság (írható) | Ügyfél neve    | Az eszközt jelenleg működtető ügyfél neve. |
| Tulajdonság (írható) | Fényerő szintje | Az eszköz fényének fényerő-szintje. Megadható 1 (magas), 2 (közepes), 3 (alacsony). |
| Telemetria | Hőmérséklet | Az eszköz által észlelt aktuális hőmérséklet. |
| Telemetria | Páratartalom    | Az eszköz által észlelt jelenlegi páratartalom. |
| Parancs | kurzorvillogás          | A LED megkezdése az eszközön az adott időintervallumon belül. |
| Parancs | turnon         | Kapcsolja be a LED-t az eszközön. |
| Parancs | kanyart        | Kapcsolja ki a LED-t az eszközön. |
| Parancs | rundiagnostics | Ez az aszinkron parancs elindítja a diagnosztika futtatását az eszközön. |

Ha testre szeretné szabni, hogyan jelenjen meg az **eszköz állapota** tulajdonság a IoT Central alkalmazásban, válassza a **Testreszabás** lehetőséget az eszköz sablonjában. Bontsa ki az **eszköz állapota** bejegyzést, adja meg az _online_ értéket a **valódi név** és az _Offline_ értékként a **hamis név**mezőben. Ezután mentse a módosításokat:

![Sablon testreszabása](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Nézetek létrehozása

A nézetek lehetővé teszik a IoT Central alkalmazáshoz csatlakoztatott eszközök kezelését. Megtekintheti például a telemetria megjelenítő nézeteket, a tulajdonságokat megjelenítő nézeteket, valamint az írható és a felhő tulajdonságainak szerkesztését lehetővé teszi a nézeteket. A nézetek egy eszköz sablon részét képezik.

Ha néhány alapértelmezett nézetet szeretne hozzáadni a **környezeti érzékelő** eszköz sablonhoz, navigáljon az eszköz sablonhoz, válassza a **nézetek**lehetőséget, és válassza az **alapértelmezett nézetek előállítása** csempét. Győződjön meg arról, hogy az **Áttekintés** és a **információ** be van **kapcsolva**, majd válassza az **alapértelmezett irányítópult-nézet (ek) előállítása**lehetőséget. Most már két alapértelmezett nézet van definiálva a sablonban.

A **környezeti érzékelő** felülete két írható tulajdonságot tartalmaz – az **ügyfél nevét** és a **fényerő szintjét**. Nézet létrehozásához a következő tulajdonságokat használhatja:

1. Válassza a **nézetek** lehetőséget, majd válassza ki az **eszköz és a Felhőbeli adatcsempe szerkesztését** .

1. Adja meg a _tulajdonságokat_ az űrlap neveként.

1. Válassza ki a **fényerő szintjét** és az **ügyfél neve** tulajdonságokat. Ezután válassza a **Hozzáadás szakaszt**.

1. Mentse a módosításokat.

![Nézet hozzáadása a tulajdonságok szerkesztésének engedélyezéséhez](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>A sablon közzététele

A **környezeti érzékelőt** használó eszköz hozzáadása előtt közzé kell tennie.

Az eszköz sablonjában válassza a **Közzététel**lehetőséget. Az **eszköz sablonjának közzététele az alkalmazás** panelen válassza a **Közzététel**lehetőséget.

A sablon használatra kész állapotának megtekintéséhez navigáljon az **eszközök** lapra a IoT Central alkalmazásban. Az **eszközök** szakasz az alkalmazás közzétett eszközeinek listáját jeleníti meg:

![Közzétett sablonok az eszközök lapon](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central alkalmazásban adjon hozzá egy valós eszközt az előző szakaszban létrehozott sablonhoz:

1. Az **eszközök** lapon válassza ki a **környezeti érzékelő** eszköz sablonját.

1. Válassza a **+ Új** lehetőséget.

1. Az **új eszköz létrehozása** párbeszédpanelen győződjön meg arról, hogy a **környezeti érzékelő** a sablon típusa, és hogy **szimulálja ezt az eszközt?** a **nem**értékre van állítva.

1. Ezután válassza a **Létrehozás** elemet.

Kattintson az eszköz nevére, majd válassza a **Csatlakoztatás**lehetőséget. Jegyezze fel az eszköz csatlakoztatására vonatkozó információkat az eszköz **csatlakoztatása** lapon – **azonosító hatókör**, **eszköz azonosítója**és **elsődleges kulcs**. Ezekre az értékekre szüksége lesz az eszköz kódjának létrehozásakor:

![Eszközkapcsolati adatok](./media/iot-central-add-environmental-sensor/device-connection.png)
