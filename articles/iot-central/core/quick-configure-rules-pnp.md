---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a telemetria szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 8d675a442ac5765d6739335ce768ea33e33decaf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957698"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Gyors útmutató: szabályok és műveletek konfigurálása az eszközhöz az Azure IoT Central (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ebben a rövid útmutatóban egy olyan szabályt hoz létre, amely e-mailt küld, ha a környezeti érzékelő eszköz hőmérséklete meghaladja a 90&deg; F számot.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre a két előző rövid útmutató [Azure IoT Central alkalmazás létrehozását](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) , és [adjon hozzá egy szimulált eszközt a IoT Central alkalmazáshoz](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a **környezeti érzékelő** eszköz sablonjának létrehozásához.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Új telemetria-szabály az alkalmazáshoz való hozzáadásához a bal oldali ablaktáblán válassza a **szabályok**elemet.

1. Új szabály létrehozásához válassza az **+ új**lehetőséget.

1. Adja meg a **környezeti hőmérsékletet** a szabály neveként.

1. A **cél eszközök** szakaszban válassza ki a **környezeti érzékelőt** eszköz sablonként. Ez a beállítás szűri azokat az eszközöket, amelyekre a szabály vonatkozik. További szűrési feltételeket adhat meg a **+ szűrő**lehetőség kiválasztásával.

1. A **feltételek** szakaszban megadhatja, hogy mi indítja el a szabályt. A következő információk segítségével határozhat meg egy olyan feltételt, amely a hőmérséklet telemetria:

    | Mező                                        | Value (Díj)                             |
    | -------------------------------------------- | ------------------------------    |
    | Mérés                                  | Hőmérséklet                       |
    | Művelet                                     | nagyobb, mint                   |
    | Value (Díj)                                        | 90                                |

    További feltételek hozzáadásához válassza a **+ feltétel**lehetőséget.

    ![Szabály feltételének létrehozása](./media/quick-configure-rules-pnp/condition.png)

1. A szabály indításakor futtatandó e-mail-művelet hozzáadásához válassza a **+ e-mail**lehetőséget.

1. A következő táblázatban található információk segítségével definiálhatja a műveletet:

    | Beállítás   | Value (Díj)                                             |
    | --------- | ------------------------------------------------- |
    | Megjelenített név | Kezelői e-mail művelet                          |
    | –        | Az Ön e-mail-címe                                |
    | Megjegyzések     | A környezeti hőmérséklet túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Szabály létrehozása művelet](./media/quick-configure-rules-pnp/action.png)

1. Kattintson a **Mentés** gombra. A szabály a **szabályok** lapon jelenik meg.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon riasztásokat a Beérkezett üzenetek mappájában.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök monitorozásáról, folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> Az [Azure IoT Central segítségével figyelheti az eszközöket](quick-monitor-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
