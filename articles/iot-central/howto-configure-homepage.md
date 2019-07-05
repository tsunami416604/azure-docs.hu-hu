---
title: Az Azure IoT Central irányítópult konfigurálása |} A Microsoft Docs
description: A jelentéskészítő, megtudhatja, hogyan konfigurálhatja az alapértelmezett Azure IoT Central irányítópult.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: e6947a4f15797028274d49069d9e2787b143860d
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503226"
---
# <a name="configure-the-application-dashboard"></a>Az irányítópult konfigurálása

A **irányítópult** az oldal, amely betölti, ha az alkalmazáshoz hozzáféréssel rendelkező felhasználók keresse meg az alkalmazás URL-CÍMÉT. Ha a kiválasztott vagy a **minta Contoso** vagy **minta Devkits** alkalmazássablon hozhat létre az alkalmazást, az alkalmazás tartalmaz egy előre meghatározott irányítópultot. Ha úgy döntött a **egyéni alkalmazás** alkalmazássablon, az irányítópulton az üres.

> [!NOTE]
> A felhasználók is [saját személyes irányítópultok kialakítása](howto-personalize-dashboard.md) helyett az alapértelmezett irányítópult.

## <a name="add-tiles"></a>Csempék hozzáadása

Az alábbi képernyőképen az irányítópult látható a létrehozott alkalmazás a **minta Contoso** sablont. Lehetőséggel testre szabhatja az alkalmazás az alapértelmezett irányítópult, **szerkesztése** tetején, az oldal jobb.

![A "Contoso minta" sablonon alapuló alkalmazások irányítópult](media/howto-configure-homepage/image1a.png)

Kiválasztásával **szerkesztése**, az irányítópult könyvtár panel nyílik meg. A függvénytár tartalmazza a csempék és irányítópult primitívek segítségével testre szabhatja az irányítópultot.

![Irányítópult-könyvtár](media/howto-configure-homepage/image2a.png)

Hozzáadhat például egy **eszközbeállítások, illetve tulajdonságok** mozaiklapra a választott eszköz aktuális beállítások és tulajdonságok értékeit. Ehhez először válassza ki a **eszköz sablon** válassza ki a **eszközpéldány**. Miután egy cím, és kattintson a csempére, adjon egy **beállítás** vagy egy **tulajdonság** megjelenítéséhez. A következő képernyőképen az látható, beállítás- és a csempe hozzáadása kiválasztva. Válassza ki **kész** menteni a módosítást az irányítópulton.

![Részletes beállítások és tulajdonságok "Konfigurálása eszköz részletei" képernyő](media/howto-configure-homepage/image3a.png)

Az operátornak megtekinti az alapértelmezett irányítópult, amikor azok az új csempe jelenik meg a most a **beállítása hőmérséklet** az eszköz beállítása:

![A megjelenített beállítások és tulajdonságok csempe "Az irányítópult" lap](media/howto-configure-homepage/image4a.png)

Áttekintheti a többi csempe a könyvtárban a felderítendő típusok az alapértelmezett irányítópult további testreszabásával.

Csempék az Azure IoT Central használatával kapcsolatos további tudnivalókért lásd: [irányítópult-csempék használata](howto-use-tiles.md).

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central alapértelmezett alkalmazás irányítópult konfigurálása, hogy a következőket teheti:

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan készítheti elő és képek feltöltése](howto-prepare-images.md)
