---
title: Az Azure IoT központi alkalmazás kezdőlapjának beállítása |} A Microsoft Docs
description: Mint szerkesztő megtudhatja, hogyan adható meg a kezdőlap, az Azure IoT Central alkalmazáshoz.
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/10/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 609dc8aa1305833fb3677de6fbe95cf490237a4a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731121"
---
## <a name="configuring-homepage"></a>Kezdőlap konfigurálása

A kezdőlapon az oldal, amely betölti, ha a hozzáféréssel rendelkező felhasználók keresse meg az alkalmazás URL-címe. Ha az alkalmazás létrehozásakor a "Contoso minta" vagy "Minta Devkits" alkalmazássablonok választotta, az alkalmazás fogja előre meghatározott kezdőlapok. Ha azonban az "Egyéni alkalmazás" sablon, akkor a kezdőlapon üres lesz.

Például itt látható a "Contoso minta" sablonon alapuló alkalmazások kezdőlapja. Testre szabhatja az alkalmazás kezdőlapja, először válassza **szerkesztése** jobb felső sarokban lévő. 

![A "Contoso minta" sablonon alapuló alkalmazások kezdőlapja](media\howto-configure-homepage\image1.png)

Kiválasztásával **szerkesztése**, ekkor megnyílik az irányítópult-könyvtár a bal oldali panelen. Nincsenek csempék és irányítópultot is hozzáadhatók a kezdőlap testreszabása primitívekre számos különböző típusú.

![Irányítópult-könyvtár](media\howto-configure-homepage\image2.png)

Hozzáadhat például egy **beállításait és tulajdonságait** csempére, hogy az aktuális értékek beállításait és tulajdonságait, a választott megjelenítés. Ehhez először válassza ki a **eszköz sablon** válassza ki a **eszközpéldány**. Miután egy cím, és kattintson a csempére, adjon egy **beállítás** vagy egy **tulajdonság** megjelenítéséhez. Ebben az esetben kiválasztottuk **beállítása hőmérséklet**. Kattintson a **kész** miatt ez a csempe jelenik meg a kezdőlapon.

![Részletes beállítások és tulajdonságok "Konfigurálása eszköz részletei" képernyő](media\howto-configure-homepage\image3.png)

Most már az operátornak megtekinti a kezdőlap elérését, amikor megjelenik az erre a csempére, amely megjeleníti a Tulajdonságok vagy az eszköz beállítások:

![A megjelenített beállítások és tulajdonságok csempe "Az irányítópult" lap](media\howto-configure-homepage\image4.png)

A többi csempe különféle az erőforrástárban felderíteni, hogyan szabhatja testre az alkalmazás kezdőlapjára még több módosításával.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central kezdőlap konfigurálása, hogy a következőket teheti:

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan készítheti elő és képek feltöltése](howto-prepare-images.md)
