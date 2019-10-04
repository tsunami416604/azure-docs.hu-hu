---
title: Az eszközök monitorozása az Azure IoT Centralban | Microsoft Docs
description: Operátorként az Azure IoT Central alkalmazással monitorozza az eszközöket.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: d716eb761ab406b65f10898b29775327a801ac45
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875492"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Oktatóanyag: Az eszközök monitorozása az Azure IoT Central használatával

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Ez az oktatóanyag bemutatja, hogy operátorként hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök monitorozására és a beállítások módosítására.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Értesítés fogadása
> * Probléma vizsgálata
> * Probléma elhárítása

## <a name="prerequisites"></a>Előfeltételek

Mielőtt belekezdene, a szerkesztőnek el kell végeznie az Azure IoT Central alkalmazás létrehozására szolgáló három szerkesztői oktatóanyagot:

* [Új eszköztípus definiálása](tutorial-define-device-type.md)
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md)
* [Operátori nézetek testreszabása](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. A szerkesztő hozzáadott egy szabályt, amely értesítést küld, amikor a csatlakoztatott légkondicionáló készülék hőmérséklete túllép egy küszöbértéken. Ellenőrizze a szerkesztő által az értesítések fogadásához választott fiókra küldött e-maileket.

Nyissa meg [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md) című oktatóanyag végén kapott e-mail-üzenetet. Az e-mailben válassza a **Kattintson ide az eszköz megnyitásához** lehetőséget:

![Riasztási értesítő e-mail](media/tutorial-monitor-devices/email.png)

Megnyílik a böngészőben az előző oktatóanyagokban létrehozott szimulált **Csatlakoztatott légkondicionáló-1** eszköz **Eszköz** oldala:

![Az értesítési e-mail-üzenetet kiváltó eszköz](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Operátorként megtekintheti az eszközzel kapcsolatos információkat a **Mérések**, a **Beállítások**, a **Tulajdonságok**, a **Szabályok** és az **Irányítópult** oldalakon. A szerkesztő testre szabta az **Irányítópultot**, hogy megjelenítse a csatlakoztatott légkondicionáló eszközzel kapcsolatos fontos információkat.

Válassza az **Irányítópult** lehetőséget az eszközzel kapcsolatos információk megtekintéséhez.

![Az eszköz irányítópultja](media/tutorial-monitor-devices/initial_screen.png)

Az irányítópulton lévő diagram az eszköz hőmérsékletét ábrázolja. Az eszköz aktuális célként megadott hőmérsékletét az **eszköz tulajdonságai** csempén is megtekintheti. Úgy dönt, hogy a célhőmérséklet túl magas.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Az eszköz célhőmérsékletének módosításához használja a **Beállítások** lapot:

1. Válassza a **Beállítások** lehetőséget. Változtassa a **Megadott hőmérsékletet** 75-ös értékre. Válassza a **Frissítés** lehetőséget, hogy az új célhőmérsékletet az eszközre küldje. Amikor az eszköz megerősíti a beállítások változását, a beállítás **szinkronizálásra**módosul:

    ![Beállítások módosítása](media/tutorial-monitor-devices/change_settings.png)

2. Válassza az **Irányítópult** lehetőséget, és ellenőrizze az új beállítás értékét:

    ![Az eszköz frissített irányítópultja](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="nextstepaction"]
> * Értesítés fogadása
> * Probléma vizsgálata
> * Probléma elhárítása

Most, hogy megismerte az eszközök monitorozásának módját, a következő javasolt lépés [egy eszköz hozzáadása](tutorial-add-device.md).