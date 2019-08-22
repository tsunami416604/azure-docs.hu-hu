---
title: Az eszközök monitorozása az Azure IoT Centralban | Microsoft Docs
description: Operátorként az Azure IoT Central alkalmazással monitorozza az eszközöket.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878116"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Oktatóanyag: Az Azure IoT Central használata az eszközök figyelésére (előzetes verziójú funkciók)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Ez az oktatóanyag bemutatja, hogy operátorként hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök monitorozására és a beállítások módosítására.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Értesítés fogadása
> * Probléma vizsgálata
> * Probléma elhárítása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt belekezdene, a szerkesztőnek el kell végeznie az Azure IoT Central alkalmazás létrehozására szolgáló három szerkesztői oktatóanyagot:

* [Új eszköztípus definiálása](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Eszköz hozzáadása](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. A szerkesztő hozzáadott egy szabályt, amely értesítést küld, amikor egy csatlakoztatott környezeti érzékelő eszköz hőmérséklete túllépte a küszöbértéket. Ellenőrizze a szerkesztő által az értesítések fogadásához választott fiókra küldött e-maileket.

Nyissa meg [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) című oktatóanyag végén kapott e-mail-üzenetet. Az e-mailben válassza ki az eszközre mutató hivatkozást:

![Riasztási értesítő e-mail](media/tutorial-monitor-devices-pnp/email.png)

Az előző oktatóanyagokban létrehozott környezeti érzékelő szimulált eszközének **irányítópult** -nézete megnyílik a böngészőben:

![Az értesítési e-mail-üzenetet kiváltó eszköz](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Kezelőként megtekintheti az eszköz adatait az irányítópulton, a **környezeti érzékelő tulajdonságaiban**és a **parancsok** oldalain. A szerkesztő testreszabta az **irányítópult** és a **környezeti érzékelő tulajdonságlapját** , hogy fontos információkat jelenítsen meg egy csatlakoztatott környezeti érzékelő eszközről.

Válassza az **Irányítópult** lehetőséget az eszközzel kapcsolatos információk megtekintéséhez.

Az irányítópulton lévő diagram az eszköz hőmérsékletét ábrázolja. Az eszköz aktuális célként megadott hőmérsékletét az **eszköz tulajdonságai** csempén is megtekintheti. Úgy dönt, hogy a célhőmérséklet túl magas.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Az eszköz módosításához használja a **környezeti érzékelő tulajdonságai** lapot:

1. Válassza a **környezeti érzékelő tulajdonságai**elemet. Módosítsa a **fényerő szintjét** 10-re. Az eszköz frissítéséhez válassza a **Mentés** lehetőséget. Amikor az eszköz megerősíti a beállítások változását, a tulajdonság állapota szinkronizálva értékre változik:

    ![Beállítások módosítása](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Válassza az **irányítópult** lehetőséget, és ellenőrizze az új tulajdonság értékét:

    ![Az eszköz frissített irányítópultja](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Értesítés fogadása
* Probléma vizsgálata
* Probléma elhárítása

Most, hogy már ismeri az eszköz figyelését, a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).