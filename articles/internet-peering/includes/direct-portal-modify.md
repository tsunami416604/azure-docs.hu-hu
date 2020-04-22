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
ms.openlocfilehash: 3894bf046ed4ee3f068e43dbc5bc5b7f2a1002b4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681078"
---
Ez a szakasz azt ismerteti, hogyan hajthatja végre a következő módosítási műveleteket a közvetlen társviszony-létesítéshez.

### <a name="add-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok hozzáadása
1. Válassza a **+ Kapcsolatok hozzáadása** gombot, és állítson be egy új társviszony-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-modify-addconnection.png)

1. Töltse ki a **Közvetlen társviszony-létesítési kapcsolat űrlapot,** és válassza a **Mentés gombot.** A társviszony-létesítési kapcsolatok konfigurálásával kapcsolatos segítségért tekintse át a "Közvetlen társviszony-létesítés létrehozása és kiépítése" című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![Közvetlen társviszony-létesítési kapcsolat képernyő](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok eltávolítása

A kapcsolat eltávolítása jelenleg nem támogatott az Azure Portalon. További információért forduljon a [Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszaminősítése aktív kapcsolatokon
1. Jelölje ki a módosítani kívánt társviszony-létesítési kapcsolatot, majd válassza **a ...**  >  **Kapcsolat szerkesztése**.
    > [!div class="mx-imgBorder"]
    > ![Kapcsolat szerkesztése](../media/setup-direct-modify-editconnection.png)

1. Módosítsa a sávszélességet a csúszka mozgatásával, majd válassza a **Mentés gombot.**
    > [!div class="mx-imgBorder"]
    > ![Sávszélesség módosítása](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>IPv4- vagy IPv6-munkamenet-információk hozzáadása az aktív kapcsolatokhoz
1. Jelölje ki a módosítani kívánt társviszony-létesítési kapcsolatot, majd válassza **a ...**  >  **A kapcsolat szerkesztése** az 1.
1. Írja be **a Munkamenet IPv4-előtagja** vagy **a Munkamenet IPv6-előtag** adatait, és válassza a **Mentés gombot.**

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Az IPv4- vagy IPv6-munkamenet adatainak eltávolítása aktív kapcsolatokról
A **munkamenet IPv4-előtagja** vagy **a Munkamenet IPv6-előtag** adatainak eltávolítása jelenleg nem támogatott a portálon. További információért forduljon a [Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).
