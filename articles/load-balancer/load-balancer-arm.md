---
title: Load Balancer Azure Resource Manager támogatása | Microsoft Docs
description: A PowerShell használata a Load Balancerhoz a Azure Resource Manager használatával. A Load Balancer sablonjainak használata
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 839b607b7787d51151401737848a46d7b66229dd
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275489"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager támogatás használata a Azure Load Balancer



Azure Resource Manager az Azure-szolgáltatások előnyben részesített felügyeleti keretrendszere. A Azure Load Balancer Azure Resource Manager-alapú API-k és eszközök használatával kezelhetők.

## <a name="concepts"></a>Alapelvek

A Resource Managerrel Azure Load Balancer a következő alárendelt erőforrásokat tartalmazza:

* Előtér-IP-konfiguráció – a terheléselosztó egy vagy több előtérbeli IP-címet is tartalmazhat, más néven virtuális IP-címeket (VIP). Ezek az IP-címek szolgálnak a forgalom bemeneti pontjaként.
* Háttérbeli címkészlet – a virtuális gép hálózati adapteréhez (NIC) társított IP-címek, amelyek terhelését terjesztik.
* Terheléselosztási szabályok – a Rule tulajdonság egy adott előtéri IP-cím és port kombinációját képezi le háttérbeli IP-címek és portszámok készletéhez. Egyetlen terheléselosztó több terheléselosztási szabállyal is rendelkezhet. Mindegyik szabály egy előtérbeli IP-cím és egy port, valamint a virtuális gépekhez társított háttér-IP és port kombinációja.
* Mintavételek – a mintavételek lehetővé teszik a virtuálisgép-példányok állapotának nyomon követését. Ha az állapot-ellenőrzés meghiúsul, a rendszer automatikusan elvégzi a virtuálisgép-példány automatikus elforgatását.
* Bejövő NAT-szabályok – a NAT-szabályok határozzák meg az előtér-IP-n keresztül áramló bejövő forgalmat, és a háttérbeli IP-címhez vannak elosztva.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Gyorsindítási sablonok

Az Azure Resource Manager lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.

A sablonok tartalmazhatják a Virtual Machines, a virtuális hálózatok, a rendelkezésre állási csoportok, a hálózati adapterek (NIC), a Storage-fiókok, a terheléselosztó, a hálózati biztonsági csoportok és a nyilvános IP-címek definícióit. A sablonokkal mindent létrehozhat, amire szüksége lehet egy összetett alkalmazáshoz. A sablonfájl a verziókövetés és az együttműködés tartalomkezelő rendszerébe is ellenőrizhető.

[További információ a sablonokról](../azure-resource-manager/resource-manager-template-walkthrough.md)

[További információ a hálózati erőforrásokról](../networking/networking-overview.md)

A Azure Load Balancert használó gyors útmutatókat a [GitHub](https://github.com/Azure/azure-quickstart-templates) -tárházban találhatja meg, amely a Közösség által létrehozott sablonok készletét tárolja.

Példák a sablonokra:

* [2 virtuális gép Load Balancer és terheléselosztási szabályokkal](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 virtuális gép VNET belső Load Balancer és Load Balancer szabályokkal](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 virtuális gép egy Load Balancer és NAT-szabályok konfigurálása az LB-ben](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Azure Load Balancer beállítása PowerShell vagy parancssori felület használatával

Ismerkedés a Azure Resource Manager-parancsmagokkal, a parancssori eszközökkel és a REST API-kkal

* Load Balancer létrehozásához [Azure hálózati parancsmagok](https://docs.microsoft.com/powershell/module/az.network#networking) használhatók.
* [Terheléselosztó létrehozása Azure Resource Manager használatával](load-balancer-get-started-ilb-arm-ps.md)
* [Az Azure CLI használata az Azure erőforrás-kezeléssel](../xplat-cli-azure-resource-manager.md)
* [Load Balancer REST API-k](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>További lépések

Azt is megteheti, [hogy megkezdi az internetkapcsolattal rendelkező terheléselosztó létrehozását](load-balancer-get-started-internet-arm-ps.md) , valamint azt, hogy milyen típusú [terjesztési módot](load-balancer-distribution-mode.md) adjon meg egy adott terheléselosztó hálózati forgalmának működéséhez.

Megtudhatja, hogyan kezelheti [a terheléselosztó üresjárati TCP-időtúllépési beállításait](load-balancer-tcp-idle-timeout.md). Ez akkor fontos, ha az alkalmazásnak meg kell őriznie a kapcsolatokat a terheléselosztó mögötti kiszolgálókon.
