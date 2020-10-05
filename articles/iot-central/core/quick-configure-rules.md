---
title: Gyors útmutató – szabályok és műveletek konfigurálása az Azure IoT Central
description: Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a telemetria szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 66c3bd8650d1194d5d753c1dc967ec8e870c8748
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "80998968"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Rövid útmutató: Az eszközre vonatkozó szabályok és műveletek konfigurálása az Azure IoT Centralban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ebben a rövid útmutatóban egy olyan szabályt hoz létre, amely e-mailt küld, ha az eszköz érzékelője által jelentett hőmérséklet meghaladja a 90 F-ot &deg; .

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre a két előző rövid útmutató [Azure IoT Central-alkalmazás létrehozását](./quick-deploy-iot-central.md) , és [adjon hozzá egy szimulált eszközt a IoT Central alkalmazáshoz](./quick-create-simulated-device.md) a **MXChip IoT fejlesztői készlet** -sablon létrehozásához.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Új telemetria-szabály az alkalmazáshoz való hozzáadásához a bal oldali ablaktáblán válassza a **szabályok**elemet.

1. Új szabály létrehozásához válassza a elemet **+** .

1. Adja meg a **környezeti hőmérsékletet** a szabály neveként.

1. A **megcélzott eszközök** szakaszban válassza a **MXChip IoT fejlesztői készlet** lehetőséget az eszköz sablonként. Ez a beállítás szűri azokat az eszközöket, amelyekre a szabály vonatkozik. A **+ szűrő**lehetőség kiválasztásával további szűrési feltételeket adhat hozzá.

1. A **feltételek** szakaszban megadhatja, hogy mi indítja el a szabályt. A következő információk segítségével határozhat meg egy olyan feltételt, amely a hőmérséklet telemetria:

    | Mező        | Érték            |
    | ------------ | ---------------- |
    | Mérés  | Hőmérséklet      |
    | Operátor     | nagyobb, mint  |
    | Érték        | 90               |

    További feltételek hozzáadásához válassza a **+ feltétel**lehetőséget.

    ![Szabály feltételének létrehozása](./media/quick-configure-rules/condition.png)

1. A szabály indításakor futtatandó e-mail-művelet hozzáadásához válassza a **+ e-mail**lehetőséget.

1. A következő táblázatban található információk segítségével határozza meg a műveletet, majd válassza a **kész**lehetőséget:

    | Beállítás   | Érték                                             |
    | --------- | ------------------------------------------------- |
    | Megjelenített név | Kezelői e-mail művelet                          |
    | Művelet        | Az Ön e-mail-címe                                |
    | Jegyzetek     | A környezeti hőmérséklet túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Szabály létrehozása művelet](./media/quick-configure-rules/action.png)

1. Válassza a **Mentés** lehetőséget. A szabály a megjelenik a **Szabályok** oldalon.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon riasztásokat a Beérkezett üzenetek mappájában.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök monitorozásáról, folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> Az [Azure IoT Central segítségével figyelheti az eszközöket](quick-monitor-devices.md).
