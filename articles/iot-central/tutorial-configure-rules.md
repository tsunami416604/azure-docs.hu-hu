---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan konfigurálhat telemetria-alapú szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: ankitscribbles
ms.author: ankitgup
ms.date: 10/12/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d7269f61579ce1ffd9a686634effd153837a2f25
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662984"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Oktatóanyag: Szabályok és műveletek az eszköz konfigurálása az Azure IoT Central

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ebben az oktatóanyagban létrehoz egy szabályt, amely e-mailt küld, amikor a csatlakoztatott légkondicionáló készülék hőmérséklete meghaladja a 90 &deg;F értéket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie az [Új eszköztípus definiálása az alkalmazásban](tutorial-define-device-type.md) című oktatóanyagot a használni kívánt **Csatlakoztatott légkondicionáló** eszközsablon létrehozásához.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Ha új, telemetria-alapú szabályt szeretne hozzáadni az alkalmazáshoz, válassza a bal oldali navigációs menüben lévő **Eszközkereső** elemet:

    ![Eszközkereső oldal](media/tutorial-configure-rules/explorerpage1.png)

    Láthatja a **Csatlakoztatott légkondicionáló (1.0.0)** eszközsablont és az előző oktatóanyagban létrehozott **Csatlakoztatott légkondicionáló-1** eszközt.

2. A csatlakoztatott légkondicionáló eszköz testreszabásának megkezdéséhez válassza ki az előző oktatóanyagban létrehozott eszközt:

    ![Csatlakoztatott légkondicionáló oldal](media/tutorial-configure-rules/builderdevicelist1.png)

3. Ha szabályt szeretne hozzáadni a **Szabályok** nézethez, válassza a **Szabályok** lehetőséget, majd kattintson a **Sablon szerkesztése** elemre:

    ![Szabályok nézet](media/tutorial-configure-rules/builderedittemplate.png)

4. Küszöbérték-alapú telemetriaszabály létrehozásához kattintson az **Új szabály** lehetőségre majd a **Telemetria** elemre.

    ![Sablon szerkesztése](media/tutorial-configure-rules/buildernewrule.png)

5. A szabály meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás                                      | Érték                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Név)                                         | Légkondicionáló hőmérsékletével kapcsolatos figyelmeztetés |
    | Szabály bekapcsolása a sablon összes eszközén | Bekapcsolva                                |
    | Szabály bekapcsolása ezen az eszközön                   | Bekapcsolva                                |
    | Állapot                                    | A hőmérséklet nagyobb, mint 90    |
    | Összesítés                                  | None                              |

    ![Hőmérsékleti szabályfeltétel](media/tutorial-configure-rules/buildertemperaturerule1.png)

## <a name="add-an-action"></a>Művelet hozzáadása

A szabályok definiálásakor egy műveletet is meghatároz, amely a szabályfeltételek teljesülésekor fut. Ebben az oktatóanyagban olyan műveletet ad hozzá, amely értesítő e-mailt küld a szabály elindításáról.

1. **Művelet** hozzáadásához **mentse** el a szabályt, majd görgessen lefelé a **Telemetria-szabály konfigurálása** panelen, majd válassza a **Műveletek** melletti **+** elemet, végül az **E-mail** lehetőséget:

    ![Hőmérsékleti szabályművelet](media/tutorial-configure-rules/builderaddaction1.png)

2. A művelet meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás   | Érték                          |
    | --------- | ------------------------------ |
    | Művelet        | Az Ön e-mail-címe             |
    | Megjegyzések     | A légkondicionáló hőmérséklete túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Alkalmazásszerkesztői hőmérsékleti művelet](media/tutorial-configure-rules/buildertemperatureaction.png)

3. Válassza a **Mentés** elemet. A szabály a **Szabályok** oldalon szerepel:

    ![Alkalmazásszerkesztő szabályok](media/tutorial-configure-rules/builderrules1.png)

4. Válassza a **Kész** lehetőséget a **Sablon szerkesztése** módból való kilépéshez.
 

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

![E-mailes művelet](media/tutorial-configure-rules/email.png)

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon több figyelmeztetést a Beérkezett üzenetek mappában. 

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
