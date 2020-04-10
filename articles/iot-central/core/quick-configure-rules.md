---
title: Rövid útmutató – Szabályok és műveletek konfigurálása az Azure IoT Centralban
description: Ez a rövid útmutató bemutatja, mint szerkesztő, hogyan konfigurálhatja a telemetriai alapú szabályok és műveletek az Azure IoT Central alkalmazás.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 66c3bd8650d1194d5d753c1dc967ec8e870c8748
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998968"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Rövid útmutató: Szabályok és műveletek konfigurálása az eszközhöz az Azure IoT Centralban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ebben a rövid útmutatóban hozzon létre egy szabályt, amely e-mailt&deg; küld, ha az eszközérzékelő által jelentett hőmérséklet meghaladja a 90 F-ot.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, el kell végeznie a két korábbi rövid útmutató [létrehozása egy Azure IoT Central-alkalmazás](./quick-deploy-iot-central.md) és [egy szimulált eszköz hozzáadása az IoT Central-alkalmazás](./quick-create-simulated-device.md) hozlétre az **MXChip IoT DevKit** eszközsablon dolgozni.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Ha új telemetriai alapú szabályt szeretne hozzáadni az alkalmazáshoz, a bal oldali ablaktáblában válassza a **Szabályok**lehetőséget.

1. Új szabály létrehozásához **+** válassza a lehetőséget.

1. Adja meg **a környezeti hőmérsékletet** szabálynévként.

1. A **Céleszközök** szakaszban válassza **az MXChip IoT DevKit** eszközsablonként lehetőséget. Ez a beállítás az eszközsablon típusa szerint szűri azeszközöket, amelyekre a szabály vonatkozik. További szűrési feltételeket a **+ Szűrő**lehetőség kiválasztásával adhat hozzá.

1. A **Feltételek szakaszban** meghatározhatja, hogy mi váltja ki a szabályt. A következő információk segítségével határozhat meg egy feltételt a hőmérséklet-telemetria imitorása alapján:

    | Mező        | Érték            |
    | ------------ | ---------------- |
    | Mérés  | Hőmérséklet      |
    | Művelet     | nagyobb, mint  |
    | Érték        | 90               |

    További feltételek hozzáadásához válassza **a + Feltétel**lehetőséget.

    ![Szabályfeltétel létrehozása](./media/quick-configure-rules/condition.png)

1. Ha a szabály aktiválásakor futtatandó e-mail műveletet szeretne hozzáadni, válassza a **+ E-mail lehetőséget.**

1. Az alábbi táblázatban található információk segítségével definiálja a műveletet, majd válassza a **Kész**lehetőséget:

    | Beállítás   | Érték                                             |
    | --------- | ------------------------------------------------- |
    | Megjelenített név | Operátori e-mail művelet                          |
    | Művelet        | Az Ön e-mail-címe                                |
    | Megjegyzések     | A környezeti hőmérséklet meghaladta a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Szabályművelet létrehozása](./media/quick-configure-rules/action.png)

1. Kattintson a **Mentés** gombra. A szabály a **Szabályok** lapon jelenik meg.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon értesítéseket a beérkezett üzenetek között.

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök figyeléséről, folytassa a rövid útmutatóval:

> [!div class="nextstepaction"]
> [Az Azure IoT Central segítségével figyelheti eszközeit.](quick-monitor-devices.md)
