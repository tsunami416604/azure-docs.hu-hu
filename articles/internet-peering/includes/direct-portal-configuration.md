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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681063"
---
1. A **Társviszony-létesítés létrehozása** lap **Konfiguráció** lapján töltse ki az itt látható mezőket.

    * **Társviszony-létesítési típus esetén**válassza a **Közvetlen**lehetőséget.
    * **Microsoft-hálózat**esetén válassza az **AS8075**lehetőséget. Ne hozzon létre társviszony-létesítést az ASN 8069-tel. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft társviszony-létesítési](mailto:peering@microsoft.com).
    * Válassza a **Termékváltozat** **alapszintű ingyenesként**lehetőséget. Ne válassza a Premium Free lehetőséget, mert speciális alkalmazásokszámára van fenntartva.
    * Válassza ki azt a **metróhelyet,** ahol be szeretné állítani a társviszony-létesítést.

        > [!NOTE]
        > Ha már rendelkezik társviszony-létesítési kapcsolattal a Microsofttal a kiválasztott **metro** helyen, és először használja az Azure Portalon a társviszony-létesítés beállításához az adott helyen, a meglévő társviszony-létesítési kapcsolatok a **Társviszony-létesítési kapcsolatok** szakaszban jelennek meg, ahogy látható. A Microsoft automatikusan átalakítja ezeket a társviszony-létesítési kapcsolatokat egy Azure-erőforrássá, így egy helyen kezelheti őket az új kapcsolatokkal együtt. További információ: [Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával.](../howto-legacy-direct-portal.md)
        >

1. A **Társviszony-létesítési kapcsolatok csoportban**jelölje be az **Új létrehozása** jelölőnégyzetet, ha minden új kapcsolatot be szeretne állítani.

    * A kapcsolatbeállítások beállításához vagy módosításához jelölje ki a vonal szerkesztési gombját.

        > [!div class="mx-imgBorder"]
        > ![Szerkesztés gomb](../media/setup-direct-conf-tab-edit.png)
    
    * Egy sor törléséhez válassza **a ...**  >  **Törölje**a gombot.

        > [!div class="mx-imgBorder"]
        > ![Törlés gomb](../media/setup-direct-conf-tab-delete.png)

    * Meg kell adnia a kapcsolat összes beállítását, ahogy az itt látható.

         > [!div class="mx-imgBorder"]
         > ![Közvetlen társviszony-létesítési kapcsolat lap](../media/setup-direct-conf-tab-connection.png)

        1. Válassza ki azt a **társviszony-létesítési lehetőséget,** ahol a kapcsolatot be kell állítani.
        1. **A munkamenet-címszolgáltató** segítségével határozható meg, hogy ki biztosítja a BGP-munkamenet beállításához szükséges alhálózatot a hálózat és a Microsoft között. Ha meg tudja adni az alhálózatot, válassza a **Társ**lehetőséget. Ellenkező esetben válassza ki a **Microsoft** és [a Microsoft társviszony-létesítés](mailto:peering@microsoft.com) felveszi Önnel a kapcsolatot. Ha ezt a beállítást választja, a Microsoft hosszabb időt vesz igénybe a társviszony-létesítési kérelem feldolgozásához. Bizonyos esetekben előfordulhat, hogy a Microsoft nem tud alhálózatokat biztosítani, ami a kérelmek megtagadását eredményezi.
        1. Ha a **Munkamenet-címszolgáltató** beállítást választotta **társként,** adja meg az IPv4- és IPv6-címeket az előtagmaszkokkal együtt a **Munkamenet IPv4-előtag** és a **Munkamenet IPv6 előtag** mezőiben.
        1. Adja meg a **hirdetett IPv4-címek maximális száma** és a **Maximálisan meghirdetett IPv6-címek** mezőben hirdetendő IPv4- és IPv6-előtagok számát.
        1. Állítsa be a **Teljes sávszélesség** csúszkát, hogy tükrözze a kapcsolat sávszélességét.
        1. A **kapcsolatbeállításainak** mentéséhez válassza a Mentés gombot.

1. Ismételje meg az előző lépést, ha további kapcsolatokat szeretne hozzáadni bármely olyan létesítményben, ahol a Microsoft a hálózattal együtt helyezkedik el, a korábban kiválasztott **metrón** belül.

1. Miután hozzáadja az összes szükséges kapcsolatot, válassza a **Véleményezés + create**lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési konfiguráció lap végleges](../media/setup-direct-conf-tab-final.png)

1. Figyelje meg, hogy a portál a megadott adatok alapvető érvényesítését futtatja. A tetején egy szalag ontja a *Futó végső érvényesítés üzenet...*.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-érvényesítés lap](../media/setup-direct-review-tab-validation.png)

1. Miután az üzenet érvényességi módosítása *átkerült,* ellenőrizze az adatokat. Küldje el a kérelmet a **Létrehozás**lehetőség kiválasztásával. A kérés módosításához válassza az **Előző** lehetőséget, és ismételje meg a lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés irtása](../media/setup-direct-review-tab-submit.png)

1. A kérelem elküldése után várja meg, amíg befejeződik az üzembe helyezés. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres üzembe helyezés az itt látható módon jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési siker](../media/setup-direct-success.png)
