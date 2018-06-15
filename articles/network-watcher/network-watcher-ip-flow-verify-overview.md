---
title: Bevezetés az IP-adatfolyam győződjön meg az Azure hálózati figyelőt |} Microsoft Docs
description: Ezen a lapon áttekintést nyújt a hálózati figyelő IP-adatfolyam ellenőrzési funkciók
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: d352fb2d-4b4f-4ac4-9c2e-1cfccf0e7e03
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8a59047a586f3d7ad7c1f29b218396bd688caafd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181599"
---
# <a name="introduction-to-ip-flow-verify-in-azure-network-watcher"></a>Bevezetés az IP-adatfolyam győződjön meg az Azure hálózati figyelőt

Ha egy csomag engedélyezett vagy megtagadott vagy a virtuális gép IP-adatfolyam győződjön meg arról ellenőrzi. A tartalom darabjai irányát, protokoll, helyi IP, távoli IP, helyi port és távoli port. Ha a csomagot a rendszer megtagadja egy biztonsági csoport, a szabály, amely megtagadja a csomag nevét adja vissza. Minden cél- és IP-választható ki, miközben IP-adatfolyam ellenőrizze segítséget nyújt a rendszergazdák gyorsan eseményadatokat kapcsolat a vagy az internethez és a vagy a helyszíni környezetbe.

IP-adatfolyam ellenőrizze a hálózati adaptert egy virtuális gép célozza. Forgalom majd ellenőrizve a konfigurált beállításokat, vagy arról, hogy a hálózati illesztő alapján. IP-adatfolyam ellenőrizze akkor hasznosak, erősítse meg, ha egy szabály a hálózati biztonsági csoport blokkolja a belépési vagy kilépési bejövő és kimenő forgalmat a virtuális gép.

Ellenőrizze a hálózati figyelőt kell az IP-adatfolyam futtatni kívánt minden régióban hozható létre példánya. Hálózati figyelőt egy regionális szolgáltatás, és csak erőforrások ugyanabban a régióban lefutott is. A használt példány nincs hatással az eredmények IP-adatfolyam győződjön meg arról, az útvonal társított hálózati adapter még mindig van visszaadott formában.

![1][1]

## <a name="next-steps"></a>További lépések

Látogasson el a következő cikkben megtudhatja, ha egy csomag engedélyezett vagy megtagadott egy adott virtuális gép a portálon keresztül. [Ellenőrizze, hogy ha a forgalom engedélyezve van a virtuális gép IP Flow ellenőrzése és a portál használatával](diagnose-vm-network-traffic-filtering-problem.md)

[1]: ./media/network-watcher-ip-flow-verify-overview/figure1.png












