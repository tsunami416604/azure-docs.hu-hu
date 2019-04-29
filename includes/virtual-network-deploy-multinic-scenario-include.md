---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: rockboyfor
ms.service: virtual-network
ms.topic: include
origin.date: 04/13/2018
ms.date: 06/11/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743285"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum végigvezeti azon használó központi telepítések több hálózati adapter a virtuális gépek egy adott helyzetben. Ebben a forgatókönyvben egy kétrétegű iaas típusú számítási feladatainak Azure-ban üzemeltetett rendelkezik. Az egyes csomagok egy virtuális hálózatot (VNet) a saját alhálózatában van üzembe helyezve. Az előtérréteg több webkiszolgálók, állítsa be a magas rendelkezésre állás érdekében egy terheléselosztó csoportosítva tevődik össze. A háttér-szinten több adatbázis-kiszolgáló tevődik össze. Az adatbázis-kiszolgálók két hálózati adapterrel, egy az adatbázis-hozzáférés, a másik felügyeleti vannak telepítve. A forgatókönyv tartalmazza a központi telepítésben lévő hálózati biztonsági csoportok (NSG) szabályozhatja, hogy milyen forgalom engedélyezve van a minden egyes alhálózathoz és hálózati adapter is. Ebben a forgatókönyvben alapvető architektúráját mutatja be, a következő képen látható:

![MultiNIC forgatókönyv](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)