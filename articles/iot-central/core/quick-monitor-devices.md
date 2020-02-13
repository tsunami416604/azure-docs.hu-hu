---
title: Rövid útmutató – az eszközök figyelése az Azure IoT Central
description: Az operátorok segítségével megtudhatja, hogyan használhatja az Azure IoT Central alkalmazást az eszközök figyelésére.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e56b733a567c706c2a15f2c30ed93a47c244db11
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168755"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Gyors útmutató: az Azure IoT Central használata az eszközök figyelésére

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ez a rövid útmutató bemutatja, hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök monitorozásához és a beállítások módosításához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, végezze el a három korábbi rövid útmutatót, [hozzon létre egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md), [adjon hozzá egy szimulált eszközt a IoT Central alkalmazáshoz](./quick-create-pnp-device.md) , és [konfigurálja az eszköz szabályait és műveleteit](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. A szerkesztő hozzáadott egy szabályt, amely értesítést küld, amikor egy csatlakoztatott eszköz hőmérséklete túllépte a küszöbértéket. Ellenőrizze a szerkesztő által az értesítések fogadásához választott fiókra küldött e-maileket.

Nyissa meg az eszköz rövid útmutatójában a [szabályok és műveletek konfigurálása](quick-configure-rules.md) végén kapott e-mail-üzenetet. Az e-mailben válassza ki az eszközre mutató hivatkozást:

![Riasztási értesítő e-mail](media/quick-monitor-devices/email.png)

Az előző rövid útmutatókban létrehozott szimulált eszköz **áttekintő** nézete a böngészőben nyílik meg:

![Az értesítési e-mail-üzenetet kiváltó eszköz](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Kezelőként megtekintheti az eszközre vonatkozó információkat az **Áttekintés**, a **Névjegy**és a **parancsok** nézetekben. A készítő létrehozott egy **eszköz kezelése** nézetet az eszköz adatainak szerkesztéséhez és az eszköz tulajdonságainak beállításához.

Az irányítópulton lévő diagram az eszköz hőmérsékletét ábrázolja. Ön dönti el, hogy az eszköz hőmérséklete túl magas-e.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Az eszköz módosításához használja az **eszköz kezelése** lapot.

Az eszköz lehűtéséhez módosítsa a **ventilátor sebességét** 500-ra. Az eszköz frissítéséhez válassza a **Mentés** lehetőséget. Amikor az eszköz megerősíti a beállítások változását, a tulajdonság állapota **szinkronizálva**értékre változik:

![Beállítások frissítése](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Értesítés fogadása
* Probléma vizsgálata
* Probléma elhárítása

Most, hogy már ismeri az eszköz figyelését, a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Eszköz-sablon létrehozása és kezelése](howto-set-up-template.md).
