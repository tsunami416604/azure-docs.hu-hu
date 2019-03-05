---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan konfigurálhat telemetria-alapú szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5c414e4398ef280b0d04d0a384722ee923c4cefa
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316022"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Oktatóanyag: Szabályok és műveletek az eszköz konfigurálása az Azure IoT Central (új felhasználói felület tervezése)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ebben az oktatóanyagban létrehoz egy szabályt, amely e-mailt küld, amikor a csatlakoztatott légkondicionáló készülék hőmérséklete meghaladja a 90 &deg;F értéket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie az [Új eszköztípus definiálása az alkalmazásban](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) című oktatóanyagot a használni kívánt **Csatlakoztatott légkondicionáló** eszközsablon létrehozásához.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Új telemetrián alapuló szabály hozzáadása az alkalmazáshoz, a bal oldali navigációs menüben válassza ki a **eszközsablonok**:

    ![Eszköz sablonok lap](media/tutorial-configure-rules-experimental/templatespage1.png)

    Megjelenik a **Légkondicionálóját csatlakoztatva (1.0.0-s)** az előző oktatóanyagban létrehozott eszköz sablont.

2. Az eszköz a sablon testreszabásához jelölje be a **Légkondicionálóját csatlakoztatott** az előző oktatóanyagban létrehozott sablont.

3. A telemetria-alapú szabály hozzáadása a **szabályok** nézetben válassza **szabályok**, jelölje be **+ új szabály**, majd válassza ki **Telemetriai**:

    ![Szabályok nézet](media/tutorial-configure-rules-experimental/newrule.png)

5. A szabály meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás                                      | Érték                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Név)                                         | Légkondicionáló hőmérsékletével kapcsolatos figyelmeztetés |
    | Szabály bekapcsolása a sablon összes eszközén | Bekapcsolva                                |
    | Állapot                                    | A hőmérséklet nagyobb, mint 90    |
    | Összesítés                                  | None                              |

    ![Hőmérsékleti szabályfeltétel](media/tutorial-configure-rules-experimental/temperaturerule.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

## <a name="add-an-action"></a>Művelet hozzáadása

A szabályok definiálásakor egy műveletet is meghatároz, amely a szabályfeltételek teljesülésekor fut. Ebben az oktatóanyagban létrehoz egy szabályt, amely az értesítő e-mail küldése művelet.

1. Hozzáadása egy **művelet**, először **mentése** a szabályt, és ezután görgessen lefelé a a **Telemetriai szabály konfigurálása** panel. Válassza ki a **+** melletti **műveletek**, és válassza a **E-mail**:

    ![Hőmérsékleti szabályművelet](media/tutorial-configure-rules-experimental/addaction.png)

2. A művelet meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás   | Érték                          |
    | --------- | ------------------------------ |
    | Művelet        | Az Ön e-mail-címe             |
    | Megjegyzések     | A légkondicionáló hőmérséklete túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Hőmérséklet-művelet](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. Kattintson a **Mentés** gombra. A szabály megjelenik a **szabályok** lapot.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

![E-mailes művelet](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy nem szeretne figyelmeztetéseket kapni a Beérkezett üzenetek mappában.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

Most, hogy a küszöbérték-alapú szabály meghatározta a javasolt következő lépésre, hogy [személyre szabni a nézeteket az operátor](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

Az Azure IoT Central különböző szabálytípusairól és a szabálydefiníció paraméterezésének módjáról további információért lásd az alábbi témaköröket:
* [Telemetria-szabály létrehozása és értesítések beállítása](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Eseményszabály létrehozása és értesítések beállítása](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

<!-- Next tutorials in the sequence -->
