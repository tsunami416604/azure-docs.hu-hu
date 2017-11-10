---
title: "Helyezze át a nyilvános társviszony-létesítés az Azure ExpressRoute a Microsoft társviszony-létesítés |} Microsoft Docs"
description: "Ez a cikk bemutatja, a lépések a Microsoft a nyilvános társviszony az ExpressRoute-társviszony létesítése –."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/09/2017
ms.author: cherylmc
ms.openlocfilehash: f48e270054ba45211c2a1517593d5d7b22925d0a
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Helyezze át a nyilvános társviszony-létesítést úgy Microsoft társviszony-létesítés

A Microsoft társviszony-létesítés útvonal szűrők az Azure PaaS szolgáltatások, például az Azure storage és az Azure SQL Database ExpressRoute támogatja. Most kell a Microsoft PaaS és a Szolgáltatottszoftver-szolgáltatások eléréséhez csak egy útválasztási tartomány. Útvonal-szűrőkkel szelektív hirdetési használni kívánt Azure-régiók PaaS szolgáltatás előtagjait.

Ez a cikk segítséget nyújt egy nyilvános társviszony-létesítési konfiguráció áthelyezése a Microsoft társviszony-létesítés állásidő nélkül. További információ az útválasztási tartományok és esetében: [ExpressRoute Kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> A prémium szintű ExpressRoute-bővítmény kell rendelkeznie ahhoz, hogy a Microsoft társviszony-létesítés. A prémium szintű bővítmény kapcsolatos további információkért tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md#expressroute-premium).

## <a name="before"></a>Előkészületek

* Ha csatlakozni szeretne a Microsoft társviszony-létesítést, kell beállítása és kezelése a hálózati címfordítást. A kapcsolat szolgáltatójánál előfordulhat, hogy beállítása és kezelése a felügyelt szolgáltatásként NAT. Ha azt tervezi, hogy elérje az Azure PaaS és a Microsoft társviszony-létesítés Azure szolgáltatott szoftver szolgáltatásokat, fontos a hálózati Címfordítás IP-készlet megfelelő méretének. Az ExpressRoute NAT kapcsolatos további információkért tekintse meg a [NAT követelmények a Microsoft társviszony-létesítéshez](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Ha már szerepel az Azure nyilvános társviszony Azure PaaS szolgáltatás-erőforrást jelenleg rendelkezik egy hálózati hozzáférés-vezérlési lista (ACL), akkor győződjön meg arról, hogy a hálózati Címfordítás IP-készlet **címtartomány** Microsoft megtalálható a tűzfal hozzáférés-vezérlési lista társviszony-létesítés.

* Ahhoz, hogy a Microsoft társviszony-létesítés állásidő nélkül áthelyezi, kövesse a lépéseket a cikk jelenjenek meg ezek a sorrendben.

## <a name="create"></a>1. A Microsoft társviszony-létesítés létrehozása

Ha a Microsoft társviszony-létesítés nem lett létrehozva, használja a következő cikkekben valamelyikét létrehozása a Microsoft társviszony-létesítés. Ha a kapcsolat szolgáltató ajánlatok felügyelt réteg 3 szolgáltatások, kérje meg a kapcsolat szolgáltatójánál engedélyezése a Microsoft a kör társviszony-létesítés.

  * [Hozzon létre a Microsoft társviszony-létesítés Azure-portál használatával](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Hozzon létre a Microsoft társviszony-létesítés Azure Powershell használatával](expressroute-howto-routing-arm.md#msft)
  * [Hozzon létre a Microsoft társviszony-létesítés Azure parancssori felület használatával](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Ellenőrizze a Microsoft társviszony-létesítés engedélyezve van

Győződjön meg arról, hogy engedélyezve van a Microsoft társviszony-létesítést, és a meghirdetett nyilvános előtag konfigurált állapotban van.

  * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure CLI](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurál és összekapcsol útvonal szűrőt a kapcsolatcsoport

Alapértelmezés szerint új Microsoft társviszony hirdetményt a előtagokat mindaddig, amíg egy útvonal-szűrőt a kapcsolatcsoport van csatolva. Amikor egy útvonal-szűrő szabályt hoz létre, megadhatja a szolgáltatás közösségeiben kíván felhasználni az Azure PaaS szolgáltatások, az alábbi képernyőfelvételen látható módon Azure-régiók listáját:

![Nyilvános társviszony egyesítése](.\media\how-to-move-peering\public.png)

Használja a következő cikkek útvonal szűrők konfigurálására:

  * [Be annak a Microsoft társviszony-létesítéshez az Azure portál használatával](how-to-routefilter-portal.md)
  * [Be annak a Microsoft társviszony-létesítéshez Azure PowerShell használatával](how-to-routefilter-powershell.md)
  * [Be annak a Microsoft társviszony-létesítéshez Azure parancssori felület használatával](how-to-routefilter-cli.md)

## <a name="delete"></a>4. A nyilvános társviszony törlése

Miután meggyőződött arról, hogy a Microsoft társviszony-létesítés van konfigurálva, és a előtagokat fel kívánja megfelelően hirdet a Microsoft társviszony-létesítést, majd törölheti a nyilvános társviszony-létesítést. A nyilvános társviszony törléséhez használja a következő cikkekben valamelyikét:

  * [Törölje az Azure nyilvános társviszony-létesítés Azure-portál használatával](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Törölje az Azure nyilvános társviszony-létesítés Azure PowerShell használatával](expressroute-howto-routing-arm.md#deletepublic)
  * [Törölje az Azure nyilvános társviszony-létesítés parancssori felület használatával](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Következő lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).