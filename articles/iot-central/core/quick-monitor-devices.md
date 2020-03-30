---
title: Rövid útmutató – Az eszközök figyelése az Azure IoT Centralban
description: Operátorként megtudhatja, hogyan használhatja az Azure IoT Central alkalmazást az eszközök figyelésére.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e56b733a567c706c2a15f2c30ed93a47c244db11
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77168755"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Rövid útmutató: Az Azure IoT Central használatával figyelheti eszközeit

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ez a rövid útmutató bemutatja, mint operátor, hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök figyelésére és a beállítások módosítására.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, el kell végeznie a három korábbi rövid útmutató [létrehozása az Azure IoT Central alkalmazás,](./quick-deploy-iot-central.md) [Adjon hozzá egy szimulált eszközt az IoT Központi alkalmazás](./quick-create-pnp-device.md) és szabályok [konfigurálása az eszközhöz.](quick-configure-rules.md)

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. A szerkesztő hozzáadott egy szabályt, hogy értesítést küldjön, ha a hőmérséklet egy csatlakoztatott eszköz érzékelőtúllépte a küszöbértéket. Ellenőrizze a szerkesztő által az értesítések fogadásához választott fiókra küldött e-maileket.

Nyissa meg az eszköz rövid útmutatójának [konfigurálása](quick-configure-rules.md) című üzenet végén kapott e-mailt. Az e-mailben válassza ki az eszközre mutató hivatkozást:

![Értesítési e-mail](media/quick-monitor-devices/email.png)

Az előző rövid útmutatókban létrehozott szimulált eszköz **áttekintő** nézete megnyílik a böngészőben:

![Az értesítési e-mail-üzenetet kiváltó eszköz](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Operátorként az Eszközről az Áttekintés , **A – Ismert**és a **Parancsok** nézetben tekintheti meg az eszközadatait. **Overview** A szerkesztő létrehozott egy **Eszközkezelés** nézetet az eszközadatok szerkesztéséhez és az eszköztulajdonságok beállításához.

Az irányítópulton lévő diagram az eszköz hőmérsékletét ábrázolja. Ön úgy dönt, hogy a készülék hőmérséklete túl magas.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Az eszköz módosításához használja az **Eszköz kezelése** lapot.

A készülék hűtéséhez módosítsa a **ventilátor sebességét** 500-ra. Az eszköz frissítéséhez válassza a **Mentés** gombot. Amikor az eszköz megerősíti, hogy a beállítások megváltoznak, a tulajdonság állapota **szinkronizálva**változik:

![Beállítások frissítése](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>További lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Értesítés fogadása
* Probléma vizsgálata
* Probléma elhárítása

Most, hogy már tudja, hogy figyelemmel kíséri a készüléket, a javasolt következő lépés a következő:

> [!div class="nextstepaction"]
> [Eszközsablon létrehozása és kezelése.](howto-set-up-template.md)
