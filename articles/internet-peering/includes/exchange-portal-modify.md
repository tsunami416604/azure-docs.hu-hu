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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680935"
---
Ez a szakasz azt ismerteti, hogyan hajthatja végre a következő módosítási műveleteket a közvetlen társviszony-létesítéshez.

### <a name="add-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok hozzáadása

1. Válassza a **+ Kapcsolatok hozzáadása** gombot, és állítson be egy új társviszony-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-exchange-modify-addconnection.png)
1. Töltse ki az **Exchange társviszony-létesítési kapcsolat képernyőt,** és válassza a **Mentés gombot.** A társviszony-létesítési kapcsolatok konfigurálásával kapcsolatos segítségért tekintse át a "Közvetlen társviszony-létesítés létrehozása és kiépítése" című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![Exchange-társviszony-létesítési kapcsolat képernyő](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok eltávolítása

1. Jelölje ki a törölni kívánt társviszony-létesítési kapcsolatot, majd válassza **a ...**  >  **Kapcsolat törlése**.
    > [!div class="mx-imgBorder"]
    > ![Kapcsolat törlése gomb](../media/setup-exchange-modify-deleteconnection.png)
1. Írja be az erőforrás-azonosítót a **Törlés megerősítése** mezőbe, és válassza a **Törlés**gombot.
    > [!div class="mx-imgBorder"]
    > ![Megerősítés törlése](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4- vagy IPv6-munkamenet hozzáadása aktív kapcsolatokhoz

1. Jelölje ki a módosítani kívánt társviszony-létesítési kapcsolatot, majd válassza **a ...**  >  **Kapcsolat szerkesztése**.
    > [!div class="mx-imgBorder"]
    > ![Kapcsolat szerkesztése gomb](../media/setup-exchange-modify-editconnection.png)
1. Adja hozzá **az IPv4-címet** vagy az **IPv6-címadatokat,** és válassza a **Mentés gombot.**
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési kapcsolatok módosításai](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>IPv4- vagy IPv6-munkamenet eltávolítása aktív kapcsolatokon

Az IPv4- vagy IPv6-munkamenet eltávolítása meglévő kapcsolatból jelenleg nem támogatott a portálon. További információért forduljon a [Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).