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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775341"
---
Ez a szakasz azt ismerteti, hogyan hajtható végre a következő módosítási műveletek a közvetlen társításhoz:

### <a name="add-direct-peering-connections"></a>Közvetlen társas kapcsolatok hozzáadása
1. Kattintson a felül található **+ kapcsolatok hozzáadása** gombra, és konfigurálja az új társ-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-modify-addconnection.png)
1. Töltse ki a **közvetlen társas kapcsolatok** űrlapot, és kattintson a **Mentés**gombra. A társítási kapcsolatok konfigurálásával kapcsolatos segítségért tekintse meg a fenti "közvetlen társítás létrehozása és kiépítése" című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Közvetlen társas kapcsolatok eltávolítása

A kapcsolatok eltávolítása jelenleg nem támogatott a portálon. Forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>A sávszélesség frissítése vagy visszalépése aktív kapcsolatokon
1. Kattintson a módosítani kívánt társi kapcsolatok elemre, majd kattintson a **...**  > a **Kapcsolódás szerkesztése** gombra.
    > [!div class="mx-imgBorder"]
    > ![társ-létrehozási kapcsolatok szerkesztése](../media/setup-direct-modify-editconnection.png)
1. Módosítsa a sávszélességet az alább látható módon, majd kattintson a **Mentés**gombra.
    > [!div class="mx-imgBorder"]
    > ![a kapcsolat sávszélességének módosítása](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz.
1. Kattintson a módosítani kívánt társi kapcsolatok elemre, majd kattintson a **...**  > a fent látható **Kapcsolatok szerkesztése** gombra.
1. Adja hozzá a **munkamenet IPv4-előtagját** vagy a **munkamenet IPv6-előtagjának** adatait, majd kattintson a **Mentés**gombra.

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet eltávolítása az aktív kapcsolatokon.
    Removing a **Session IPv4 prefix** or **Session IPv6 prefix** info is not currently supported on portal. Contact [Microsoft peering](mailto:peeringexperience@microsoft.com).
