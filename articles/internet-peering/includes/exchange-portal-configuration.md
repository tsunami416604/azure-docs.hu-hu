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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680959"
---
1. A **Társviszony-létesítés létrehozása** lap **Konfiguráció** lapján töltse ki a mezőket az ábrán látható módon.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési lap létrehozása Exchange-társviszony-létesítési típus](../media/setup-exchange-conf-tab.png)

    * **Társviszony-létesítési típus esetén**válassza az **Exchange**lehetőséget.
    * Válassza a **Termékváltozat** **alapszintű ingyenesként**lehetőséget.
    * Válassza ki azt a **metróhelyet,** ahol be szeretné állítani a társviszony-létesítést.

        > [!NOTE]
        > Ha már rendelkezik társviszony-létesítési kapcsolatokkal a Microsofttal a kiválasztott **metro** helyen, és először használja a portált az adott helyen a társviszony-létesítés beállításához, a meglévő társviszony-létesítési kapcsolatok a **Társviszony-létesítési kapcsolatok** szakaszban jelennek meg az ábrán látható módon. A Microsoft automatikusan átalakítja ezeket a társviszony-létesítési kapcsolatokat egy Azure-erőforrássá, így egy helyen kezelheti őket az új kapcsolatokkal együtt. További információ: [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával.](../howto-legacy-exchange-portal.md)
        >

1. A **Társviszony-létesítési kapcsolatok csoportban**jelölje be az **Új létrehozása** jelölőnégyzetet, ha minden új kapcsolatot be szeretne állítani.

    * A kapcsolatbeállítások beállításához vagy módosításához jelölje ki a vonal szerkesztési gombját.

        > [!div class="mx-imgBorder"]
        > ![Szerkesztés gomb](../media/setup-exchange-conf-tab-edit.png)

    * Egy sor törléséhez válassza **a ...**  >  **Törölje**a gombot.

        > [!div class="mx-imgBorder"]
        > ![Törlés gomb](../media/setup-exchange-conf-tab-delete.png)

    * Meg kell adnia a kapcsolat összes beállítását, ahogy az itt látható.

         > [!div class="mx-imgBorder"]
         > ![Exchange-társviszony-létesítési kapcsolat lapja](../media/setup-exchange-conf-tab-connection.png)

        1. Válassza ki azt a **társviszony-létesítési lehetőséget,** ahol a kapcsolatot be kell állítani.
        1. Az **IPv4-cím** és az **IPv6-címmezőbe** írja be azokat az IPv4- és IPv6-címeket, amelyeket a Microsoft-útválasztókban a szomszéd paranccsal konfigurálna.
        1. Adja meg a **hirdetett IPv4-címek maximális száma** és a **Maximálisan meghirdetett IPv6-címek** mezőben hirdetendő IPv4- és IPv6-előtagok számát.
        1. A csatlakozási beállítások mentéséhez válassza az **OK gombot.**

1. Ismételje meg a lépést, ha további kapcsolatokat szeretne hozzáadni bármely olyan létesítményben, ahol a Microsoft a hálózattal együtt helyezkedik el, a korábban kiválasztott **metrón** belül.

1. Miután hozzáadja az összes szükséges kapcsolatot, válassza a **Véleményezés + create**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési konfiguráció lap](../media/setup-exchange-conf-tab-final.png)

1. Figyelje meg, hogy a portál a megadott adatok alapvető érvényesítését futtatja. A tetején egy szalag ontja a *Futó végső érvényesítés üzenet...*.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-érvényesítés lap](../media/setup-direct-review-tab-validation.png)

1. Miután az üzenet érvényességi módosítása *átkerült,* ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás**lehetőség kiválasztásával. A kérés módosításához válassza az **Előző** lehetőséget, és ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés irtása](../media/setup-exchange-review-tab-submit.png)

1. A kérelem elküldése után várja meg, amíg a központi telepítés befejeződik. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres üzembe helyezés az itt látható módon jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési siker](../media/setup-direct-success.png)
