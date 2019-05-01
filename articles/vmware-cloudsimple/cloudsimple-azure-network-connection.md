---
title: VMware megoldást, CloudSimple – az Azure hálózati kapcsolatok
description: További tudnivalók az Azure virtuális hálózat csatlakoztatása az CloudSimple régió hálózathoz
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577000"
---
# <a name="azure-network-connection-overview"></a>Az Azure network áttekintése

Egy régióban egy CloudSimple szolgáltatás létrehozásakor azt:

* Azure ExpressRoute-kapcsolatcsoport létrehozása és csatlakoztatja a szolgáltatás az adott régióban
* A CloudSimple régió hálózat csatlakozik az Azure virtuális hálózat vagy a helyszíni hálózat az Azure ExpressRoute használatával
* Az Azure-előfizetéshez, vagy a helyszíni hálózat, a magánfelhő-alapú környezetben futó hozzáférési szolgáltatásokat nyújt

Ez a kapcsolat nem nagy sávszélességű és kis késésű.

## <a name="benefits"></a>Előnyök

Az Azure hálózati kapcsolat teszi lehetővé:

* Az Azure-t a biztonsági mentési cél virtuális gépeknél a Private Cloud.
* A KMS-kiszolgálókat a Magánfelhő vSAN adattároló titkosítása az Azure-előfizetés telepítheti.
* Hibrid alkalmazások, amelyben a webes szint, az alkalmazás fut, amíg az alkalmazás a nyilvános felhőben, és a Magánfelhő futtathat adatbázisrétegeknek használja.

## <a name="expressroute-connection-to-on-premises-network"></a>A helyszíni hálózat az ExpressRoute-kapcsolat

A meglévő Azure ExpressRoute-kapcsolatcsoporttal csatlakozhat a CloudSimple régió. Globális elérhetőségű az ExpressRoute szolgáltatás használható egymással a két kapcsolatcsoporttal csatlakozhat.  A helyszíni és a CloudSimple ExpressRoute-Kapcsolatcsoportok között létrejön a kapcsolat.

Ez a módszer, amely a két környezet közötti kapcsolat jön létre:

* Biztonságos
* Privát
* Nagy sávszélesség
* Közel valós idejű adatelérés

Hozzon létre egy ExpressRoute-kapcsolat a helyszíni hálózathoz, hogy [forduljon az ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

* [Virtuális hálózati kapcsolat beállítása](https://docs.azure.cloudsimple.com/virtual-network-connection)