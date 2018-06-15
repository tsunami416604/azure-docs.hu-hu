---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805104"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum végigvezeti a központi telepítésre használja több hálózati adaptert a virtuális gépek egy adott forgatókönyvhöz. Ebben a forgatókönyvben a kétféle IaaS munkaterhelések Azure-ban üzemeltetett rendelkezik. Minden egyes réteg a virtuális hálózatot (VNet) a saját alhálózat lett telepítve. Az előtér-réteg több webkiszolgálók csoportosítva beállítása a magas rendelkezésre állású terheléselosztó tevődik össze. A háttér-réteg több adatbázis-kiszolgálók tevődik össze. Az adatbázis-kiszolgáló két hálózati adapterrel rendelkező minden, egy adatbázis-hozzáférési, a másik felügyeleti lett telepítve. Az az eset tartalmazza a központi telepítésben lévő hálózati biztonsági csoportokkal (NSG-k) szabályozásához, hogy milyen forgalom engedélyezve van minden egyes alhálózathoz, és a hálózati adapter is. Az alábbi ábra az ebben a forgatókönyvben alapvető architektúráját mutatja be:

![MultiNIC forgatókönyv](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

