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
ms.openlocfilehash: d9ff01cf0180dae7f75d9753ba889d0caddad937
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775341"
---
Ez a szakasz azt ismerteti, hogy miként hajthatja végre a következő módosítási műveleteket a közvetlen társviszony-létesítéshez:

### <a name="add-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok hozzáadása
1. Kattintson a + **Kapcsolatok hozzáadása** gombra a tetején, és állítson be egy új társviszony-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-modify-addconnection.png)
1. Töltse ki a **Közvetlen társviszony-létesítési kapcsolat képernyőt,** és kattintson a **Mentés gombra.** A társviszony-létesítési kapcsolat konfigurálásával kapcsolatos segítségért tekintse át a fenti" Közvetlen társviszony-létesítés című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Közvetlen társviszony-létesítési kapcsolatok eltávolítása

A kapcsolat eltávolítása jelenleg nem támogatott a portálon. Lépjen kapcsolatba [a Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszaminősítése aktív kapcsolatokon
1. Kattintson a társviszony-létesítési kívánt módosítani, majd kattintson a **...**  >  **Kapcsolat szerkesztése** gomb.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési kapcsolat szerkesztése](../media/setup-direct-modify-editconnection.png)
1. Módosítsa a sávszélességet az alábbi módon, majd kattintson a **Mentés gombra.**
    > [!div class="mx-imgBorder"]
    > ![Társviszony-módosítási sávszélesség](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása aktív kapcsolatokhoz.
1. Kattintson a társviszony-létesítési kívánt módosítani, majd kattintson a **...**  >  **A kapcsolat szerkesztése** gomb a fent látható módon.
1. Adja hozzá **a Munkamenet IPv4-előtagja** vagy **a Munkamenet IPv6-előtag** adatait, és kattintson a **Mentés gombra.**

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Távolítsa el az IPv4/IPv6 munkamenetet az aktív kapcsolatokból.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
