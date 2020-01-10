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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774496"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján töltse ki a mezőket az alább látható módon.

    > [!div class="mx-imgBorder"]
    > ![peering Configuration – Exchange](../media/setup-exchange-conf-tab.png)

    * A **társ**-kezelés típusa beállításnál válassza az *Exchange*lehetőséget.
    * Válassza az SKU *alapszintű ingyenes*lehetőséget.
    * Válassza ki azt a **Metro** -helyet, ahová be szeretné állítani a társítást.

        > [!NOTE]
        > Ha a kiválasztott **Metro** -helyen már rendelkezik a Microsofttal való kapcsolattal, és a portálon először használja a társítást ezen a helyen, akkor a meglévő társ-összevonási kapcsolatok az alább látható módon jelennek meg a társítási **kapcsolatok** szakaszban. A Microsoft automatikusan átalakítja ezeket az összekapcsolási kapcsolatokat az Azure-erőforrással, hogy az új kapcsolatokkal együtt kezelje őket egy helyen. További információ: [örökölt Exchange-társítás átalakítása Azure-erőforrásra a portál használatával](../howto-legacy-exchange-portal.md) .
        >

1. A társítási **kapcsolatok**területen kattintson az **új létrehozása** elemre, és adjon hozzá egy sort minden új kapcsolathoz, amelyet be szeretne állítani.

    * A kapcsolódási beállítások konfigurálásához/módosításához kattintson a sor Szerkesztés gombjára.

        > [!div class="mx-imgBorder"]
        > ![peering Configuration – Exchange Edit](../media/setup-exchange-conf-tab-edit.png)

    * Egy sor törléséhez kattintson a következőre: **...** gombra > **delete**.

        > [!div class="mx-imgBorder"]
        > ![peering Configuration – Exchange Edit](../media/setup-exchange-conf-tab-delete.png)

    * A kapcsolatok összes beállítását az alább látható módon kell megadnia.

         > [!div class="mx-imgBorder"]
         > ![peering Configuration – Exchange-kapcsolatok](../media/setup-exchange-conf-tab-connection.png)

        1. Válassza ki azt a társítási **létesítményt** , ahol a kapcsolatot be kell állítani.
        1. Az IPv4- **cím** és az **IPv6-cím**mezőben adja meg az IPv4-és IPv6-címeket, amelyek a szomszéd parancs használatával konfigurálhatók a Microsoft-útválasztókon.
        1. Adja meg azoknak az IPv4-és IPv6-ELŐTAGOKNAK a számát, amelyeket a meghirdetett **IPv4-címek** és a **meghirdetett IPv6-címek** maximális száma mezőben fog reklámozni.
        1. A kapcsolódási beállítások mentéséhez kattintson **az OK** gombra.

1. Ismételje meg a fenti lépést további kapcsolatok hozzáadásához bármely olyan létesítményben, ahol a Microsoft a hálózattal együtt található a korábban kiválasztott **metrón** belül.

1. Az összes szükséges kapcsolat hozzáadását követően kattintson a **felülvizsgálat + létrehozás**gombra.

    > [!div class="mx-imgBorder"]
    > ![a társítási konfiguráció lap végleges](../media/setup-exchange-conf-tab-final.png)

1. Figyelje meg, hogy a portál a beírt információk alapszintű érvényesítését futtatja. Ez a felső menüszalagon jelenik meg, a *végső ellenőrzés futtatásával..* .

    > [!div class="mx-imgBorder"]
    > ![társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Miután bekapcsolta az *érvényesítést*, ellenőrizze az adatokat, és küldje el a kérést a **Létrehozás**gombra kattintva. Ha módosítania kell a kérelmét, kattintson az **előző** gombra, és ismételje meg a fenti lépéseket.

    > [!div class="mx-imgBorder"]
    > ![társ-beküldési](../media/setup-exchange-review-tab-submit.png)

1. Miután elküldte a kérést, várja meg, amíg befejeződik az üzembe helyezés. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés az alábbi módon fog megjelenni.

    > [!div class="mx-imgBorder"]
    > ![a sikeres társ-kezelés](../media/setup-direct-success.png)
