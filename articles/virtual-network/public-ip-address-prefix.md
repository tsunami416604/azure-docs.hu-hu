---
title: Azure nyilvános IP-cím előtagja | Microsoft Docs
description: Ismerje meg, hogy mi az Azure nyilvános IP-cím előtagja, és hogyan segíthet kiszámítható nyilvános IP-címek hozzárendeléséhez az erőforrásokhoz.
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
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 964bc915347d11e087da0b34a8d4160d807a4158
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965412"
---
# <a name="public-ip-address-prefix"></a>Nyilvános IP-cím előtagja

A nyilvános IP-cím előtag az Azure-beli nyilvános végpontok IP-címeinek fenntartott tartománya. Az Azure a megadott számon alapuló címtartományt foglal le az előfizetéshez. Ha még nem ismeri a nyilvános címeket, tekintse meg a [nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) című témakört.

A nyilvános IP-címek az egyes Azure-régiók címeinek egy készletéhez vannak rendelve. Az egyes régiókban az Azure által használt tartományok listáját is [letöltheti](https://www.microsoft.com/download/details.aspx?id=56519) . Például a 40.121.0.0/16 az egyik az USA keleti régiójában az Azure által használt 100-tartomány. A tartomány tartalmazza a 40.121.0.1-40.121.255.254 használható címét.

Egy nyilvános IP-cím előtagot hoz létre egy Azure-régióban és-előfizetésben egy név megadásával, valamint azt, hogy az előtag hány címet tartalmazzon. Ha például a/28 nyilvános IP-cím előtagját hozza létre, az Azure 16 címet foglal le az egyik tartományában. Nem tudja, hogy az Azure melyik tartományhoz lesz hozzárendelve, amíg létre nem hozza a tartományt, de a címek folytonos. A nyilvános IP-címek előtagjai díjkötelesek. Részletekért lásd: [nyilvános IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Miért érdemes létrehozni egy nyilvános IP-cím előtagot?

Nyilvános IP-cím erőforrások létrehozásakor az Azure egy elérhető nyilvános IP-címet rendel a régióban használt bármely tartományból. Miután az Azure hozzárendeli a címet, tudja, mi a címe, de amíg az Azure nem rendeli hozzá a címet, nem tudja, hogy melyik címet lehet hozzárendelni. Ez problémás lehet, ha például Ön vagy az Ön üzleti partnere, beállíthatja az adott IP-címeket engedélyező tűzfalszabályok beállításait. Minden alkalommal, amikor új nyilvános IP-címet rendel egy erőforráshoz, a címet fel kell venni a tűzfalszabálybe. Ha a nyilvános IP-cím előtagból oszt ki címeket az erőforrásokhoz, a tűzfalszabályok nem frissülnek minden alkalommal, amikor az egyik címet hozzárendeli, mert a teljes tartományt hozzá lehet adni egy szabályhoz.

## <a name="benefits"></a>Előnyök

- A nyilvános IP-címek erőforrásait ismert tartományból lehet létrehozni.
- Ön vagy az üzleti partnerei létrehozhatnak olyan tűzfalszabályok olyan tartományokkal, amelyek tartalmazzák a jelenleg hozzárendelt nyilvános IP-címeket, valamint a még nem hozzárendelt címeket. Így nincs szükség a tűzfalszabályok módosítására, amikor IP-címeket rendel hozzá az új erőforrásokhoz.
- A létrehozható tartomány alapértelmezett mérete/28 vagy 16 IP-cím.
- Az Azure-előfizetésekben lévő statikus nyilvános IP-címek maximális száma korlátozott, de a létrehozott tartományoknak nincs korlátai. Ennek eredményeképpen a létrehozott tartományok száma nem terjedhet ki több statikus nyilvános IP-címre, mint amennyit az előfizetésében lehet. További információ: Azure- [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Az előtagból származó címek használatával létrehozott címek bármely olyan Azure-erőforráshoz hozzárendelhetők, amelyhez nyilvános IP-címet rendelhet hozzá.
- Könnyen megtekintheti, hogy mely IP-címek vannak lefoglalva, és a tartományon belül még nem vannak lefoglalva.

## <a name="scenarios"></a>Alkalmazási helyzetek
A következő erőforrásokat a statikus nyilvános IP-címekhez rendelheti hozzá egy előtagból:

|Erőforrás|Alkalmazási helyzet|Lépések|
|---|---|---|
|Virtual Machines| A nyilvános IP-címek az Azure-beli virtuális gépekhez való társítása csökkenti a felügyeleti terhelést, amikor az IP-címek engedélyezve vannak a tűzfalon. Egyszerűen engedélyezheti a teljes előtagot egyetlen tűzfalszabály használatával. Az Azure-beli virtuális gépekkel való skálázás során az IP-címeket ugyanahhoz az előtagokhoz társíthatja, így a költségek, az idő és a felügyelet terhelése is megtakarítható.| IP-címek hozzárendelése egy előtagból a virtuális géphez: 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. [Rendelje hozzá az IP-címet a virtuális gép hálózati adapteréhez.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Standard Load Balancer | Nyilvános IP-címek társítása előtagból az előtérbeli IP-konfigurációba vagy egy Load Balancer kimenő szabálya biztosítja az Azure nyilvános IP-címtartomány egyszerűsítését. Egyszerűsítheti a forgatókönyvet úgy, hogy a kimenő kapcsolatok ápolása a nyilvános IP-előtag által meghatározott összefüggő IP-címekből származik. | IP-címek hozzárendelése előtagból a terheléselosztó számára: 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. A Load Balancer létrehozásakor válassza ki vagy frissítse a fenti 2. lépésben létrehozott IP-címet a Load Balancer előtér-IP-címével. |
| Azure Firewall | A kimenő SNAT előtagjaként nyilvános IP-címet is használhat. Ez azt jelenti, hogy az összes kimenő virtuális hálózati forgalom a [Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nyilvános IP-címére van lefordítva. Mivel ez az IP-cím egy előre meghatározott előtagból származik, nagyon könnyen megtudhatja, hogy az Azure-beli nyilvános IP-lábnyom milyen módon fog kinézni. | 1. [hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. [Az Azure tűzfal telepítésekor](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)ügyeljen arra, hogy kiválassza az előtagja által korábban lefoglalt IP-címet.|
| Application Gateway v2 | A nyilvános IP-címet az automatikus skálázás és a Zone-redundáns Application Gateway v2 előtaggal is használhatja. Mivel ez az IP-cím egy előre meghatározott előtagból származik, nagyon könnyen megtudhatja, hogy az Azure-beli nyilvános IP-lábnyom milyen módon fog kinézni. | 1. [hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-címet az előtagból.](manage-public-ip-address-prefix.md) 3. [A Application Gateway központi telepítésekor](../application-gateway/quick-create-portal.md#create-an-application-gateway)ügyeljen arra, hogy az előtagja által korábban lefoglalt IP-címet válassza ki.|

## <a name="constraints"></a>Korlátozások

- Nem adhatja meg az előtaghoz tartozó IP-címeket. Az Azure a megadott méret alapján foglalja le az előtaghoz tartozó IP-címeket.
- Létrehozhat egy legfeljebb 16 IP-címet vagy egy/28 előtagot. További információ: Azure- [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- A tartomány nem módosítható, miután létrehozta az előtagot.
- Csak a standard SKU-val létrehozott statikus nyilvános IP-címek rendelhetők hozzá az előtag tartományához. Ha többet szeretne megtudni a nyilvános IP-címekről, tekintse meg a [nyilvános IP-cím](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)című témakört.
- A tartomány címei csak Azure Resource Manager erőforrásokhoz rendelhetők hozzá. A klasszikus üzemi modellben nem lehet címeket hozzárendelni az erőforrásokhoz.
- Az előtagból létrehozott összes nyilvános IP-címnek ugyanabban az Azure-régióban és-előfizetésben kell lennie, mint az előtag, és hozzá kell rendelni az azonos régióhoz és előfizetéshez tartozó erőforrásokhoz.
- Egy előtag nem törölhető, ha az ahhoz tartozó címek hozzá vannak rendelve egy erőforráshoz társított nyilvános IP-címek erőforrásaihoz. Szüntesse meg az összes olyan nyilvános IP-cím-erőforrást, amely először az előtaghoz rendelt IP-címet.


## <a name="next-steps"></a>Következő lépések

- Nyilvános IP-cím előtagjának [létrehozása](manage-public-ip-address-prefix.md)
