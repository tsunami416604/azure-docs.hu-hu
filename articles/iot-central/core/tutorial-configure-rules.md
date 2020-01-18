---
title: 'Oktatóanyag: szabályok és műveletek konfigurálása az Azure IoT Central'
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan konfigurálhat telemetria-alapú szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 9140114e7d31f24770bdcce9aae849b01aae9996
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263631"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Oktatóanyag: Az eszközre vonatkozó szabályok és műveletek konfigurálása az Azure IoT Centralban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ebben az oktatóanyagban egy olyan szabályt hoz létre, amely e-mailt küld, ha egy csatlakoztatott légkondicionáló eszköz hőmérséklete meghaladja a 70&deg; F.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie az [Új eszköztípus definiálása az alkalmazásban](tutorial-define-device-type.md) című oktatóanyagot a használni kívánt **Csatlakoztatott légkondicionáló** eszközsablon létrehozásához.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Új telemetria-szabály az alkalmazáshoz való hozzáadásához a bal oldali panelen válassza az **eszközök sablonjai**elemet:

    ![Eszközök sablonjai lap](media/tutorial-configure-rules/templatespage1.png)

    Megjelenik az előző oktatóanyagban létrehozott **csatlakoztatott légkondicionáló (1.0.0)** eszköz sablonja.

2. Az eszköz sablonjának testreszabásához válassza ki az előző oktatóanyagban létrehozott **csatlakoztatott légkondicionáló** sablont.

3. Ha telemetria szabályt szeretne hozzáadni a **szabályok** nézetben, válassza a **szabályok**, majd az **+ új szabály**, majd a **telemetria**lehetőséget:

    ![Szabályok nézet](media/tutorial-configure-rules/newrule.png)

4. A szabály meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás                                      | Value (Díj)                             |
    | -------------------------------------------- | ------------------------------    |
    | Name (Név)                                         | Légkondicionáló hőmérsékletével kapcsolatos figyelmeztetés |
    | Szabály bekapcsolása a sablon összes eszközén | Be                                |
    | Állapot                                    | A hőmérséklet nagyobb, mint 70    |
    | Összesítés                                  | None                              |

    ![Hőmérsékleti szabályfeltétel](media/tutorial-configure-rules/temperaturerule.png)

    Ezután válassza a **Save** (Mentés) lehetőséget.

## <a name="add-an-action"></a>Művelet hozzáadása

A szabályok definiálásakor egy műveletet is meghatároz, amely a szabályfeltételek teljesülésekor fut. Ebben az oktatóanyagban egy olyan szabályt hoz létre, amely e-mailben értesítést küld.

1. **Művelet**hozzáadásához először **mentse** a szabályt, majd görgessen le a telemetria- **szabály konfigurálása** panelen. Válassza ki a **műveletek**melletti **+** , majd válassza az **e-mail**lehetőséget:

    ![Hőmérsékleti szabályművelet](media/tutorial-configure-rules/addaction.png)

2. A művelet meghatározásához használja a következő táblázatban lévő információkat:

    | Beállítás      | Value (Díj)                                               |
    | ------------ | --------------------------------------------------- |
    | Megjelenített név | E-mail hőmérséklet riasztása                             |
    | –           | Az Ön e-mail-címe                                  |
    | Megjegyzések        | A légkondicionáló hőmérséklete túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Hőmérsékleti művelet](media/tutorial-configure-rules/temperatureaction.png)

3. Kattintson a **Mentés** gombra. A szabály a megjelenik a **Szabályok** oldalon.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon riasztásokat a Beérkezett üzenetek mappájában.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
>
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

Most, hogy meghatározta a küszöbérték-alapú szabályt, a javasolt következő lépés az [operátor nézeteinek testreszabása](tutorial-customize-operator.md).

Az Azure IoT Central különböző szabálytípusairól és a szabálydefiníció paraméterezésének módjáról további információért lásd az alábbi témaköröket:

* [Telemetria-szabály létrehozása és értesítések beállítása](howto-create-telemetry-rules.md)
* [Eseményszabály létrehozása és értesítések beállítása](howto-create-event-rules.md)

<!-- Next tutorials in the sequence -->