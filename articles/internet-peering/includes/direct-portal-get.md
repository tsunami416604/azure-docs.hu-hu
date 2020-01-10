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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775081"
---
1. Lépjen az **erőforráscsoportok** elemre, és kattintson a kiválasztott erőforráscsoporthoz az **egyenrangú** erőforrás létrehozásakor. Ha túl sok erőforráscsoport van, akkor használhatja a *szűrő* mezőt.

    > [!div class="mx-imgBorder"]
    > ![egyenrangú erőforráscsoport](../media/setup-direct-get-resourcegroup.png)

1. Kattintson a létrehozott **egyenrangú** erőforrásra.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-get-open.png)

1. Az **Áttekintés** oldalon a magas szintű információk láthatók. Figyelje meg az alább látható adatokat.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-get-overview.png)

1. A bal oldalon kattintson az **ASN-adatok** elemre az PeerAsn létrehozásakor elküldött információk megtekintéséhez

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-get-asninfo.png)

1. A bal oldalon kattintson a **kapcsolatok**elemre. Figyelje meg az ASN és a Microsoft közötti, a metrón belüli különböző létesítmények közötti kapcsolatok összefoglalását. A kapcsolatok összegzése az **Áttekintés** lapról is megjelenhet, ha a középső ablaktáblán látható **kapcsolatok** elemre kattint a fent kiemelt módon.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-get-connectionssummary.png)

    * A **kapcsolódási állapot** megegyezik a beállított társ-összekapcsolási állapottal. Az ebben a mezőben megjelenített állapotok a közvetlen társítási [útmutatóban](../walkthrough-direct-all.md) látható állapotjelző diagramot követik.
    * Az **IPv4-munkamenet állapota** és az **IPv6-munkamenet állapota** az IPv4-és az IPv6-alapú BGP-munkamenet-állapotoknak felel meg.  
    * Amikor kijelöl egy sort a tetején, a lenti ***kapcsolat*** szakasz az egyes kapcsolatok részleteit jeleníti meg. A nyílgombokra kattintva kibonthatja az alszakaszok ***konfigurációját***, az ***IPv4-cím*** és az ***IPv6-cím*** elemet.
