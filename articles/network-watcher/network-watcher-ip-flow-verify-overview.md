---
title: "Bevezetés az IP-adatfolyam győződjön meg az Azure hálózati figyelőt |} Microsoft Docs"
description: "Ezen a lapon áttekintést nyújt a hálózati figyelő IP-adatfolyam ellenőrzési funkciók"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b2ad45e76320c59d18dce7b39166679801b4170a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Bevezetés az IP-adatfolyam győződjön meg az Azure hálózati figyelőt

IP-adatfolyam ellenőrizze ellenőrzések, ha egy csomag engedélyezett vagy megtagadott vagy a virtuális gép 5 rekordos adatok alapján. Ez az információ irányát, protokoll, helyi IP, távoli IP, helyi port és távoli port áll. Ha a csomagot a rendszer megtagadja egy biztonsági csoport, a szabály, amely megtagadja a csomag nevét adja vissza. Minden cél- és IP-választható ki, miközben a Ez a szolgáltatás segítségével a rendszergazdák gyorsan eseményadatokat kapcsolat a vagy az internethez és a vagy a helyszíni környezetben.

IP-adatfolyam ellenőrizze a hálózati adaptert egy virtuális gép célozza. Forgalom majd ellenőrizve a konfigurált beállításokat, vagy arról, hogy a hálózati illesztő alapján. Ezzel a képességgel olyan hasznos, erősítse meg, ha egy szabály a hálózati biztonsági csoport blokkolja a belépési vagy kilépési bejövő és kimenő forgalmat a virtuális gép.

Ellenőrizze a hálózati figyelőt kell az IP-adatfolyam futtatni kívánt minden régióban hozható létre példánya. Hálózati figyelőt egy regionális szolgáltatás, és csak erőforrások ugyanabban a régióban lefutott is. Ez nincs hatással IP folyamat eredményeinek ellenőrzése továbbra is visszaadott az útvonal társított hálózati adapter.

![1][1]

## <a name="next-steps"></a>Következő lépések

Látogasson el a következő cikkben megtudhatja, ha egy csomag engedélyezett vagy megtagadott egy adott virtuális gép a portálon keresztül. [Ellenőrizze, hogy ha a forgalom engedélyezve van a virtuális gép IP Flow ellenőrzése és a portál használatával](network-watcher-check-ip-flow-verify-portal.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












