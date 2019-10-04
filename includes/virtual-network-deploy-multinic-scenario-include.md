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
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179045"
---
## <a name="scenario"></a>Forgatókönyv
Ez a dokumentum végigvezeti azon használó központi telepítések több hálózati adapter a virtuális gépek egy adott helyzetben. Ebben a forgatókönyvben egy kétrétegű iaas típusú számítási feladatainak Azure-ban üzemeltetett rendelkezik. Az egyes csomagok egy virtuális hálózatot (VNet) a saját alhálózatában van üzembe helyezve. Az előtérréteg több webkiszolgálók, állítsa be a magas rendelkezésre állás érdekében egy terheléselosztó csoportosítva tevődik össze. A háttér-szinten több adatbázis-kiszolgáló tevődik össze. Az adatbázis-kiszolgálók két hálózati adapterrel, egy az adatbázis-hozzáférés, a másik felügyeleti vannak telepítve. A forgatókönyv tartalmazza a központi telepítésben lévő hálózati biztonsági csoportok (NSG) szabályozhatja, hogy milyen forgalom engedélyezve van a minden egyes alhálózathoz és hálózati adapter is. Ebben a forgatókönyvben alapvető architektúráját mutatja be, a következő képen látható:

![MultiNIC forgatókönyv](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

