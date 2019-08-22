---
title: Szabályok és műveletek konfigurálása az Azure IoT Centralban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan konfigurálhat telemetria-alapú szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: c2aa6edfe7ce9b670ea1015e2da72f3ecc48c9ec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878151"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-preview-features"></a>Oktatóanyag: Szabályok és műveletek konfigurálása az eszközhöz az Azure IoT Central (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ebben az oktatóanyagban egy olyan szabályt hoz létre, amely e-mailt küld, ha a környezeti érzékelő eszköz hőmérséklete meghaladja a 90&deg; F-ot.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Telemetria-alapú szabály létrehozása
> * Művelet hozzáadása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, végezze el az [új eszköz definiálása](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) az alkalmazásban oktatóanyagban a **környezeti érzékelő** eszköz sablonjának létrehozásához, hogy működjön a szolgáltatással.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Új telemetria-szabály az alkalmazáshoz való hozzáadásához a bal oldali navigációs menüben válassza a **szabályok**lehetőséget.

1. Új szabály létrehozásához válassza az **+ új**lehetőséget. Ezután válassza a **telemetria**lehetőséget.

1. Adja meg a **környezeti hőmérsékletet** a szabály neveként.

1. A **hatókör** szakaszban válassza ki a **környezeti érzékelőt** eszköz sablonként. Az a hatókör, amelyre ez a szabály vonatkozik. A **+ Filter**használatával további szűrési feltételeket adhat hozzá.

1. A **feltétel** szakaszban megadhatja, hogy mi indítja el a szabályt. A következő információk segítségével határozhat meg egy olyan feltételt, amely a hőmérséklet telemetria:

    | Mező                                        | Value                             |
    | -------------------------------------------- | ------------------------------    |
    | Mérés                                  | Hőmérséklet                       |
    | Operator                                     | nagyobb, mint                   |
    | Value                                        | 90                                |

    További feltételek hozzáadásához válassza a **+ feltétel**lehetőséget.

    ![Szabály feltételének létrehozása](./media/tutorial-configure-rules-pnp/condition.png)

1. A szabály indításakor futtatandó művelet hozzáadásához válassza a **+ művelet**lehetőséget, majd válassza az **e-mail**lehetőséget.

1. A következő táblázatban található információk segítségével definiálhatja a műveletet:

    | Beállítás   | Value                                             |
    | --------- | ------------------------------------------------- |
    | Cél        | E-mail cím                                |
    | Megjegyzések     | A környezeti hőmérséklet túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    ![Szabály létrehozása művelet](./media/tutorial-configure-rules-pnp/action.png)

1. Kattintson a **Mentés** gombra. A szabály a **szabályok** lapon jelenik meg.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Amikor teljesülnek a szabályban megadott feltételek, az alkalmazás egy üzenetet küld a műveletben megadott e-mail-címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon riasztásokat a Beérkezett üzenetek mappájában.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Most, hogy meghatározta a küszöbérték-alapú szabályt a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Eseményszabály létrehozása és értesítések beállítása](howto-create-event-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
