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
ms.openlocfilehash: b967c844ab145074490e931122cbe092d67de0c1
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774548"
---
Ha a társítási erőforrás üzembe helyezése sikeresen **megtörtént,** az alábbi lépésekkel tekintheti meg.

1. Lépjen az **erőforráscsoportok** elemre, és kattintson a kiválasztott erőforráscsoporthoz az **egyenrangú** erőforrás létrehozásakor. Ha túl sok erőforráscsoport van, akkor használhatja a *szűrő* mezőt.

    > [!div class="mx-imgBorder"]
    > ![egyenrangú erőforráscsoport](../media/setup-direct-get-resourcegroup.png)

1. Kattintson a létrehozott **egyenrangú** erőforrásra.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-get-open.png)

1. Az **Áttekintés** oldalon a magas szintű információk láthatók. Figyelje meg az alább látható adatokat.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-exchange-get-overview.png)

1. A bal oldalon kattintson az **ASN-adatok** elemre az PeerAsn létrehozásakor elküldött információk megtekintéséhez

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-get-asninfo.png)

1. A bal oldalon kattintson a **kapcsolatok**elemre. Figyelje meg az ASN és a Microsoft közötti, a metrón belüli különböző létesítmények közötti kapcsolatok összefoglalását. A kapcsolatok összegzése az **Áttekintés** lapról is megjelenhet, ha a középső ablaktáblán látható **kapcsolatok** elemre kattint a fent kiemelt módon.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-exchange-get-connectionssummary.png)

    * A **kapcsolódási állapot** megegyezik a beállított társ-összekapcsolási állapottal. Az ebben a mezőben megjelenített állapotok az Exchange-társítási [útmutatóban](../walkthrough-exchange-all.md) látható állapotjelző diagramot követik
    * Az **IPv4-munkamenet állapota** és az **IPv6-munkamenet állapota** az IPv4-és az IPv6-alapú BGP-munkamenet-állapotoknak felel meg.  
    * Amikor kijelöl egy sort a tetején, a lenti ***kapcsolat*** szakasz az egyes kapcsolatok részleteit jeleníti meg. A nyílgombokra kattintva kibonthatja az alszakaszok ***konfigurációját***, az ***IPv4-cím*** és az ***IPv6-cím*** elemet.

    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-exchange-get-connectionsipv4.png)
