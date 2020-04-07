---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673984"
---
## <a name="create-a-device-template"></a>Eszközsablon létrehozása

Hozzon létre `environmental-sensor` egy mappát a helyi számítógépen.

Töltse le a [Környezetvédelmi érzékelő képesség modell](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) `environmental-sensor` JSON fájlt, és mentse el a mappába.

A letöltött fájlban a két `{YOUR_COMPANY_NAME_HERE}` példányt szövegszerkesztővel `EnvironmentalSensorInline.capabilitymodel.json` cserélheti le a vállalat nevére.

Az Azure IoT Central alkalmazásban hozzon létre egy környezeti `EnvironmentalSensorInline.capabilitymodel.json` *érzékelő* nevű eszközsablont az eszközképességi modellfájl importálásával:

![Eszközsablon importált eszközképesség-modellel](./media/iot-central-add-environmental-sensor/device-template.png)

Az eszközképességi modell két interfészt tartalmaz: a szabványos **eszközinformációs interfészt** és az egyéni **környezeti érzékelő** interfészt. A **környezeti érzékelő** felület a következő képességeket határozza meg:

| Típus | Megjelenítendő név | Leírás |
| ---- | ------------ | ----------- |
| Tulajdonság | Eszközállapot     | Az eszköz állapota. Két állam online / offline állnak rendelkezésre. |
| Tulajdonság (írható) | Vevő neve    | Az eszközt jelenleg üzemeltető ügyfél neve. |
| Tulajdonság (írható) | Fényerő szint | A készülék fényerejének fényereje. Megadható 1 (magas), 2 (közepes), 3 (alacsony). |
| Telemetria | Hőmérséklet | A készülék által észlelt aktuális hőmérséklet. |
| Telemetria | Páratartalom    | A készülék által észlelt aktuális páratartalom. |
| Parancs | Pislogás          | Kezdje el villogni a készülék LED-jét az adott időintervallumban. |
| Parancs | fordulat         | Kapcsolja be a LED-et a készüléken. |
| Parancs | Kanyart        | Kapcsolja ki a készülék LED-jét. |
| Parancs | rundiagnostics (diagnosztika) | Ez az aszinkron parancs elindítja a diagnosztika futtatását az eszközön. |

Ha testre szeretné szabni, hogy az **Eszközállapot** tulajdonság hogyan jelenjen meg az IoT Central alkalmazásban, válassza a **Testreszabás lehetőséget** az eszközsablonban. Bontsa ki az **Eszközállapot-bejegyzést,** írja be az _Online_ nevet **Igaz névként,** offline nevet _pedig_ **hamis névként.** Ezután mentse a módosításokat:

![Az eszközsablon testreszabása](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Nézetek létrehozása

A nézetek lehetővé teszik az IoT Central alkalmazáshoz csatlakoztatott eszközökkel való interakciót. Rendelkezhet például telemetriai adatokat megjelenítő, tulajdonságokat megjelenítő nézeteket, valamint olyan nézetekkel, amelyek lehetővé teszik az írható és felhőalapú tulajdonságok szerkesztését. A nézetek egy eszközsablon részét képezik.

Ha néhány alapértelmezett nézetet szeretne hozzáadni a **Környezetérzékelő** eszközsablonhoz, keresse meg az eszközsablont, válassza a **Nézetek**lehetőséget, és válassza az **Alapértelmezett nézetek létrehozása csempét.** Győződjön meg **arról, hogy az Áttekintés** és a Be – **be** van **kapcsolva,** majd válassza **az Alapértelmezett irányítópult-nézet(ek) létrehozása lehetőséget.** Most már két alapértelmezett nézet van definiálva a sablonban.

A **Környezeti érzékelő** felület két írható tulajdonságot tartalmaz : Az ügyfél **neve** és **a fényerő szint**. Nézet létrehozásához a következő tulajdonságokat szerkesztheti:

1. Válassza a **Nézetek** lehetőséget, majd válassza az **Eszköz szerkesztése és** a felhőalapú adatok csempét.

1. Adja meg a _Tulajdonságok_ nevet űrlapnévként.

1. Válassza ki a **Fényerő szint** és **a Vevő neve** tulajdonságokat. Ezután válassza **a Hozzáadás szakaszlehetőséget.**

1. Mentse a módosításokat.

![Nézet hozzáadása a tulajdonságszerkesztés engedélyezéséhez](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>A sablon közzététele

A **Környezeti érzékelő** eszközsablont használó eszköz hozzáadása előtt közzé kell tennie azt.

Az eszközsablonban válassza a **Közzététel**lehetőséget. Az **Eszközsablon közzététele az alkalmazáspanelen** válassza a **Közzététel**lehetőséget.

Annak ellenőrzéséhez, hogy a sablon készen áll-e a használatra, keresse meg az **Eszközök** lapot az IoT Central alkalmazásban. Az **Eszközök** szakasz az alkalmazásban közzétett eszközök listáját jeleníti meg:

![Közzétett sablonok az eszközök lapon](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazásban adjon hozzá egy valódi eszközt az előző szakaszban létrehozott eszközsablonhoz:

1. Az **Eszközök** lapon válassza a **Környezeti érzékelő** eszköz sablont.

    > [!TIP]
    > Ügyeljen arra, hogy a + **Új**lehetőséget választja, különben létrehoz egy nem társított eszközt.

1. Válassza **a + Új**lehetőséget.

1. Győződjön meg arról, hogy a **Szimulált** ki van **kapcsolva.** Ezután válassza **a Létrehozás lehetőséget.**

Kattintson az eszköz nevére, majd a **Csatlakozás gombra.** Jegyezze fel az eszközkapcsolat adatait az **Eszközkapcsolat** lapon - **Azonosító hatókör**, **Eszközazonosító**és **Elsődleges kulcs**. Az eszközkód létrehozásakor ezekre az értékekre van szüksége:

![Eszközkapcsolat adatai](./media/iot-central-add-environmental-sensor/device-connection.png)
