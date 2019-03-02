---
title: Az eszközök monitorozása az Azure IoT Centralban | Microsoft Docs
description: Operátorként az Azure IoT Central alkalmazással monitorozza az eszközöket.
author: dominicbetts
ms.author: dobett
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 03e0826d306328ce9a7293d26289f04a012c447f
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216458"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Oktatóanyag: Az eszközök monitorozása az Azure IoT Central használatával

Ez az oktatóanyag bemutatja, hogy operátorként hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök monitorozására és a beállítások módosítására.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Értesítés fogadása
> * Probléma vizsgálata
> * Probléma elhárítása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt belekezdene, a szerkesztőnek el kell végeznie az Azure IoT Central alkalmazás létrehozására szolgáló három szerkesztői oktatóanyagot:

* [Új eszköztípus definiálása](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Operátori nézetek testreszabása](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. A szerkesztő hozzáadott egy szabályt, amely értesítést küld, amikor a csatlakoztatott légkondicionáló készülék hőmérséklete túllép egy küszöbértéken. Ellenőrizze a szerkesztő által az értesítések fogadásához választott fiókra küldött e-maileket.

Nyissa meg [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) című oktatóanyag végén kapott e-mail-üzenetet. Az e-mailben válassza a **Kattintson ide az eszköz megnyitásához** lehetőséget:

![Riasztási értesítések e-mailben](media/tutorial-monitor-devices-experimental/email.png)

Megnyílik a böngészőben az előző oktatóanyagokban létrehozott szimulált **Csatlakoztatott légkondicionáló-1** eszköz **Eszköz** oldala:

![Az értesítési e-mail-üzenetet kiváltó eszköz](media/tutorial-monitor-devices-experimental/sourcedevice.png)

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Operátorként megtekintheti az eszközzel kapcsolatos információkat a **Mérések**, a **Beállítások**, a **Tulajdonságok**, a **Szabályok** és az **Irányítópult** oldalakon. A szerkesztő testre szabta az **Irányítópultot**, hogy megjelenítse a csatlakoztatott légkondicionáló eszközzel kapcsolatos fontos információkat.

Válassza az **Irányítópult** lehetőséget az eszközzel kapcsolatos információk megtekintéséhez.

![Az eszköz irányítópultja](media/tutorial-monitor-devices-experimental/initial_screen.png)

Az irányítópulton lévő diagram az eszköz hőmérsékletét ábrázolja. Emellett megtekintheti az eszköz az aktuális cél hőmérséklet a **eszköztulajdonságok** csempére. Úgy dönt, hogy a célhőmérséklet túl magas.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Az eszköz célhőmérsékletének módosításához használja a **Beállítások** lapot:

1. Válassza a **Beállítások** lehetőséget. Változtassa a **Megadott hőmérsékletet** 75-ös értékre. Válassza a **Frissítés** lehetőséget, hogy az új célhőmérsékletet az eszközre küldje. Ha az eszköz megerősíti, hogy a beállítások módosítása, a beállítás állapota **szinkronizált**:

    ![Beállítások frissítése](media/tutorial-monitor-devices-experimental/change_settings.png)

2. Válassza az **Irányítópult** lehetőséget, és ellenőrizze az új beállítás értékét:

    ![Az eszköz frissített irányítópultja](media/tutorial-monitor-devices-experimental/new_settings.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="nextstepaction"]
> * Értesítés fogadása
> * Probléma vizsgálata
> * Probléma elhárítása

Most, hogy megismerte az eszközök monitorozásának módját, a következő javasolt lépés [egy eszköz hozzáadása](tutorial-add-device-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).