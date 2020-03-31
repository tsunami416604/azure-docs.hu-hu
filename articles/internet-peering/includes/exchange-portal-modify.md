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
ms.openlocfilehash: e3e87e59f5b3c95051d9ee53e4b8d87afe9d9ba8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774561"
---
Ez a szakasz azt ismerteti, hogy miként hajthatja végre a következő módosítási műveleteket a közvetlen társviszony-létesítéshez:

### <a name="add-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok hozzáadása

1. Kattintson a + **Kapcsolatok hozzáadása** gombra a tetején, és állítson be egy új társviszony-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-exchange-modify-addconnection.png)
1. Töltse ki az **Exchange társviszony-létesítési kapcsolat képernyőt,** és kattintson a **Mentés gombra.** A társviszony-létesítési kapcsolat konfigurálásával kapcsolatos segítségért tekintse át a fenti" Közvetlen társviszony-létesítés című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési erőforrás nézete](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-társviszony-létesítési kapcsolatok eltávolítása

1. Kattintson a társviszony-létesítési kapcsolatot törölni kívánt, majd kattintson a **...**  >  **Kapcsolat törlése** gomb.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési kapcsolat törlése](../media/setup-exchange-modify-deleteconnection.png)
1. Írja be az erőforrás-azonosítót a **Törlés megerősítése** mezőbe a kiemelt mezőkben látható módon, majd kattintson a **Törlés gombra.**
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési kapcsolat törlésemegerősítés](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása aktív kapcsolatokhoz

1. Kattintson a társviszony-létesítési kívánt módosítani, majd kattintson a **...**  >  **Kapcsolat szerkesztése** gomb.
    > [!div class="mx-imgBorder"]
    > ![Társviszony-létesítési kapcsolat szerkesztése](../media/setup-exchange-modify-editconnection.png)
1. Adja hozzá **az IPv4-címet** vagy **az IPv6-címadatait,** és kattintson a **Mentés gombra.**
    > [!div class="mx-imgBorder"]
    > ![Társviszony módosítása](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4-munkamenet eltávolítása aktív kapcsolatokon

A portálon jelenleg nem támogatott az IPv4/IPv6-munkamenet eltávolítása egy meglévő kapcsolatból. Lépjen kapcsolatba [a Microsoft társviszony-létesítési](mailto:peeringexperience@microsoft.com).