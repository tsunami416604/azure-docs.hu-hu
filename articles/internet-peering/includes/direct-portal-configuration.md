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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775276"
---
1. A **Társviszony-létesítés létrehozása** lap **Konfiguráció** lapján töltse ki a mezőket az alábbi módon.

    * **Társviszony-létesítési típus esetén**válassza a *Közvetlen*lehetőséget.
    * **Microsoft-hálózat**esetén válassza az *AS8075*lehetőséget. Ne hozzon létre társviszony-létesítést az ASN 8069-tel. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft társviszony-létesítési](mailto:peering@microsoft.com).
    * Válassza a **Termékváltozat** *alapszintű ingyenesként*lehetőséget. Ne válassza a *Premium Free* lehetőséget, mivel speciális alkalmazásokhoz van fenntartva.
    * Válassza ki azt a **metro** helyet, ahol be szeretné állítani a társviszony-létesítést.

        > [!NOTE]
        > Ha már rendelkezik társviszony-létesítési kapcsolatokkal a Microsofttal a kiválasztott **metro** helyen, és első alkalommal használja a portált az adott helyen a társviszony-létesítés beállításához, akkor a meglévő társviszony-létesítési kapcsolatok az alábbi módon jelennek meg a **Társviszony-létesítési kapcsolatok** szakaszban. A Microsoft automatikusan konvertálja ezeket a társviszony-létesítési kapcsolatokat az Azure-erőforrásba, így egy helyen kezelheti őket az új kapcsolatokkal együtt. További információ: [Örökölt közvetlen társviszony-létesítés átalakítása azure-erőforrásba a portál használatával.](../howto-legacy-direct-portal.md)
        >

1. A **Társviszony-létesítési kapcsolatok csoportban**kattintson az **Új létrehozása** gombra, ha minden új kapcsolathoz hozzá szeretne adni egy vonalat.

    * A kapcsolatbeállítások konfigurálásához/módosításához kattintson a vonal szerkesztési gombjára.

        > [!div class="mx-imgBorder"]
        > ![Társviszony-létesítési konfiguráció – közvetlen szerkesztés](../media/setup-direct-conf-tab-edit.png)
    
    * Ha törölni szeretne egy sort, kattintson a **...** gombra > **Törlés gombra.**

        > [!div class="mx-imgBorder"]
        > ![Társviszony-létesítési konfiguráció – közvetlen szerkesztés](../media/setup-direct-conf-tab-delete.png)

    * A kapcsolat összes beállítását meg kell adnia az alábbiak szerint.

         > [!div class="mx-imgBorder"]
         > ![Társviszony-létesítési konfiguráció – közvetlen kapcsolat](../media/setup-direct-conf-tab-connection.png)

        1. Válassza ki azt a **társviszony-létesítési lehetőséget,** ahol a kapcsolatot be kell állítani.
        1. **A munkamenet-címszolgáltató** segítségével határozható meg, hogy ki biztosítja a BGP-munkamenet beállításához szükséges alhálózatot a hálózat és a Microsoft között. Ha alhálózatot tud biztosítani, válassza a *Társ*lehetőséget. Else válassza a **Microsoft** és [a Microsoft társviszony-létesítés](mailto:peering@microsoft.com) felveszi Önnel a kapcsolatot. Vegye figyelembe, hogy ha így választ, a Microsoft nak hosszabb időt kell igénybe vennie a társviszony-létesítési kérelem feldolgozása. Bizonyos esetekben előfordulhat, hogy a Microsoft nem tud olyan alhálózatokat biztosítani, amelyek a kérelmek elutasítását eredményezik.
        1. Ha **a Munkamenet-címszolgáltatót** választotta *társként,* akkor adja meg az IPv4- és IPv6-címet az előtagmaszkkal együtt a **Munkamenet IPv4-előtag** és a **Munkamenet IPv6-előtag** mezőkben.
        1. Adja meg a hirdetni kívánt IPv4- és IPv6-előtagok számát a **Maximálishirdetett IPv4-címek,** illetve a **Hirdetett IPv6-címek maximális** száma mezőben.
        1. Állítsa be a **Teljes sávszélesség** csúszkát, hogy tükrözze a kapcsolat sávszélességét.
        1. A csatlakozási beállítások mentéséhez kattintson az **OK** gombra.

1. Ismételje meg a fenti lépés, hogy adjunk további kapcsolatok at any létesítmény, ahol a Microsoft közös helyen található a hálózat, a **Metro** korábban kiválasztott.

1. Hozzáadása után az összes szükséges kapcsolatokat, kattintson a **Review + create**.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési konfigurációs lap végleges](../media/setup-direct-conf-tab-final.png)

1. Figyelje meg, hogy a portál futtatja a megadott adatok alapvető érvényesítését. Ez egy szalagon jelenik meg a tetején, mint *futás végső érvényesítés...*.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-érvényesítés lap](../media/setup-direct-review-tab-validation.png)

1. Miután az *Érvényesítés i d.* elemre vált, ellenőrizze az adatokat, és küldje el a kérelmet a **Létrehozás**gombra kattintva. Ha módosítania kell a kérését, kattintson az **Előző gombra,** és ismételje meg a fenti lépéseket.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés iküldött](../media/setup-direct-review-tab-submit.png)

1. Miután elküldte a kérelmet, várja meg, amíg befejeződik a központi telepítés. Ha a telepítés sikertelen, forduljon [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com). A sikeres telepítés az alábbiak szerint jelenik meg.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési siker](../media/setup-direct-success.png)
