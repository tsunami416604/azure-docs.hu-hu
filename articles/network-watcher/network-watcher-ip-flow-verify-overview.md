---
title: Bevezetés az IP-folyamat ellenőrzése az Azure Network Watcher |} A Microsoft Docs
description: Ez az oldal áttekintést nyújt a Network Watcher IP-cím-folyamat ellenőrzésével képesség
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: kumud
ms.openlocfilehash: 5c34fd2b6d354f594ed153647c1bed700566fad6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64709592"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Bevezetés az IP-folyamat ellenőrzése az Azure Network Watcher

IP-folyamat ellenőrzésével ellenőrzéseket, ha egy csomag engedélyezett vagy tiltott, vagy a virtuális gépről. Az információk iránya, protokoll, helyi IP-cím, távoli IP, helyi port és a távoli port áll. A csomagot a rendszer megtagadja egy biztonsági csoport is, ha a csomagot letiltó szabály nevét adja vissza. Minden forrás vagy cél IP-választható ki, míg az IP-folyamat ellenőrzésével segít a rendszergazdák gyorsan diagnosztizálhatja a kapcsolódási problémák, vagy az internethez és a vagy a helyszíni környezetben.

IP-folyamat ellenőrzésével megállapítja, hogy minden hálózati biztonsági csoportok (NSG) szabályai alkalmazva a hálózati adapterhez, például egy alhálózatot vagy a virtuális gép hálózati adapteren. Forgalom ellenőrzése majd, illetve a hálózati illesztőt a konfigurált beállítások alapján. IP-folyamat ellenőrzésével hasznos megerősítve, ha egy szabályt a hálózati biztonsági csoport blokkolja a bejövő és kimenő forgalmat, vagy a virtuális gépről.

Ellenőrizze a Network Watcher kell IP-folyamat futtatni kívánt összes régiókban lehet létrehozni egy példányát. A Network Watcher egy regionális szolgáltatás, és csak az erőforrásokon ugyanabban a régióban kell futott. A használt példány nincs hatással az eredményeket az IP-folyamat ellenőrzése, a hálózati adapter vagy alhálózat társított útvonalakat továbbra is a visszaadott.

![1][1]

## <a name="next-steps"></a>További lépések

Látogasson el az alábbi cikkben Ismerje meg, ha egy csomag engedélyezett vagy megtagadott egy adott virtuális gép a portálon keresztül. [Ellenőrizze, hogy ha a forgalom engedélyezve van a IP Flow ellenőrizze a virtuális gép a portál használatával](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png

