---
title: Az Azure IoT központi alkalmazás kezdőlapjának beállítása |} A Microsoft Docs
description: Mint szerkesztő megtudhatja, hogyan adható meg a kezdőlap, az Azure IoT Central alkalmazáshoz.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55773524"
---
# <a name="configuring-homepage"></a>Kezdőlap konfigurálása

A kezdőlapon az oldal, amely betölti, ha az alkalmazáshoz hozzáféréssel rendelkező felhasználók keresse meg az alkalmazás URL-címe. Ha az alkalmazás létrehozásakor a "Contoso minta" vagy "Minta Devkits" alkalmazássablonok választotta, az alkalmazás fogja előre meghatározott kezdőlapok. Ha azonban az "Egyéni alkalmazás" sablon, akkor a kezdőlapon üres lesz.

## <a name="add-tiles"></a>Csempék hozzáadása

Például itt látható a "Contoso minta" sablonon alapuló alkalmazások kezdőlapja. Testre szabhatja az alkalmazás kezdőlapja, először válassza **szerkesztése** jobb felső sarokban lévő. 

![A "Contoso minta" sablonon alapuló alkalmazások kezdőlapja](media/howto-configure-homepage-experimental/image1.png)

Kiválasztásával **szerkesztése**, ekkor megnyílik az irányítópult-könyvtár a bal oldali panelen. Nincsenek csempék és irányítópultot is hozzáadhatók a kezdőlap testreszabása primitívekre számos különböző típusú.

![Irányítópult-könyvtár](media/howto-configure-homepage-experimental/image2.png)

Hozzáadhat például egy **beállításait és tulajdonságait** csempére, hogy az aktuális értékek beállításait és tulajdonságait, a választott megjelenítés. Ehhez először válassza ki a **eszköz sablon** válassza ki a **eszközpéldány**. Miután egy cím, és kattintson a csempére, adjon egy **beállítás** vagy egy **tulajdonság** megjelenítéséhez. Ebben az esetben kiválasztottuk **beállítása hőmérséklet**. Kattintson a **kész** miatt ez a csempe jelenik meg a kezdőlapon.

![Részletes beállítások és tulajdonságok "Konfigurálása eszköz részletei" képernyő](media/howto-configure-homepage-experimental/image3.png)

Most már az operátornak megtekinti a kezdőlap elérését, amikor megjelenik az erre a csempére, amely megjeleníti a Tulajdonságok vagy az eszköz beállítások:

![A megjelenített beállítások és tulajdonságok csempe "Az irányítópult" lap](media/howto-configure-homepage-experimental/image4.png)

A többi csempe különféle az erőforrástárban felderíteni, hogyan szabhatja testre az alkalmazás kezdőlapjára még több módosításával.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central kezdőlap konfigurálása, hogy a következőket teheti:

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan készítheti elő és képek feltöltése](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
