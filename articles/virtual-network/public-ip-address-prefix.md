---
title: Azure nyilvános IP-cím előtagja | Microsoft dokumentumok
description: Ismerje meg, hogy mi az Azure nyilvános IP-cím előtagja, és hogyan segíthet kiszámítható nyilvános IP-címek hozzárendelése az erőforrásokhoz.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: anavin
ms.openlocfilehash: 9604d5567e74ef470c3e798b6a3df9e48d6fde5d
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985303"
---
# <a name="public-ip-address-prefix"></a>Nyilvános IP-cím előtagja

A nyilvános IP-cím előtag az Azure-beli nyilvános végpontok fenntartott IP-címtartománya. Az Azure egy összefüggő címtartományt rendel az előfizetéshez a megadott száma alapján. Ha nem ismeri a nyilvános címeket, olvassa el a [Nyilvános IP-címek című témakört.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Nyilvános IP-címek vannak rendelve egy címkészlet minden Azure-régióban. [Letöltheti](https://www.microsoft.com/download/details.aspx?id=56519) az Azure által az egyes régiókhoz használt tartományok listáját. Például 40.121.0.0/16 egyike a több mint 100 tartományok Azure használja az USA keleti régiójában. A tartomány a 40.121.0.1 - 40.121.255.254 használható címeket tartalmazza.

Hozzon létre egy nyilvános IP-cím előtagot egy Azure-régióban és előfizetést egy név megadásával, és hány címet szeretne az előtag tartalmaznia. Ha például /28 nyilvános IP-címelőtagot hoz létre, az Azure 16 címet foglal le az egyik tartományából. Nem tudja, hogy az Azure melyik tartományt rendeli hozzá, amíg létre nem hozza a tartományt, de a címek összefüggőek. A nyilvános IP-cím előtagok díjkötelesek. További információt a [nyilvános IP-címdíjszabás című](https://azure.microsoft.com/pricing/details/ip-addresses)témakörben talál.

## <a name="why-create-a-public-ip-address-prefix"></a>Miért érdemes nyilvános IP-címelőtagot létrehozni?

Nyilvános IP-cím erőforrások létrehozásakor az Azure hozzárendel egy elérhető nyilvános IP-címet a régióban használt tartományok bármelyikéből. Miután az Azure hozzárendeli a címet, tudja, mi a cím, de amíg az Azure hozzá nem rendeli a címet, nem tudja, hogy milyen cím rendelhető hozzá. Ez akkor lehet problémás, ha ön vagy üzleti partnerei olyan tűzfalszabályokat állítanak be, amelyek meghatározott IP-címeket engedélyeznek. Minden alkalommal, amikor új nyilvános IP-címet rendel egy erőforráshoz, a címet hozzá kell adni a tűzfalszabályhoz. Ha nyilvános IP-címelőtagból rendel címeket az erőforrásokhoz, a tűzfalszabályokat nem kell minden alkalommal frissíteni, amikor hozzárendel egy címet, mert a teljes tartomány hozzáadható egy szabályhoz.

## <a name="benefits"></a>Előnyök

- Ismert tartományból nyilvános IP-címerőforrásokat hozhat létre.
- Ön vagy üzleti partnerei tűzfalszabályokat hozhatnak létre olyan tartományokkal, amelyek tartalmazzák a jelenleg kiosztott nyilvános IP-címeket, valamint azokat a címeket, amelyeket még nem osztott hozzá. Így nincs szükség a tűzfalszabályok módosítására, amikor IP-címeket rendel az új erőforrásokhoz.
- A létrehozható tartomány alapértelmezett mérete /28 vagy 16 IP-cím.
- Nincs enek korlátok, hogy hány tartományokat hozhat létre, azonban vannak korlátok a statikus nyilvános IP-címek maximális száma lehet egy Azure-előfizetésben. Ennek eredményeképpen a létrehozott tartományok száma nem foglalhat magában több statikus nyilvános IP-címet, mint az előfizetésben. További információ: [Azure-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Az előtagból származó címek használatával létrehozott címek bármely Azure-erőforráshoz hozzárendelhetők, amelyhez nyilvános IP-címet rendelhet.
- Könnyen megtekintheti, hogy mely IP-címeket osztják le, és még nem osztották ki a tartományon belül.

## <a name="scenarios"></a>Forgatókönyvek
Egy előtagból a következő erőforrásokat társíthatja egy statikus nyilvános IP-címhez:

|Erőforrás|Forgatókönyv|Lépések|
|---|---|---|
|Virtuális gépek| A nyilvános IP-k társítása egy előtagról a virtuális gépekre az Azure-ban csökkenti a felügyeleti többletterhelést, amikor az IP-k tűzfalon való engedélyezési listájáról van szó. Egyszerűen egy teljes előtagot is felsorolhat egyetlen tűzfalszabállyal. Az Azure-beli virtuális gépekkel való méretezés során ugyanattól az előtagtól társítható az IP-k, költség-, idő- és felügyeleti többletköltségek.| IP-k társítása előtagról a virtuális gépre: 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. [Társítsa az IP-címet a virtuális gép hálózati illesztőjéhez.](virtual-network-network-interface-addresses.md#add-ip-addresses) Az [IP-eket virtuálisgép-méretezési készlethez](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)is társíthatja.
| Standard terheléselosztók | A nyilvános IP-címeket egy előtagról az előtér-IP-konfigurációra vagy a terheléselosztó kimenő szabályára társítja, így egyszerűsítheti az Azure nyilvános IP-címterét. Egyszerűsítheti a forgatókönyvet, ha a kimenő kapcsolatok at egy nyilvános IP-előtag által meghatározott összefüggő IP-címek tartományából kell származnia. | Ip-k társítása előtagból a terheléselosztóhoz: 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. A terheléselosztó létrehozásakor válassza ki vagy frissítse a fenti 2. |
| Azure Firewall | A kimenő SNAT előtagból származó nyilvános IP-címet is használhatja. Ez azt jelenti, hogy az összes kimenő virtuális hálózati forgalom lefordítása az [Azure Tűzfal](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nyilvános IP-címére. Mivel ez az IP-cím egy előre meghatározott előtagból származik, nagyon könnyen megállapítható, hogy a nyilvános IP-lábnyom az Azure-ban hogyan fog kinézni. | 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. Az [Azure-tűzfal telepítésekor](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)győződjön meg arról, hogy válassza ki a korábban lefoglalt IP-cím az előtagból.|
| Alkalmazásátjáró v2 | Használhatja a nyilvános IP-t egy előtag az automatikus skálázás és a zónaredundáns alkalmazásátjáró v2. Mivel ez az IP-cím egy előre meghatározott előtagból származik, nagyon könnyen megállapítható, hogy a nyilvános IP-lábnyom az Azure-ban hogyan fog kinézni. | 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. Az [Application Gateway telepítésekor](../application-gateway/quick-create-portal.md#create-an-application-gateway)mindenképpen válassza ki a korábban lefoglalt IP-címet az előtagból.|

## <a name="constraints"></a>Korlátozások

- Az előtag IP-címei nem adhatók meg. Az Azure lefoglalja az előtag IP-címeket a megadott méret alapján.
- Alapértelmezés szerint legfeljebb 16 IP-címből vagy /28-as előtagból hozhat létre előtagot. Tekintse át [a hálózati korlátok növelése kérelmek](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) és az [Azure-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) további információkért.
- Az előtag létrehozása után nem módosíthatja a tartományt.
- Csak a standard termékváltozattal létrehozott statikus nyilvános IP-címek rendelhetők hozzá az előtag tartományából. Ha többet szeretne megtudni a nyilvános IP-cím-skus-okról, olvassa el a [nyilvános IP-címet.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- A tartományból származó címek csak az Azure Resource Manager-erőforrásokhoz rendelhetők hozzá. A klasszikus központi telepítési modell erőforrásaihoz nem rendelhetők címek.
- Az előtagból létrehozott összes nyilvános IP-címnek ugyanabban az Azure-régióban és előfizetésben kell léteznie, mint az előtagnak, és hozzá kell rendelni az ugyanabban a régióban és előfizetésben lévő erőforrásokhoz.
- Nem törölhet előtagot, ha a benne lévő címek egy erőforráshoz társított nyilvános IP-cím-erőforrásokhoz vannak rendelve. Először az ELŐTAGHOZ RENDELT NYILVÁNOS IP-címerőforrásokat távolítsa el az előtagtól.


## <a name="next-steps"></a>További lépések

- Nyilvános IP-címelőtag [létrehozása](manage-public-ip-address-prefix.md)
