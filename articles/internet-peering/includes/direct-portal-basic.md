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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774639"
---
1. Kattintson az **erőforrás létrehozása** > **az összes**megjelenítése elemre.

    > [!div class="mx-imgBorder"]
    > ![keresési társítás](../media/setup-seeall.png)

1. Keressen rá a *Keresés a keresőmezőbe* , és nyomja le az *ENTER billentyűt* a billentyűzeten. Az eredmények között kattintson a **társ** -erőforrás elemre.

    > [!div class="mx-imgBorder"]
    > ![a peering](../media/setup-launch.png) elindítása

1. A **társítás elindítása után** tekintse át az oldalt a részletek megismeréséhez. Ha elkészült, kattintson a **Létrehozás**gombra.

    > [!div class="mx-imgBorder"]
    > ![társ-létrehozási](../media/setup-create.png) létrehozása

1. A társítás **létrehozása** lap **alapok** lapján töltse ki a mezőket az alább látható módon.

    > [!div class="mx-imgBorder"]
    > ![a peering alapjai](../media/setup-basics-tab.png)

    * Válassza ki az Azure- **előfizetését**.
    * Az **erőforráscsoport**esetében választhat egy meglévő erőforráscsoportot a legördülő listából, vagy létrehozhat egy új csoportot az **új létrehozása**elemre kattintva. Ehhez a példához létre fogunk hozni egy új erőforráscsoportot.
    * A **név** megegyezik az erőforrás nevével, és tetszőlegesen választhatja ki.
    * A **régió** automatikusan ki van választva, ha a fenti lépésben kiválasztott egy meglévő erőforráscsoportot. Ha úgy döntött, hogy új erőforráscsoportot hoz létre, akkor azt az Azure-régiót is ki kell választania, ahol az erőforrást tárolni szeretné. USA keleti régiója

        > [!NOTE]
        > Az a régió, ahol az erőforráscsoport található, független attól a helytől, ahol a Microsofttal kívánja létrehozni a kapcsolatot. Az ajánlott eljárás azonban az, hogy a legközelebb az Azure-régiókban található erőforráscsoportok között szervezzen egyenrangú erőforrásokat. Például: a Ashburn-ben az *USA keleti* régiójában vagy *Kelet-régiója* is létrehozhat egy erőforráscsoportot.

    * Válassza ki az ASN-t a **társ ASN** mezőben.

        > [!IMPORTANT]
        > * A ValidationState csak a "jóváhagyva" értékkel választhatja, mielőtt elküldené a kérést. Ha most elküldte a PeerAsn kérelmet, várjon 12 órát, vagy az ASN-társítást "jóváhagyva". Ha a kiválasztott ASN-konfiguráció érvényessége függőben van, hibaüzenet jelenik meg. 
        > * Ha nem látja a kiválasztani kívánt ASN-t, ellenőrizze, hogy a megfelelő előfizetést választotta-e. Ha igen, ellenőrizze, hogy már létrehozott-e PeerAsn az [Azure-előfizetéshez való társítási társ-hozzárendeléssel](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![peering alapjai kitöltve](../media/setup-direct-basics-filled-tab.png)

    * Kattintson a **Next (tovább) gombra: a Configuration >** a folytatáshoz.
