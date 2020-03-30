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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75775081"
---
1. Nyissa meg **az Erőforráscsoportok lehetőséget,** és kattintson a **társviszonylétesítési** erőforrás létrehozásakor kiválasztott erőforráscsoportra. A Szűrő mezőt akkor *használhatja,* ha túl sok erőforráscsoport van.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforráscsoport](../media/setup-direct-get-resourcegroup.png)

1. Kattintson a létrehozott **Társviszony-létesítési** erőforrásra.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-get-open.png)

1. Az **Áttekintés** lap magas szintű információkat jelenít meg. Figyelje meg az alábbi információkat.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-get-overview.png)

1. A bal oldalon kattintson az **ASN-információkra** a PeerAsn létrehozása során elküldött információk megtekintéséhez

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-get-asninfo.png)

1. A bal oldalon kattintson a **Kapcsolatok gombra.** Figyelje meg a tetején egy összefoglalót a társviszony-létesítési kapcsolatok között az ASN és a Microsoft, a különböző létesítmények a metrón belül. A kapcsolatok összegzését az **Áttekintés** oldalról is megkaphatja, ha a fent kiemelt módon a középső ablaktáblán a **Kapcsolatok** elemre kattint.

    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-get-connectionssummary.png)

    * **A kapcsolat állapota** megfelel a létesítési kapcsolat beállításának. Az ebben a mezőben megjelenő állapotok a [Közvetlen társviszony-létesítés forgatókönyvében látható állapotábrát követik.](../walkthrough-direct-all.md)
    * **Az IPv4-munkamenet-állapot** és az **IPv6-munkamenet-állapot** megfelel az IPv4- és Az IPv6 BGP-munkamenet állapotának.  
    * Amikor kijelöl egy sort a tetején, az alsó ***kapcsolat*** szakasz az egyes kapcsolatok részleteit jeleníti meg. A nyíljelekre kattintva kibonthatja a ***Konfiguráció,*** Az ***IPv4-cím*** és az ***IPv6-cím alszakaszait.***
