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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774561"
---
Ez a szakasz azt ismerteti, hogyan hajtható végre a következő módosítási műveletek a közvetlen társításhoz:

### <a name="add-exchange-peering-connections"></a>Exchange-partneri kapcsolatok hozzáadása

1. Kattintson a felül található **+ kapcsolatok hozzáadása** gombra, és konfigurálja az új társ-létesítési kapcsolatot.
    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-exchange-modify-addconnection.png)
1. Töltse ki az **Exchange-társi kapcsolatok** űrlapot, és kattintson a **Mentés**gombra. A társítási kapcsolatok konfigurálásával kapcsolatos segítségért tekintse meg a fenti "közvetlen társítás létrehozása és kiépítése" című szakasz lépéseit.
    > [!div class="mx-imgBorder"]
    > ![társ-erőforrás nézet](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-társi kapcsolatok eltávolítása

1. Kattintson a törölni kívánt társi kapcsolatok elemre, majd kattintson a **...**  > a **Kapcsolódás törlése** gombra.
    > [!div class="mx-imgBorder"]
    > ![társ-létesítési kapcsolatok törlése](../media/setup-exchange-modify-deleteconnection.png)
1. Adja meg az erőforrás-azonosítót a **Törlés megerősítése** mezőbe a Kiemelt mezőkben látható módon, majd kattintson a **Törlés**gombra.
    > [!div class="mx-imgBorder"]
    > ![társ-létesítési DeleteConfirm](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet hozzáadása az aktív kapcsolatokhoz

1. Kattintson a módosítani kívánt társi kapcsolatok elemre, majd kattintson a **...**  > a **Kapcsolódás szerkesztése** gombra.
    > [!div class="mx-imgBorder"]
    > ![társ-létrehozási kapcsolatok szerkesztése](../media/setup-exchange-modify-editconnection.png)
1. Adja hozzá az **IPv4-cím** vagy az **IPv6-cím** adatait, majd kattintson a **Mentés**gombra.
    > [!div class="mx-imgBorder"]
    > ![társ-létrehozási kapcsolatok módosítása](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>IPv4/IPv6-munkamenet eltávolítása az aktív kapcsolatokon

Egy IPv4/IPv6-munkamenet meglévő kapcsolatból való eltávolítása jelenleg nem támogatott a portálon. Forduljon a [Microsoft-partnerekhez](mailto:peeringexperience@microsoft.com).