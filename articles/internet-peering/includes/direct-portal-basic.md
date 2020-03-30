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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774639"
---
1. Kattintson **az Erőforrás** > létrehozása**Az összes megtekintése gombra.**

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés keresése](../media/setup-seeall.png)

1. Keressen rá a *Társviszony-létesítés* elemre a keresőmezőben, és nyomja meg az *Enter* billentyűt a billentyűzeten. Az eredmények, kattintson a **Társviszony-létesítés** erőforrás.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés indítása](../media/setup-launch.png)

1. A **Társviszony-létesítés** elindítása után tekintse át az oldalt a részletek megértéséhez. Ha készen áll, kattintson a **Létrehozás gombra.**

    > [!div class="mx-imgBorder"]
    > ![Társviszony létesítéslétrehozása](../media/setup-create.png)

1. A **Társviszony-létesítés létrehozása** lap **Alapjai** lapján töltse ki a mezőket az alábbi módon.

    > [!div class="mx-imgBorder"]
    > ![A társviszony-létesítés alapjai](../media/setup-basics-tab.png)

    * Válassza ki **Azure-előfizetését.**
    * Az **Erőforráscsoport legördülő**menüben választhat egy meglévő erőforráscsoportot, vagy létrehozhat egy új csoportot az **Új létrehozása**gombra kattintva. Ehhez a példához létrehozunk egy új erőforráscsoportot.
    * **A név** az erőforrás nevének felel meg, és bármi lehet, amit csak választhat.
    * **A terület** automatikusan be van jelölve, ha a fenti lépésben egy meglévő erőforráscsoportot választott. Ha úgy döntött, hogy hozzon létre egy új erőforráscsoportot, majd ki kell választania azt az Azure-régiót is, ahol az erőforrást szeretné. USA keleti régiója

        > [!NOTE]
        > Az a régió, ahol az erőforráscsoport található, független attól a helytől, ahol társviszony-létesítést szeretne létrehozni a Microsofttal. Ajánlott azonban a társviszony-létesítési erőforrásokat a legközelebbi Azure-régiókban található erőforráscsoportokba szervezni. Pl.: ashburni társviszony-létesítéshez létrehozhat egy erőforráscsoportot *az USA keleti részén* vagy az USA keleti *részén2*

    * Válassza ki az ASN-t a **Társ ASN** mezőben.

        > [!IMPORTANT]
        > * Csak akkor választhat olyan ASN-t, amelynek ValidationState állapota "Jóváhagyva" lesz, mielőtt társviszony-létesítési kérelmet küldene. Ha csak benyújtotta a PeerAsn kérelmet, várjon 12 órát, vagy ha az ASN-társítás "Jóváhagyva" lesz. Ha a kiválasztott ASN érvényesítésre vár, hibaüzenet jelenik meg. 
        > * Ha nem látja a választandó ASN-t, ellenőrizze, hogy a megfelelő előfizetést választotta-e. Ha igen, ellenőrizze, hogy már létrehozta-e a PeerAsn-t [az Azure-előfizetéshez társtársASN](../howto-subscription-association-portal.md)használatával.

        > [!div class="mx-imgBorder"]
        > ![A társviszony-létesítés alapjai kitöltve](../media/setup-direct-basics-filled-tab.png)

    * Kattintson a **Tovább: A >konfigurálása** a folytatáshoz.
