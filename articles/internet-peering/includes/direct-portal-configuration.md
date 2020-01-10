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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775276"
---
1. A társítás **létrehozása** lap **konfiguráció** lapján töltse ki a mezőket az alább látható módon.

    * A **társ**-kezelés típusa beállításnál válassza a *közvetlen*lehetőséget.
    * A **Microsoft Network**esetében válassza a *AS8075*lehetőséget. Ne hozzon létre társítást ASN 8069-mel. Speciális alkalmazások számára van fenntartva, és csak a [Microsoft-partnerek](mailto:peering@microsoft.com)használják.
    * Válassza az SKU *alapszintű ingyenes*lehetőséget. Ne válassza a *prémium szintű ingyenes* lehetőséget, mert a speciális alkalmazások számára van fenntartva.
    * Válassza ki azt a **Metro** -helyet, ahová be szeretné állítani a társítást.

        > [!NOTE]
        > Ha a kiválasztott **Metro** -helyen már rendelkezik a Microsofttal való kapcsolattal, és a portálon először használja a társítást ezen a helyen, akkor a meglévő társ-összevonási kapcsolatok az alább látható módon jelennek meg a társítási **kapcsolatok** szakaszban. A Microsoft automatikusan átalakítja ezeket az összekapcsolási kapcsolatokat az Azure-erőforrással, hogy az új kapcsolatokkal együtt kezelje őket egy helyen. További információért lásd: [örökölt közvetlen társak átalakítása Azure-erőforrásra a portál használatával](../howto-legacy-direct-portal.md) .
        >

1. A társítási **kapcsolatok**területen kattintson az **új létrehozása** elemre, és adjon hozzá egy sort minden új kapcsolathoz, amelyet be szeretne állítani.

    * A kapcsolódási beállítások konfigurálásához/módosításához kattintson a sor Szerkesztés gombjára.

        > [!div class="mx-imgBorder"]
        > ![peering Configuration – közvetlen szerkesztés](../media/setup-direct-conf-tab-edit.png)
    
    * Egy sor törléséhez kattintson a következőre: **...** gombra > **delete**.

        > [!div class="mx-imgBorder"]
        > ![peering Configuration – közvetlen szerkesztés](../media/setup-direct-conf-tab-delete.png)

    * A kapcsolatok összes beállítását az alább látható módon kell megadnia.

         > [!div class="mx-imgBorder"]
         > ![peering Configuration – közvetlen kapcsolatok](../media/setup-direct-conf-tab-connection.png)

        1. Válassza ki azt a társítási **létesítményt** , ahol a kapcsolatot be kell állítani.
        1. A **munkamenet-címtartomány** segítségével megállapítható, hogy ki biztosítja a hálózat és a Microsoft közötti BGP-munkamenet beállításához szükséges alhálózatot. Ha meg tudja adni az alhálózatot, válassza a *társ*lehetőséget. Másik lehetőségként válassza a **Microsoft** és a [Microsoft-partnerek](mailto:peering@microsoft.com) felveszik Önnel a kapcsolatot. Vegye figyelembe, hogy a kiválasztásakor a Microsoft számára hosszabb időt kellene igénybe venni a partneri kérelem feldolgozására. Bizonyos esetekben előfordulhat, hogy a Microsoft nem tud olyan alhálózatokat biztosítani, amelyek a kérelmek elutasítását eredményezik.
        1. Ha a **munkamenet-cím szolgáltatót** a *társként*választotta, akkor adja meg az IPv4-és IPv6-címeket az előtag maszkkal együtt a mezők **munkamenet IPv4-előtag** és a **munkamenet IPv6-előtag** mezőben.
        1. Adja meg azoknak az IPv4-és IPv6-ELŐTAGOKNAK a számát, amelyeket a meghirdetett **IPv4-címek** és a **meghirdetett IPv6-címek** maximális száma mezőben fog reklámozni.
        1. Módosítsa a **teljes sávszélesség** csúszkát, hogy tükrözze a kapcsolat sávszélességét.
        1. A kapcsolódási beállítások mentéséhez kattintson **az OK** gombra.

1. Ismételje meg a fenti lépést további kapcsolatok hozzáadásához bármely olyan létesítményben, ahol a Microsoft a hálózattal együtt található a korábban kiválasztott **metrón** belül.

1. Az összes szükséges kapcsolat hozzáadását követően kattintson a **felülvizsgálat + létrehozás**gombra.

    > [!div class="mx-imgBorder"]
    > ![a társítási konfiguráció lap végleges](../media/setup-direct-conf-tab-final.png)

1. Figyelje meg, hogy a portál a beírt információk alapszintű érvényesítését futtatja. Ez a felső menüszalagon jelenik meg, a *végső ellenőrzés futtatásával..* .

    > [!div class="mx-imgBorder"]
    > ![társ-ellenőrzés lap](../media/setup-direct-review-tab-validation.png)

1. Miután bekapcsolta az *érvényesítést*, ellenőrizze az adatokat, és küldje el a kérést a **Létrehozás**gombra kattintva. Ha módosítania kell a kérelmét, kattintson az **előző** gombra, és ismételje meg a fenti lépéseket.

    > [!div class="mx-imgBorder"]
    > ![társ-beküldési](../media/setup-direct-review-tab-submit.png)

1. Miután elküldte a kérést, várja meg, amíg befejeződik az üzembe helyezés. Ha a telepítés sikertelen, forduljon a [Microsoft-partneri](mailto:peering@microsoft.com)kapcsolathoz. A sikeres üzembe helyezés az alábbi módon fog megjelenni.

    > [!div class="mx-imgBorder"]
    > ![a sikeres társ-kezelés](../media/setup-direct-success.png)
