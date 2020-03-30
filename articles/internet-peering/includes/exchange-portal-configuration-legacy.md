---
title: fájl belefoglalása
titleSuffix: Azure
description: fájl belefoglalása
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775211"
---
1. A **Társviszony-létesítés létrehozása** lap **Konfiguráció** lapján töltse ki a mezőket az alábbi módon.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési konfiguráció - Exchange](../media/setup-exchange-conf-tab.png)

    * **Társviszony-létesítési típus esetén**válassza az *Exchange*lehetőséget.
    * Válassza a **Termékváltozat** *alapszintű ingyenesként*lehetőséget.
    * Válassza ki a **metro** helyét, ahol szeretné átalakítani társviszony-létesítés az Azure-erőforrás. Ha a microsofttal olyan társviszony-létesítési kapcsolatok vannak a kiválasztott **Metro-helyen,** amelyek nem konvertálódnak Azure-erőforrássá, akkor az ilyen kapcsolatok az alábbi **társviszony-létesítési kapcsolatok** szakaszban jelennek meg. Most már konvertálhatja ezeket a társviszony-létesítési kapcsolatokat Azure-erőforrássá.

        > [!div class="mx-imgBorder"]
        > ![Társviszony-létesítési konfiguráció - Exchange – Örökölt kapcsolatok](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Az örökölt társviszony-létesítési kapcsolatok beállításai nem módosíthatók. Ha további társviszony-létesítési kapcsolatokat szeretne hozzáadni a Microsofttal a kiválasztott **Metro** helyen, ezt az **Új** létrehozása gombra kattintva teheti meg. További információ: [Exchange-társviszony-létesítés létrehozása vagy módosítása a portálon.](../howto-exchange-portal.md)
        >

1. Kattintson a **Véleményezés + create gombra.** Figyelje meg, hogy a portál futtatja a megadott adatok alapvető érvényesítését. Ez egy szalagon jelenik meg a tetején, mint *futás végső érvényesítés...*.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-érvényesítés lap](../media/setup-direct-review-tab-validation.png)

1. Miután az *Érvényesítés i d.* elemre vált, ellenőrizze az adatokat, és küldje el a kérelmet a **Létrehozás**gombra kattintva. Ha módosítania kell a kérését, kattintson az **Előző gombra,** és ismételje meg a fenti lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés iküldött](../media/setup-exchange-review-tab-submit.png)

1. Miután elküldte a kérelmet, várja meg, amíg befejeződik a központi telepítés. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres telepítés az alábbiak szerint jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési siker](../media/setup-direct-success.png)
