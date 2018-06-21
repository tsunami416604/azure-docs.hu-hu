---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan konfigurálhat telemetria-alapú szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: ankitgupta
ms.author: ankitgup
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af2aa8d7b01d973da400808fd3e97d0739693cd2
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236331"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Oktatóanyag: Az eszközre vonatkozó szabályok és műveletek konfigurálása az Azure IoT Centralban

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan konfigurálhat telemetria-alapú szabályokat és műveleteket a Microsoft Azure IoT Central-alkalmazásban.

Ebben az oktatóanyagban létrehoz egy szabályt, amely e-mailt küld, amikor a csatlakoztatott légkondicionáló készülék hőmérséklete meghaladja a 90 &deg;F értéket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie az [Új eszköztípus definiálása az alkalmazásban](tutorial-define-device-type.md) című oktatóanyagot a használni kívánt **Csatlakoztatott légkondicionáló** eszközsablon létrehozásához.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Ha új, telemetria-alapú szabályt szeretne hozzáadni az alkalmazáshoz, válassza a bal oldali navigációs menüben lévő **Eszközkereső** elemet:

    ![Eszközkereső oldal](media/tutorial-configure-rules/explorerpage.png)

    Láthatja a **Csatlakoztatott légkondicionáló (1.0.0)** eszközsablont és az előző oktatóanyagban létrehozott **Csatlakoztatott légkondicionáló-1** eszközt.

2. A csatlakoztatott légkondicionáló eszköz testreszabásának megkezdéséhez válassza ki az előző oktatóanyagban létrehozott eszközt:

    ![Csatlakoztatott légkondicionáló oldal](media/tutorial-configure-rules/builderdevicelist.png)

3. Ha szabályt szeretne hozzáadni a **Szabályok** nézethez, válassza a **Szabályok** lehetőséget:

    ![Szabályok nézet](media/tutorial-configure-rules/builderrulesview.png)

4. Küszöbérték-alapú telemetriaszabály létrehozásához válassza az **Új szabály** lehetőséget, majd a **Telemetria** elemet.

5. A szabály meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás     | Érték                          |
    | ----------- | ------------------------------ |
    | Name (Név)        | Légkondicionáló hőmérséklete    |
    | Szabály engedélyezése | Bekapcsolva                             |
    | Állapot   | A hőmérséklet nagyobb, mint 90 |

    ![Hőmérsékleti szabályfeltétel](media/tutorial-configure-rules/buildertemperaturerule.png)

## <a name="add-an-action"></a>Művelet hozzáadása

A szabályok definiálásakor egy műveletet is meghatároz, amely a szabályfeltételek teljesülésekor fut. Ebben az oktatóanyagban olyan műveletet ad hozzá, amely értesítő e-mailt küld a szabály elindításáról.

1. **Művelet** hozzáadásához görgessen lefelé a **Telemetria-szabály konfigurálása** panelen, majd válassza a **Műveletek** melletti **+** elemet, és válassza az **E-mail** lehetőséget:

    ![Hőmérsékleti szabályművelet](media/tutorial-configure-rules/builderaddaction.png)

2. A művelet meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás   | Érték                          |
    | --------- | ------------------------------ |
    | Művelet        | Az Ön e-mail-címe             |
    | Megjegyzések     | A légkondicionáló hőmérséklete túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Alkalmazásszerkesztői hőmérsékleti művelet](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Válassza a **Mentés** elemet. A szabály a **Szabályok** oldalon szerepel:

    ![Alkalmazásszerkesztő szabályok](media/tutorial-configure-rules/builderrules.png)

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

![E-mailes művelet](media/tutorial-configure-rules/email.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

Most, hogy meghatározott egy küszöbérték-alapú szabályt, a következő javasolt lépés [az operátori nézetek testreszabása](tutorial-customize-operator.md).

Az Azure IoT Central különböző szabálytípusairól és a szabálydefiníció paraméterezésének módjáról további információért lásd az alábbi témaköröket:
* [Telemetria-szabály létrehozása és értesítések beállítása](howto-create-telemetry-rules.md)
* [Eseményszabály létrehozása és értesítések beállítása](howto-create-event-rules.md)

<!-- Next tutorials in the sequence -->
