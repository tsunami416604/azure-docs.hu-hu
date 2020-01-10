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
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775068"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján töltse ki a mezőket az alább látható módon.

    > [!div class="mx-imgBorder"]
    > ![társ-konfigurálás – közvetlen](../media/setup-direct-conf-tab.png)

    * A **társ**-kezelés típusa beállításnál válassza a *közvetlen*lehetőséget.
    * A **Microsoft Network**esetében válassza a *AS8075*lehetőséget. Ne válassza az ASN 8069 lehetőséget. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft-partnerek](mailto:peering@microsoft.com)használják.
    * Válassza az SKU *alapszintű ingyenes*lehetőséget. Ne válassza a *prémium szintű ingyenes* lehetőséget, mert a speciális alkalmazások számára van fenntartva.
    * Válassza ki azt a **Metro** -helyet, ahol át szeretné alakítani az Azure-erőforrást. Ha a kiválasztott **metróban** nem az Azure-erőforrásra konvertált kapcsolattal rendelkezik a Microsofttal, akkor az ilyen kapcsolatok az alább látható módon lesznek felsorolva a társítási **kapcsolatok** szakaszban. Most már konvertálhatja ezeket a társ-kapcsolatokat az Azure-erőforráshoz.

        > [!div class="mx-imgBorder"]
        > ![peering Configuration – Direct – örökölt kapcsolatok](../media/setup-directlegacy-conf-tab.png)

1. Ha frissítenie kell a sávszélességet, a kapcsolati beállítások módosításához kattintson a vonal Szerkesztés gombjára.

    > [!div class="mx-imgBorder"]
    > ![peering Configuration – közvetlen szerkesztés](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Ha a kiválasztott **Metro** -helyen további társi kapcsolatokat szeretne hozzáadni a Microsofthoz, az **új gomb létrehozása** gombra kattintva teheti meg. További információért lásd: [közvetlen társítás létrehozása vagy módosítása a portál használatával](../howto-direct-portal.md) .
    >

1. Kattintson a **felülvizsgálat + létrehozás**gombra. Figyelje meg, hogy a portál a beírt információk alapszintű érvényesítését futtatja. Ez a felső menüszalagon jelenik meg, a *végső ellenőrzés futtatásával..* .

    > [!div class="mx-imgBorder"]
    > ![társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Miután bekapcsolta az *érvényesítést*, ellenőrizze az adatokat, és küldje el a kérést a **Létrehozás**gombra kattintva. Ha módosítania kell a kérelmét, kattintson az **előző** gombra, és ismételje meg a fenti lépéseket.

    > [!div class="mx-imgBorder"]
    > ![társ-beküldési](../media/setup-direct-review-tab-submit.png)

1. Miután elküldte a kérést, várja meg, amíg befejeződik az üzembe helyezés. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés az alábbi módon fog megjelenni.

    > [!div class="mx-imgBorder"]
    > ![a sikeres társ-kezelés](../media/setup-direct-success.png)
