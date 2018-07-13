---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38941715"
---
Nyisson meg egy portot, vagy hozzon létre egy végpontot a virtuális géphez (VM) az Azure-ban egy alhálózatot vagy virtuális hálózati adapter hálózati szűrő létrehozásával. Ezeket a szűrőket, amely a bejövő és kimenő adatforgalom vezérlésére, helyezze el az erőforrás a forgalmat fogadó csatolt hálózati biztonsági csoport.

Használjuk a webes forgalom ilyenek például a 80-as porton. Miután egy virtuális gép kiszolgálására konfigurált webes kéréseket a szabványos TCP protokollt használja port 80-as (ne felejtse el indítsa el a megfelelő szolgáltatásokat, és nyissa meg a virtuális gépen, valamint bármely operációs rendszer tűzfalszabályok), akkor:

1. Hozzon létre egy hálózati biztonsági csoportot.
2. Hozzon létre egy bejövő szabályt átengedi a forgalmat:
   * a "80" Célporttartomány
   * a forrásport-tartományt a "*" (bármely forrásportból engedélyezése)
   * prioritás értéke kisebb 65 500 (hogy magasabb prioritású, mint az alapértelmezett catch-all a rendszer megtagadja a bejövő szabály)
3. A hálózati biztonsági csoport társítása a virtuális hálózati adapter vagy alhálózatot.

Biztosítsa a környezetét a hálózati biztonsági csoportok és a szabályok egy összetett hálózati konfigurációt is létrehozhat. A példában csak egy vagy két olyan szabályokat, amelyek engedélyezik a HTTP-forgalom vagy a távoli felügyeleti. További információkért tekintse meg a következő ["További információ"](#more-information-on-network-security-groups) szakasz vagy [Mi az a hálózati biztonsági csoport?](../articles/virtual-network/security-overview.md)

