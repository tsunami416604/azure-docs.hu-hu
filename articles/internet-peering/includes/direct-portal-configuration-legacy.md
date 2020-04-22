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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678818"
---
1. A **Társviszony-létesítés létrehozása** lap **Konfiguráció** lapján töltse ki az itt látható mezőket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési lap konfigurációja lap létrehozása](../media/setup-direct-conf-tab.png)

    * **Társviszony-létesítési típus esetén**válassza a **Közvetlen**lehetőséget.
    * **Microsoft-hálózat**esetén válassza az **AS8075**lehetőséget. Ne válassza az ASN 8069 lehetőséget. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft társviszony-létesítési](mailto:peering@microsoft.com).
    * Válassza a **Termékváltozat** **alapszintű ingyenesként**lehetőséget. Ne válassza a Premium Free lehetőséget, mert speciális alkalmazásokszámára van fenntartva.
    * Válassza ki azt a **metro** helyet, ahol a társviszony-létesítést Azure-erőforrássá szeretné alakítani. Ha a microsofttal olyan létesítési kapcsolatokkal rendelkezik a kiválasztott **Metro** helyen, amelyek nem konvertálódnak Azure-erőforrássá, ezek a kapcsolatok a **Társviszony-létesítési kapcsolatok** szakaszban jelennek meg a látható módon. Most már konvertálhatja ezeket a társviszony-létesítési kapcsolatokat egy Azure-erőforrássá.

        > [!div class="mx-imgBorder"]
        > ![Társviszony-létesítési kapcsolatok listája](../media/setup-directlegacy-conf-tab.png)

1. Ha frissítenie kell a sávszélességet, a kapcsolatbeállítások módosításához válassza a vonal szerkesztési gombját.

    > [!div class="mx-imgBorder"]
    > ![Szerkesztés gomb](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Ha további társviszony-létesítési kapcsolatokat szeretne hozzáadni a Microsofttal a kijelölt **metro** helyen, válassza az **Új létrehozása lehetőséget.** További információt a [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portál használatával](../howto-direct-portal.md)című témakörben talál.
    >

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Figyelje meg, hogy a portál a megadott adatok alapvető érvényesítését futtatja. A tetején egy szalag ontja a *Futó végső érvényesítés üzenet...*.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-érvényesítés lap](../media/setup-direct-review-tab-validation.png)

1. Miután az üzenet érvényességi módosítása *átkerült,* ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás**lehetőség kiválasztásával. A kérés módosításához válassza az **Előző** lehetőséget, és ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés irtása](../media/setup-direct-review-tab-submit.png)

1. A kérelem elküldése után várja meg, amíg a központi telepítés befejeződik. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres üzembe helyezés az itt látható módon jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési siker](../media/setup-direct-success.png)
