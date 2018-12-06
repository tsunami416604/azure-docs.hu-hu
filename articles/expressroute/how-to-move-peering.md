---
title: Helyezze át a nyilvános társviszony-létesítést az Azure expressroute-on Microsoft társviszony-létesítésre |} A Microsoft Docs
description: Ez a cikk bemutatja, a lépések végrehajtásával helyezhetők át, a nyilvános társviszony-létesítés a Microsoft társviszony-létesítést az expressroute-on.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: 579f8874459004ef6bfa0d0794ab09333e053acb
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966114"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Helyezze át a nyilvános társviszony-létesítés Microsoft társviszony-létesítésre

Az ExpressRoute támogatja a Microsoft-társviszony-létesítés útvonalszűrőkkel való használatát az Azure PaaS-szolgáltatások, például az Azure Storage és az Azure SQL Database esetén. A Microsoft PaaS- és SaaS-szolgáltatásokhoz való hozzáféréshez most csak egy útválasztási tartományra van szüksége. Útvonalszűrők segítségével szelektíven meghirdetheti a PaaS-szolgáltatások előtagjait a felhasználni kívánt Azure-régiókhoz.

Ez a cikk segít helyezze át a nyilvános társviszony-létesítés konfigurációját a Microsoft társviszony-létesítés üzemkimaradás nélkül. Útválasztási tartományok és a társviszony-létesítéseket kapcsolatos további információkért lásd: [ExpressRoute-Kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).


## <a name="before"></a>Előkészületek

* Ha csatlakozni szeretne a Microsoft társviszony-létesítés, kell beállítása és kezelése helyezkedik el. A kapcsolatszolgáltató előfordulhat, hogy beállítása és kezelése a NAT egy felügyelt szolgáltatásként. Ha azt tervezi, az Azure PaaS és Microsoft társviszony-létesítés Azure SaaS-szolgáltatások eléréséhez, fontos a megfelelő méretezés a NAT IP-készlet. Az ExpressRoute NAT kapcsolatos további információkért lásd: a [Microsoft társviszony-létesítés NAT-követelményei](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Ha a nyilvános társviszony-létesítés használata jelenleg rendelkezik az IP-hálózati szabályok használt nyilvános IP-címek hozzáférés [Azure Storage](../storage/common/storage-network-security.md) vagy [Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md), győződjön meg róla, hogy a NAT IP-címkészlet konfigurálva kell a Microsoft társviszony-létesítés tartalmazza az Azure storage-fiók vagy az Azure SQL-fiók nyilvános IP-címek listáját.

* Annak érdekében, hogy a Microsoft társviszony-létesítés üzemkimaradás nélkül át, kövesse a lépéseket ebben a sorrendben jelenjenek meg ezek a cikkben.

## <a name="create"></a>1. A Microsoft társviszony-létesítés

Microsoft társviszony-létesítés nem lett létrehozva, ha bármely, a következő cikkek segítségével a Microsoft társviszony-létesítés. Ha felügyelt kapcsolat szolgáltató ajánlatait igényei 3 services layer, megkérheti a kapcsolatszolgáltató engedélyezése a Microsoft társviszony-létesítést a kapcsolatcsoporthoz.

  * [Hozzon létre a Microsoft társviszony-létesítés Azure portal használatával](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Hozzon létre a Microsoft társviszony-létesítés Azure Powershell-lel](expressroute-howto-routing-arm.md#msft)
  * [Hozzon létre a Microsoft társviszony-létesítés Azure CLI használatával](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Ellenőrizze a Microsoft társviszony-létesítés engedélyezve van

Győződjön meg arról, hogy a Microsoft társviszony-létesítés engedélyezve van, és a meghirdetett nyilvános előtagok konfigurált állapotban vannak.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurálása és a egy útvonalszűrőhöz csatlakoztatása kapcsolatcsoporthoz

Alapértelmezés szerint a Microsoft társviszony-létesítéseket új ne hirdesse meg bármely előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport. Egy útvonalszűrő-szabály létrehozásakor az Azure-régiók, amelyet szeretne felhasználni az Azure PaaS-szolgáltatások, az alábbi képernyőképen látható módon szolgáltatásközösségek listájában adhatja meg:

![Nyilvános társviszony-létesítés egyesítése](./media/how-to-move-peering/public.png)

Adja meg a következő cikkek bármelyikével útvonalszűrők:

  * [A Microsoft társviszony-létesítést az Azure portal használatával útvonalszűrőinek konfigurálása](how-to-routefilter-portal.md)
  * [Microsoft társviszony-létesítés Azure PowerShell-lel útvonalszűrőinek konfigurálása](how-to-routefilter-powershell.md)
  * [Microsoft társviszony-létesítés Azure CLI-vel útvonalszűrőinek konfigurálása](how-to-routefilter-cli.md)

## <a name="delete"></a>4. A nyilvános társviszony-létesítés törlése

Miután ellenőrizte, hogy a Microsoft társviszony-létesítésre van konfigurálva, és az előtagok fel kívánja használni a Microsoft társviszony-létesítés megfelelően van-e hirdetve, majd törölheti a nyilvános társviszony-létesítés. A nyilvános társviszony-létesítés törléséhez használja a következő cikkek valamelyikét:

  * [Az Azure nyilvános társviszony-létesítés törlése az Azure portal használatával](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Az Azure nyilvános társviszony-létesítés törlése az Azure PowerShell-lel](expressroute-howto-routing-arm.md#deletepublic)
  * [Az Azure nyilvános társviszony-létesítés törlése parancssori felület használatával](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Társviszony-létesítéseket megtekintése
  
Minden ExpressRoute-Kapcsolatcsoportok és a társviszony az Azure Portalon listáját láthatja. További információkért lásd: [megtekintése a Microsoft társviszony-létesítés részleteinek](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>További lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
