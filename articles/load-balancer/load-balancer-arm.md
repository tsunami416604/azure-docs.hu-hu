---
title: Terheléselosztó támogatása az Azure Resource Manager |} Microsoft Docs
description: Az Azure Resource Manager terheléselosztóhoz powershell használatával. Terheléselosztó sablonok használata
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d1d098f45c391363523b6a56ec0c6fdb40e64d68
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Az Azure erőforrás-kezelő támogatási használata az Azure terheléselosztó



Az Azure Resource Manager az előnyben részesített felügyeleti keretet biztosít az Azure-szolgáltatások. Az Azure Load Balancer Azure Resource Manager-alapú API-k és eszközök használatával kezelhetők.

## <a name="concepts"></a>Alapelvek

A Resource Manager Azure terheléselosztó a következő gyermekerőforrásait tartalmazza:

* Terheléselosztó előtér-IP-konfiguráció – tartalmazhat egy vagy több előtérbeli IP-címek, más néven a virtuális IP-címek (VIP). Ezek az IP-címek szolgálnak a forgalom bemeneti pontjaként.
* Háttér-címkészlet – ezek azok az a virtuális gép hálózati kártya (NIC), amelyhez a terhelés elosztása társított IP-címeket.
* Terheléselosztási szabályok – a szabály tulajdonság leképezi a megadott előtér-IP- és port kombinációján a háttérbeli IP-címek egy készletének – port kombináció. Egyetlen terheléselosztó több terheléselosztási szabállyal is rendelkezhet. Minden egyes szabály egy előtér-IP- és a port és a háttér IP-címet és a virtuális gépek társított port.
* Mintavételt – a mintavételt lehetővé teszik a Virtuálisgép-példányok állapotának nyomon követéséhez. Ha egy állapotmintáihoz nem sikerül, a Virtuálisgép-példány szükséges kívül Elforgatás automatikusan.
* Bejövő NAT-szabályok – meghatározása a bejövő forgalom haladnak keresztül az előtér-IP- és terjesztése a háttér IP-címet a NAT-szabályok.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Gyorsindítási sablonok

Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.

Sablonok tartalmazhatnak a virtuális gépek, virtuális hálózatok, rendelkezésre állási csoportok, hálózati adapterek (NIC), Storage-fiókok, Terheléselosztók, hálózati biztonsági csoportok és nyilvános IP-címek definícióit. Sablonok hozhat létre mindent megtalál az összetett alkalmazások. A sablonfájl ellenőrizhetők a verziókövetés és együttműködés tartalomkezelési rendszerbe.

[További információ a sablonok](../azure-resource-manager/resource-manager-template-walkthrough.md)

[További információ a hálózati erőforrások](../virtual-network/resource-groups-networking.md)

Gyors üzembe helyezés sablonok használata az Azure Load Balancer, tekintse meg a [GitHub-tárházban](https://github.com/Azure/azure-quickstart-templates) üzemeltető közösségi létrehozott sablonok készlete.

Sablonok példái:

* [2 virtuális gép egy terheléselosztó és terheléselosztási szabályok](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuális gép egy belső terheléselosztó és a terheléselosztó szabályait a VNETEN belül](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuális gép egy terheléselosztó és a Terheléselosztó NAT-szabályok konfigurálása](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>A PowerShell vagy a CLI Azure Load Balancer beállítása

Ismerkedés az Azure Resource Manager parancsmagok, a parancssori eszköz és a REST API-k

* [Azure-hálózat parancsmagokat](https://msdn.microsoft.com/library/azure/mt163510.aspx) hozzon létre egy terheléselosztó használható.
* [Azure Resource Manager használatával terheléselosztó létrehozása](load-balancer-get-started-ilb-arm-ps.md)
* [Az Azure erőforrás-kezelés az Azure parancssori felület használatával](../xplat-cli-azure-resource-manager.md)
* [Terheléselosztó REST API-k](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>További lépések

Is [Internet felé néző terheléselosztó létrehozásához](load-balancer-get-started-internet-arm-ps.md) és konfigurálása, hogy milyen típusú [mód](load-balancer-distribution-mode.md) egy meghatározott típusú terheléselosztó hálózati forgalom viselkedését.

Megtudhatja, hogyan kezelheti [üresjárati TCP időtúllépési beállítások terheléselosztó](load-balancer-tcp-idle-timeout.md). Ez azért fontos, ha az alkalmazás kell életben tartása kapcsolatok kiszolgálók egy terheléselosztó mögött.
