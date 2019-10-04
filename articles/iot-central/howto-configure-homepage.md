---
title: Az Azure IoT Central alkalmazás irányítópultjának konfigurálása | Microsoft Docs
description: A Builder használatával megtudhatja, hogyan konfigurálhatja az alapértelmezett Azure IoT Central alkalmazás-irányítópultot.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850210"
---
# <a name="configure-the-application-dashboard"></a>Az alkalmazás irányítópultjának konfigurálása

Az **irányítópult** az a lap, amely akkor töltődik be, amikor az alkalmazáshoz hozzáférő felhasználók megnyitják az alkalmazás URL-címét. Ha a **minta contoso** vagy a **minta Devkits** alkalmazás sablonját választotta az alkalmazás létrehozásához, az alkalmazás előre definiált irányítópulttal rendelkezik. Ha az **egyéni alkalmazás** -alkalmazás sablont választotta, az irányítópult üres.

> [!NOTE]
> A felhasználók az alapértelmezett alkalmazás-irányítópult helyett [saját személyes irányítópultokat](howto-personalize-dashboard.md) is létrehozhatnak.

## <a name="add-tiles"></a>Csempék hozzáadása

Az alábbi képernyőképen egy, a **minta contoso** -sablonból létrehozott alkalmazás irányítópultja látható. Az alkalmazás alapértelmezett irányítópultjának testreszabásához kattintson a lap jobb felső sarkában található **Szerkesztés** gombra.

![Az alkalmazások irányítópultja a "minta contoso" sablon alapján](media/howto-configure-homepage/image1a.png)

A **Szerkesztés**lehetőség kiválasztásával megnyílik az irányítópult-könyvtár panel. A függvénytár tartalmazza az irányítópult testreszabásához használható csempéket és irányítópult-primitíveket.

![Irányítópult-könyvtár](media/howto-configure-homepage/image2a.png)

Hozzáadhat például egy eszközbeállítások **és a tulajdonságok** csempét egy eszköz aktuális beállításainak és tulajdonságainak értékének megjelenítéséhez. Ehhez először válasszon ki egy **eszközt** , majd válassza ki az **eszköz példányát**. Ezután adjon meg egy címet a csempének, és válasszon egy **beállítást** vagy egy **tulajdonságot** , amelyet meg szeretne adni. Az alábbi képernyőfelvételen a csempéhez való hozzáadáshoz kiválasztott beállítások és tulajdonságok láthatók. Kattintson a **kész** gombra a módosítás az irányítópulton való mentéséhez.

!["Az eszköz részleteinek konfigurálása" űrlap a beállítások és a tulajdonságok részleteivel](media/howto-configure-homepage/image3a.png)

Most, amikor egy operátor megtekinti az alapértelmezett alkalmazás-irányítópultot, az új csempe jelenik meg az eszköz **beállított hőmérsékleti** beállításával:

!["Irányítópult" lap, amely megjeleníti a csempén látható beállításokat és tulajdonságokat](media/howto-configure-homepage/image4a.png)

A könyvtárban található egyéb csempe-típusok megismerésével megismerheti, hogyan szabhatja testre az alapértelmezett alkalmazás-irányítópultot.

Ha többet szeretne megtudni a csempék használatáról az Azure IoT Centralban, tekintse meg az [irányítópult-csempék használatát](howto-use-tiles.md)ismertető témakört.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central alapértelmezett alkalmazás-irányítópultjának konfigurálását, a következőket teheti:

> [!div class="nextstepaction"]
> [Útmutató képek előkészítéséhez és feltöltéséhez](howto-prepare-images.md)
