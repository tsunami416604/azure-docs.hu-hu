---
title: Rövid útmutató – az eszközök figyelése az Azure IoT Central
description: Kezelőként az Azure IoT Central alkalmazással figyelheti az eszközöket ebben a rövid útmutatóban.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e0f0e765ba6bb8b0969fbf639c8427e8b65214fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434758"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Gyors útmutató: az Azure IoT Central használata az eszközök figyelésére (előzetes verziójú funkciók)

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez a rövid útmutató bemutatja, hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök monitorozásához és a beállítások módosításához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, végezze el a három korábbi rövid útmutatót, [hozzon létre egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md), [adjon hozzá egy szimulált eszközt a IoT Central alkalmazáshoz](./quick-create-pnp-device.md) , és [konfigurálja az eszköz szabályait és műveleteit](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. A szerkesztő hozzáadott egy szabályt, amely értesítést küld, amikor egy csatlakoztatott környezeti érzékelő eszköz hőmérséklete túllépte a küszöbértéket. Ellenőrizze a szerkesztő által az értesítések fogadásához választott fiókra küldött e-maileket.

Nyissa meg az eszköz rövid útmutatójában a [szabályok és műveletek konfigurálása](quick-configure-rules.md) végén kapott e-mail-üzenetet. Az e-mailben válassza ki az eszközre mutató hivatkozást:

![Riasztási értesítő e-mail](media/quick-monitor-devices/email.png)

Az előző rövid útmutatók során létrehozott környezeti érzékelő szimulált eszköz **irányítópult** nézete megnyílik a böngészőben:

![Az értesítési e-mail-üzenetet kiváltó eszköz](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Kezelőként megtekintheti az eszközre vonatkozó információkat az **Áttekintés**, a **környezeti érzékelő tulajdonságai**és a **parancsok** oldalain. A szerkesztő testreszabta az **irányítópult** és a **környezeti érzékelő tulajdonságlapját** , hogy fontos információkat jelenítsen meg egy csatlakoztatott környezeti érzékelő eszközről.

Az eszközre vonatkozó információk megtekintéséhez válassza az **Áttekintés** nézetet.

Az irányítópulton lévő diagram az eszköz hőmérsékletét ábrázolja. Ön dönti el, hogy az eszköz hőmérséklete túl magas-e.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Ha módosítani kívánja az eszközt, használja a **környezeti érzékelő tulajdonságai** lapot.

Válassza a **környezeti érzékelő tulajdonságai**elemet. Módosítsa a **fényerő szintjét** 10-re. Az eszköz frissítéséhez válassza a **Mentés** lehetőséget. Amikor az eszköz megerősíti a beállítások változását, a tulajdonság állapota **szinkronizálva**értékre változik:

![Beállítások frissítése](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Értesítés fogadása
* Probléma vizsgálata
* Probléma elhárítása

Most, hogy már ismeri az eszköz figyelését, a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Eszköz-sablon létrehozása és kezelése](howto-set-up-template.md).
