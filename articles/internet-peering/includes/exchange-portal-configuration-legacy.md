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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678550"
---
1. A **Társviszony-létesítés létrehozása** lap **Konfiguráció** lapján töltse ki az itt látható mezőket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési lap létrehozása Exchange-társviszony-létesítési típus](../media/setup-exchange-conf-tab.png)

    * **Társviszony-létesítési típus esetén**válassza az **Exchange**lehetőséget.
    * Válassza a **Termékváltozat** **alapszintű ingyenesként**lehetőséget.
    * Válassza ki azt a **metro** helyet, ahol a társviszony-létesítést Azure-erőforrássá szeretné alakítani. Ha a microsofttal olyan létesítési kapcsolatokkal rendelkezik a kiválasztott **Metro** helyen, amelyek nem konvertálódnak Azure-erőforrássá, ezek a kapcsolatok a **Társviszony-létesítési kapcsolatok** szakaszban jelennek meg a látható módon. Most már konvertálhatja ezeket a társviszony-létesítési kapcsolatokat egy Azure-erőforrássá.

        > [!div class="mx-imgBorder"]
        > ![Társviszony-létesítési kapcsolatok listája](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Az örökölt társviszony-létesítési kapcsolatok beállításai nem módosíthatók. Ha további társviszony-létesítési kapcsolatokat szeretne hozzáadni a Microsofttal a kijelölt **metro** helyen, válassza az **Új létrehozása lehetőséget.** További információt az [Exchange-társviszony-létesítés létrehozása vagy módosítása a portál használatával című](../howto-exchange-portal.md)témakörben talál.
        >

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Figyelje meg, hogy a portál a megadott adatok alapvető érvényesítését futtatja. A tetején egy szalag ontja a *Futó végső érvényesítés üzenet...*.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-érvényesítés lap](../media/setup-direct-review-tab-validation.png)

1. Miután az üzenet érvényességi módosítása *átkerült,* ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás**lehetőség kiválasztásával. Ha módosítania kell a kérését, válassza **az Előző** lehetőséget, és ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés irtása](../media/setup-exchange-review-tab-submit.png)

1. A kérelem elküldése után várja meg, amíg a központi telepítés befejeződik. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres telepítés az ábrán látható módon jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési siker](../media/setup-direct-success.png)
