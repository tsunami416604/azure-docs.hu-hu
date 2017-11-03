---
title: "Következő Ugrás az Azure hálózati figyelő következő ugrás - Azure-portálon található |} Microsoft Docs"
description: "Ez a cikk leírja, hogyan megtalálhatja a következő ugrás típusa van, és IP-cím használatával a következő Ugrás az Azure portál használatával"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: jimdial
editor: 
ms.assetid: 7b459dcf-4077-424e-a774-f7bfa34c5975
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 445ec8c7eeb8dd715d3778b44372d16666da7fb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-azure-network-watcher-using-the-portal"></a>Megtudhatja, milyen a következő ugrás típusa a következő ugrás funkció használ az Azure hálózati figyelőt a portál használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [Az Azure REST API-n](network-watcher-check-next-hop-rest.md)

Következő ugrás csak a hálózati figyelő, amely a képességét get biztosít, a következő ugrás típusa és az IP-cím a megadott virtuális gép alapján. Ez a szolgáltatás akkor hasznos, meghatározni, hogy ha egy virtuális gép elhagyó forgalomra halad át egy átjárót, az interneten vagy a virtuális hálózatok a cél eléréséhez.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) létrehozása egy hálózati figyelőt. A forgatókönyv feltételezi, hogy létezik-e egy erőforráscsoportot, egy érvényes virtuális géppel használandó.

## <a name="scenario"></a>Forgatókönyv

A forgatókönyv, a cikkben szereplő következő ugrás a következő ugrás típusa és az IP-cím erőforrás használja. Következő ugrás kapcsolatos további információkért látogasson el a [következő ugrásaként áttekintése](network-watcher-next-hop-overview.md).

Ebben a forgatókönyvben a tartalma:

* A következő ugrás beolvasni a virtuális gépről.

## <a name="get-next-hop"></a>Következő ugrás beolvasása

### <a name="step-1"></a>1. lépés

Nyissa meg a hálózati figyelőt erőforrás az Azure portálon.

### <a name="step-2"></a>2. lépés

Kattintson a **a következő Ugrás** a navigációs ablaktáblán válassza ki a virtuális gép és a hálózati illesztő, töltse ki a forrás és cél IP, és kattintson a **a következő Ugrás** gombra.

> [!NOTE]
> Következő ugrás megköveteli, hogy a virtuális gép erőforrásához lefoglalt futtatásához.

![következő ugrás áttekintése beolvasása][1]

### <a name="step-3"></a>3. lépés

Ha a feladat befejeződött, a eredményei. Az IP-cím és az eszköznek a következő ugrás van, megjelenik. A következő táblázat a rendelkezésre álló visszaadott értékeket a portálon.

**Következő ugrás típusa**

* Internet
* VirtualAppliance
* Pedig
* VnetLocal
* HyperNetGateway
* VnetPeering
* None

Ha egy egyéni útvonala irányíthatja a forgalmat lett megadva, a felhasználó által megadott útvonal (UDR) jelenik meg, valamint az eredményeket.

![következő ugrás eredményt ad][2]

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan ellátogatva programozott módon tekintse át a hálózati biztonsági csoport beállításainak [NSG naplózás hálózati figyelőt](network-watcher-nsg-auditing-powershell.md)

[1]: ./media/network-watcher-check-next-hop-portal/figure1.png
[2]: ./media/network-watcher-check-next-hop-portal/figure2.png














