---
title: "Ellenőrizze a forgalom Azure hálózati figyelő IP-adatfolyam ellenőrizze - Azure-portál |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan ellenőrizhető, ha a bejövő és kimenő forgalmat a virtuális gépek engedélyezett vagy megtagadott"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 8333ccfd1e4cc917c8af4b3006292e43b77ecc7f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Ha a forgalom engedélyezett vagy megtagadott vagy a virtuális gép IP Flow ellenőrizze és Azure hálózati figyelőt összetevője ellenőrzése

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Az Azure REST API-n](network-watcher-check-ip-flow-verify-rest.md)


IP-adatfolyam ellenőrizze, hogy egy funkciója, amely lehetővé teszi, hogy ellenőrizze, hogy ha a forgalom engedélyezve van-e, vagy a virtuális gép hálózati figyelőt. Az érvényesítési futtathatja a bejövő vagy kimenő forgalmat. Ebben a forgatókönyvben akkor hasznos, ha szeretné, hogy a virtuális gép kommunikálhat külső erőforrásokra vagy egy másik erőforrás aktuális állapotának. IP-adatfolyam győződjön meg arról is használható, ha a hálózati biztonsági csoport (NSG) szabályok konfigurációja megfelelő-e, és hibáinak elhárítása az NSG-szabályok által blokkolt adatfolyamok ellenőrzése. Egy másik oka IP folyamat, ellenőrizze annak biztosítása, amelyet a letiltott forgalmat blokkol megfelelően az NSG.

## <a name="before-you-begin"></a>Előkészületek

Ez a forgatókönyv azt feltételezi, hogy már követte lépéseit [hozzon létre egy hálózati figyelőt](network-watcher-create.md) hozzon létre egy hálózati figyelőt, vagy egy meglévő példánya hálózati figyelőt. A forgatókönyv feltételezi, hogy létezik-e egy erőforráscsoportot, egy érvényes virtuális géppel használandó.

## <a name="scenario"></a>Forgatókönyv

Ez a forgatókönyv használ IP Flow ellenőrizze ellenőrizheti, ha egy virtuális gép működik egy másik 443-as porton keresztül. Ha a forgalmat a rendszer megtagadja, a biztonsági szabály, amely megtagadja a forgalom adja vissza. IP Flow ellenőrizze kapcsolatos további információkért látogasson el a [IP-adatfolyam ellenőrizze áttekintése](network-watcher-ip-flow-verify-overview.md)

### <a name="run-ip-flow-verify"></a>Futtatási IP-adatfolyam ellenőrzése

Nyissa meg a hálózati figyelőt, és kattintson a **IP folyamata ellenőrizze**. Válassza ki a virtuális gép és a hálózati illesztő szeretné ellenőrizni a forgalmat. Adja meg a szűrési további adatokat, és kattintson a **ellenőrizze**.

Miután rákattintott **ellenőrizze**, a folyamat a megadott feltételeknek megfelelő jelölőnégyzet be van jelölve. Az eredmény az **engedélyezett hozzáférési** vagy **hozzáférés megtagadva**. Ha megtagadja a hozzáférést, a hálózati biztonsági csoport (NSG) és a biztonsági szabály, amely a forgalom blokkolása valósul meg. Ha a forgalom szolgáltatásmegtagadást normális működés, majd a szabály sikeres volt.

> [!NOTE]
> IP-adatfolyam ellenőrizze, hogy megköveteli, hogy a virtuális gép erőforrásához van lefoglalva.

Mint az alábbi képen látható, a kimenő HTTPS-forgalom engedélyezve volt.

![IP-adatfolyam ellenőrzése – áttekintés][1]

Az alábbi képen látható, forgalom megváltozott-e a bejövő és a bejövő portot 123 értékre módosult. Forgalom most megtagadva az együtt a hálózati biztonsági csoport és a biztonsági szabályt, amely a forgalom megtagadása biztosított "Hozzáférés megtagadva" üzenet.

![IP-adatfolyam eredmények][2]

## <a name="next-steps"></a>További lépések

Ha a forgalmat blokkol, és nem kell, lásd: [hálózati biztonsági csoportok kezelése](../virtual-network/virtual-network-manage-nsg-arm-portal.md) nyomon követheti a hálózati biztonsági csoport és a biztonsági meghatározott szabályokat.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png













