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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678859"
---
1. Nyissa meg az **Erőforráscsoportok lehetőséget,** és jelölje ki a **Társviszony-létesítési** erőforrás létrehozásakor kijelölt erőforráscsoportot. Ha túl sok erőforráscsoport van, használja a **Szűrő** mezőt.

    > [!div class="mx-imgBorder"]
    > ![Erőforráscsoportok](../media/setup-direct-get-resourcegroup.png)

1. Jelölje ki a létrehozott **társviszony-létesítési erőforrást.**

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-get-open.png)

1. **Az Áttekintés** lap magas szintű információkat jelenít meg, ahogy az itt látható.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás – áttekintés ablaktábla](../media/setup-direct-get-overview.png)

1. A bal oldalon válassza az **ASN-adatok at** a PeerAsn létrehozásakor elküldött információk megtekintéséhez.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás ASN-adatai](../media/setup-direct-get-asninfo.png)

1. A bal oldalon válassza a **Kapcsolatok**lehetőséget. A képernyő tetején az ASN és a Microsoft közötti társviszony-létesítési kapcsolatok összegzése látható a metró különböző létesítményei között. A kapcsolatok összegzését az **Áttekintés** lapon is elérheti, ha az ablaktábla közepén a **Kapcsolatok** lehetőséget választja, ahogy az látható.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás-kapcsolatok](../media/setup-direct-get-connectionssummary.png)

    * **A kapcsolat állapota** megfelel a társviszony-létesítési beállítások állapotának. Az ebben a mezőben megjelenő állapotok a [Közvetlen társviszony-létesítés forgatókönyvében látható állapotábrát követik.](../walkthrough-direct-all.md)
    * **Az IPv4-munkamenet-állapot,** illetve az **IPv6-munkamenet-állapot** megfelel az IPv4- és az IPv6 BGP-munkamenet állapotának. 
    * Amikor kijelöl egy sort a képernyő tetején, az alsó **Kapcsolat** szakasz az egyes kapcsolatok részleteit jeleníti meg. Válassza ki a **Konfiguráció**, **IPv4-cím**és **IPv6-cím**kibontásához.
