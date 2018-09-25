---
title: Az Azure nyilvános IP-címelőtag |} A Microsoft Docs
description: További tudnivalók milyen az Azure nyilvános IP-címelőtag van, és hogyan segíthet kiszámítható nyilvános IP-címek hozzárendelése az erőforrások.
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
ms.openlocfilehash: 5bbe0709f89ca198b0571526291f700c99e9e59f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966826"
---
# <a name="public-ip-address-prefix"></a>Nyilvános IP-cím előtagja

Egy nyilvános IP-címelőtag egy fenntartott IP-címtartományt a nyilvános végpontok az Azure-ban. Az Azure-címek alapján hány megadhatja az előfizetés egybefüggő dátumtartományt foglal le. Ha még nem ismeri a nyilvános cím, [nyilvános IP-címek.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Nyilvános IP-címek egy címkészletből minden egyes Azure-régióban vannak rendelve. Is [letöltése](https://www.microsoft.com/download/details.aspx?id=41653) az minden olyan régió esetében használja az Azure-címtartományok listáját. Ha például 40.121.0.0/16 az egyik több mint 100 tartományokat az Azure használ, az USA keleti régiójában. A tartományba beletartozik az 40.121.0.1 - felhasználható cím 40.121.255.254.

Hoz létre egy nyilvános IP-címelőtag egy Azure-régióban és az előfizetés nevét adja meg, és hány cím kívánja felvenni az előtagot. Például ha létrehoz egy nyilvános IP-címelőtag/28-as, Azure foglal le 16 címeket a tartományok közül az Ön számára. Melyik Azure fog hozzárendelni, amíg nem hoz létre a tartomány tartomány nem tudja, de a címek olyan egybefüggő. Nyilvános IP-címelőtagokat díjat kell. További információkért lásd: [nyilvános IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses).

> [!IMPORTANT]
> Nyilvános IP-előtag van, korlátozott számú régióban egy nyilvános előzetes verzióban érhető el. Is [ismerje meg, hogy mit jelent az előzetes verzióban](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Nyilvános IP-címelőtag jelenleg érhető el: USA nyugati középső Régiója, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA középső RÉGIÓJA, Észak-Európa, Nyugat-Európa és Délkelet-Ázsia. Régiók frissített listáját, keresse fel [Azure-frissítések](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="why-create-a-public-ip-address-prefix"></a>Miért érdemes létrehozni egy nyilvános IP-címelőtag?

Nyilvános IP-cím erőforrás létrehozásakor az Azure hozzárendelhetők elérhető nyilvános IP-cím a tartományokkal a régióban használják őket. Az Azure hozzárendeli a címet, ha tudja, hogy mi a címet, de addig, amíg az Azure hozzárendeli a címet, nem tudja, milyen címet hozzárendelni. A problémás lehet, ha például Ön, vagy az üzleti partnerek tűzfalszabályok beállítása, amely lehetővé teszi meghatározott IP-címek. Minden alkalommal, amikor új nyilvános IP-címet rendel egy erőforrást, a cím a tűzfalszabály hozzáadandó rendelkezik. Az erőforrások címeket rendel egy nyilvános IP-címelőtag a, ha tűzfalszabályok nem kell minden alkalommal, amikor a címek hozzárendelését frissíthető, mert a teljes körű hozzá lehet adni egy szabályt.

## <a name="benefits"></a>Előnyök

- Nyilvános IP-cím erőforrás is létrehozhat egy ismert tartományból.
- Ön vagy az üzleti partnerek tartományok, amelyek tartalmazzák a jelenleg hozzárendelt nyilvános IP-címeket, valamint a még nem rendelt címek hozhat létre tűzfalszabályokat. Ez így nem kell a tűzfalszabályokat módosítani, mert IP-címek hozzárendelése új erőforrásokat.
- Az alapértelmezett méret egy tartományt is létrehozhat, / 28 vagy 16 IP-címek.
- Nincs korlátozás feltárhatja, hogy hány tartományokat is létrehozhat, azonban nincsenek korlátozások az Azure-előfizetéssel is rendelkezik statikus nyilvános IP-címek maximális számát. Ennek eredményeképpen hoz létre tartományok száma nem tartalmazzák a további statikus nyilvános IP-címek, mint az előfizetés is lehet. További információkért lásd: [Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- A címeket, létrehozhatja az előtag-címeket rendelhet bármely Azure-erőforrások, amelyeket hozzárendelhet egy nyilvános IP-címet.
- Könnyen látható, amely IP-címek vannak lefoglalva, és még ki nem adott tartományon belül.

## <a name="scenarios"></a>Forgatókönyvek
Az előtag a következő erőforrások társítható egy statikus nyilvános IP-cím:

|Erőforrás|Forgatókönyv|Lépések|
|---|---|---|
|Virtuális gépek| Nyilvános IP-címek az előtag az Azure-beli virtuális gépekhez való társítása csökkenti a felügyeleti terhelést, esetén, a tűzfal IP-címek engedélyezési. Egyszerűen csak engedélyezett egy teljes előtagból egyetlen tűzfalszabályt is. Megoldást az Azure-beli virtuális gépek, a költség, idő és munkaterhelést mentése ugyanazon előtaggal IP-címek is hozzárendelhetők.| IP-címek társításához az előtag a virtuális gépre: 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-cím előtag.](manage-public-ip-address-prefix.md) 3. [Az IP-cím a virtuális gép hálózati adapteréhez társítja.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Terheléselosztók | Társítja a nyilvános IP-címek az előtag, az előtérbeli IP-konfiguráció vagy a terheléselosztó kimenő szabály biztosítja, hogy az Azure nyilvános IP-címtér egyszerűsítését. A forgatókönyv egyszerűsítheti karcsúsítási kimenő kapcsolatokat lehet egy nyilvános IP-címelőtag által meghatározott folytonos IP-címek tartományának származik. | IP-címek társításához az előtag a terheléselosztóhoz: 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-cím előtag.](manage-public-ip-address-prefix.md) 3. A terheléselosztó létrehozásakor válassza ki, vagy frissítse a IP-címet az előtérbeli IP-címét a terheléselosztó a fenti 2. lépésben létrehozott. |
| Azure Firewall | Kimenő SNAT az előtag nyilvános IP-cím használható. Ez azt jelenti, hogy az összes kimenő virtuális hálózati forgalom fordítja le a [Azure tűzfal](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nyilvános IP-cím. Mivel az IP egy előre meghatározott előtag származik, célszerű rendkívül könnyen lehet előre tudni az Azure-ban a nyilvános IP-erőforrásigénye fog kinézni. | 1. [Hozzon létre egy előtagot.](manage-public-ip-address-prefix.md) 2. [Hozzon létre egy IP-cím előtag.](manage-public-ip-address-prefix.md) 3. Ha Ön [üzembe helyezése az Azure-tűzfalon](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), ügyeljen arra, hogy válassza ki az Ön previosuly lefoglalt IP előtagot.|

## <a name="constraints"></a>Korlátozások

- Az IP-címek a előtag nem adható meg. Az Azure az előtag, az Ön által megadott méret alapján foglal le IP-címeket.
- A tartomány az előtag létrehozását követően nem módosítható.
- Csak az IPv4-címek tartománya. A tartomány nem tartalmaz IPv6-címek.
- Csak statikus nyilvános IP-címeket a Standard Termékváltozat létrehozott hozzárendelhetők az előtag a tartományból. További információ a nyilvános IP-cím termékváltozatok kapcsolatban lásd: [nyilvános IP-cím](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- A tartomány-címeket csak az Azure Resource Manager-erőforrások lehet hozzárendelni. Címek nem rendelhetők erőforrásokhoz a klasszikus üzemi modellben.
- Az előtag alapján létrehozott minden nyilvános IP-cím az ugyanazon Azure-régióban és az előfizetés előtagot léteznie kell, és hozzá kell rendelni az azonos régióban, és az előfizetés erőforrásainak.
- Előtag nem lehet törölni, ha bármely címét, benne egy erőforrás társított nyilvános IP-cím erőforrások vannak rendelve. Az összes nyilvános IP-cím erőforrások rendelt IP-címek az előtag, először a társítást.


## <a name="next-steps"></a>További lépések

- [Hozzon létre](manage-public-ip-address-prefix.md) egy nyilvános IP-cím előtagja
