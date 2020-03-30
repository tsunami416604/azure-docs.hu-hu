---
title: Bevezetés az IP-forgalom ellenőrzésébe az Azure Network Watcherben | Microsoft dokumentumok
description: Ez az oldal áttekintést nyújt a Hálózatfigyelő IP-áramlásának ellenőrzéséről
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: damendo
ms.openlocfilehash: 69aca5e0901a0da8aa98fe310ac220898bf650b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845006"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Bevezetés az IP-forgalom ellenőrzésébe az Azure Network Watcherben

Az IP-folyamat ellenőrzi, hogy egy csomag engedélyezett vagy megtagadott-e egy virtuális gépre vagy egy virtuális gépről. Az információ irányból, protokollból, helyi IP-címből, távoli IP-címből, helyi portból és távoli portból áll. Ha egy biztonsági csoport megtagadja a csomagot, a rendszer visszaadja a csomagot megtagadó szabály nevét. Bár bármely forrás- vagy célIP-cím választható, az IP-folyamat ellenőrzése segít a rendszergazdáknak gyorsan diagnosztizálni a kapcsolódási problémákat az internetről vagy az internetről, illetve a helyszíni környezetből vagy a helyszíni környezetbe.

Az IP-folyamat ellenőrzése a hálózati adapterre alkalmazott összes hálózati biztonsági csoportra (NSG) vonatkozó szabályokat vizsgálja, például egy alhálózatra vagy egy virtuálisgép-hálózati adapterre. A forgalom ezután a hálózati adapterre vagy a hálózati adapterről beállított beállítások alapján történik. Az IP-folyamat ellenőrzése akkor hasznos, ha egy hálózati biztonsági csoport egyik szabálya blokkolja a virtuális gépre irányuló vagy onnan érkező forgalmat, vagy kikapcsolódik onnan.

Létre kell hozni a Hálózati figyelő egy példányát minden olyan régióban, amelyen az IP-folyamat ellenőrzése fut. A Network Watcher egy regionális szolgáltatás, és csak ugyanabban a régióban lévő erőforrásokon futtatható. A használt példány nincs hatással az IP-folyamat ellenőrzésének eredményeire, mivel a hálózati adapterhez vagy az alhálózathoz társított bármely útvonal továbbra is vissza kerül.

![1][1]

## <a name="next-steps"></a>További lépések

Látogasson el a következő cikket, hogy megtudja, ha egy csomag engedélyezett vagy megtagadott egy adott virtuális gép a portálon keresztül. [Annak ellenőrzése, hogy engedélyezett-e a forgalom egy virtuális gépen az IP-folyamat ellenőrzése a portál használatával](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

