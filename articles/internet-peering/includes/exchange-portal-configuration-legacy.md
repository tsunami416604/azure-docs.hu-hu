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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775211"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján töltse ki a mezőket az alább látható módon.

    > [!div class="mx-imgBorder"]
    > ![peering Configuration – Exchange](../media/setup-exchange-conf-tab.png)

    * A **társ**-kezelés típusa beállításnál válassza az *Exchange*lehetőséget.
    * Válassza az SKU *alapszintű ingyenes*lehetőséget.
    * Válassza ki azt a **Metro** -helyet, ahol át szeretné alakítani az Azure-erőforrást. Ha a kiválasztott **metróban** nem az Azure-erőforrásra konvertált kapcsolattal rendelkezik a Microsofttal, akkor az ilyen kapcsolatok az alább látható módon lesznek felsorolva a társítási **kapcsolatok** szakaszban. Most már konvertálhatja ezeket a társ-kapcsolatokat az Azure-erőforráshoz.

        > [!div class="mx-imgBorder"]
        > ![peering Configuration-Exchange-Legacy kapcsolatok](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Az örökölt társítási kapcsolatok beállításai nem módosíthatók. Ha a kiválasztott **Metro** -helyen további társi kapcsolatokat szeretne hozzáadni a Microsofthoz, az **új gomb létrehozása** gombra kattintva teheti meg. További információért lásd: [Exchange-társ létrehozása vagy módosítása a portál használatával](../howto-exchange-portal.md) .
        >

1. Kattintson a **felülvizsgálat + létrehozás**gombra. Figyelje meg, hogy a portál a beírt információk alapszintű érvényesítését futtatja. Ez a felső menüszalagon jelenik meg, a *végső ellenőrzés futtatásával..* .

    > [!div class="mx-imgBorder"]
    > ![társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Miután bekapcsolta az *érvényesítést*, ellenőrizze az adatokat, és küldje el a kérést a **Létrehozás**gombra kattintva. Ha módosítania kell a kérelmét, kattintson az **előző** gombra, és ismételje meg a fenti lépéseket.

    > [!div class="mx-imgBorder"]
    > ![társ-beküldési](../media/setup-exchange-review-tab-submit.png)

1. Miután elküldte a kérést, várja meg, amíg befejeződik az üzembe helyezés. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés az alábbi módon fog megjelenni.

    > [!div class="mx-imgBorder"]
    > ![a sikeres társ-kezelés](../media/setup-direct-success.png)
