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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678581"
---
1. Válassza **az Erőforrás** > létrehozása**Az összes megtekintése lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés keresése](../media/setup-seeall.png)

1. Keressen rá a **társviszony-létesítésre** a keresőmezőben, és válassza az **Enter** elemet a billentyűzeten. Az eredmények közül válasszon egy **társviszony-létesítési erőforrást.**

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítés indítása](../media/setup-launch.png)

1. A **társviszony-létesítés** megkezdése után tekintse át az oldalt a részletek megértéséhez. Ha készen áll, válassza a **Létrehozás gombot.**

    > [!div class="mx-imgBorder"]
    > ![Társviszony létesítéslétrehozása](../media/setup-create.png)

1. A **Társviszony-létesítés létrehozása** lap **Alapjai** lapján töltse ki az itt látható mezőket.

    > [!div class="mx-imgBorder"]
    > ![A társviszony-létesítés alapjai lap](../media/setup-basics-tab.png)

    * Válassza ki az **Azure-előfizetést.**
    * Az **Erőforráscsoport csoportban**választhat egy meglévő erőforráscsoportot a legördülő listából, vagy létrehozhat egy új csoportot az **Új létrehozása**lehetőség kiválasztásával. Ehhez a példához hozzunk létre egy új erőforráscsoportot.
    * **A név** megfelel az erőforrás nevének, és bármi lehet, amit csak választhat.
    * **A terület** automatikusan ki van jelölve, ha meglévő erőforráscsoportot választott. Ha úgy döntött, hogy hozzon létre egy új erőforráscsoportot, akkor is ki kell választania az Azure-régióban, ahol az erőforrás t akarja, hogy.

        > [!NOTE]
        > Az a régió, ahol egy erőforráscsoport található, független attól a helytől, ahol társviszony-létesítést szeretne létrehozni a Microsofttal. De ajánlott eljárás a társviszony-létesítési erőforrások at erőforráscsoportokon belül, amelyek a legközelebbi Azure-régiókban található. Például ashburni társviszony-létesítések esetén létrehozhat egy erőforráscsoportot az USA keleti részén vagy az USA keleti részén2.

    * Válassza ki az ASN-t a **Társ ASN** mezőben.

        > [!IMPORTANT]
        > * Csak társviszony-létesítési kérelem elküldése előtt választhat asn-t jóváhagyottként validationstate néven. Ha csak benyújtotta a PeerAsn kérelmet, várjon 12 órát, vagy ha az ASN-társítás jóváhagyása. Ha a kiválasztott ASN érvényesítésre vár, hibaüzenet jelenik meg. 
        > * Ha nem látja a választandó ASN-t, ellenőrizze, hogy a megfelelő előfizetést választotta-e. Ha igen, ellenőrizze, hogy már létrehozta-e a PeerAsn-t az [Azure-előfizetéshez](../howto-subscription-association-portal.md)társítva.

        > [!div class="mx-imgBorder"]
        > ![Kitöltött társviszony-létesítési alapismeretek](../media/setup-direct-basics-filled-tab.png)

    * Válassza a **Tovább lehetőséget: A** folytatáshoz >.
