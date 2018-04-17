---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 5fc9412713bc57c3fd7753e133b2587ea7a68938
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum végigvezeti a központi telepítésre használja több hálózati adaptert a virtuális gépek egy adott forgatókönyvhöz. Ebben a forgatókönyvben a kétféle IaaS munkaterhelések Azure-ban üzemeltetett rendelkezik. Minden egyes réteg a virtuális hálózatot (VNet) a saját alhálózat lett telepítve. Az előtér-réteg több webkiszolgálók csoportosítva beállítása a magas rendelkezésre állású terheléselosztó tevődik össze. A háttér-réteg több adatbázis-kiszolgálók tevődik össze. Az adatbázis-kiszolgáló két hálózati adapterrel rendelkező minden, egy adatbázis-hozzáférési, a másik felügyeleti lett telepítve. Az az eset tartalmazza a központi telepítésben lévő hálózati biztonsági csoportokkal (NSG-k) szabályozásához, hogy milyen forgalom engedélyezve van minden egyes alhálózathoz, és a hálózati adapter is. Az alábbi ábra az ebben a forgatókönyvben alapvető architektúráját mutatja be:  

![MultiNIC forgatókönyv](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

