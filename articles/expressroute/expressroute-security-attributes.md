---
title: Gyakori biztonsági attribútumok az Azure expressroute-hoz
description: A gyakori biztonsági attribútumok Azure ExpressRoute értékelésére ellenőrzőlista
services: expressroute
ms.service: expressroute
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: mbaldwin
ms.openlocfilehash: d6156715fb87831d465197fd8eec59d245221e48
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083274"
---
# <a name="common-security-attributes-for-azure-expressroute"></a>Gyakori biztonsági attribútumok az Azure expressroute-hoz

Biztonsági integrálva van az Azure-szolgáltatások minden szempontját. Ez a cikk a gyakori biztonsági attribútumok Azure ExpressRoute beépített dokumentumok.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Megelőző

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Titkosítás inaktív állapotban:<ul><li>Kiszolgálóoldali titkosítás</li><li>Kiszolgálóoldali titkosítás a felhasználó által kezelt kulcsok</li><li>Más titkosítási funkciók (például az ügyféloldali, mindig titkosított, stb.)</ul>|  – | Az ExpressRoute nem tárolnak ügyféladatokat. |
| Titkosítás az átvitel során:<ul><li>Express route-titkosítás</li><li>A Vnet-titkosítás</li><li>Hálózatok titkosítása</ul>| Nem | |
| Titkosítási kulcs kezelése (CMK, BYOK, stb.)| – |  |
| Oszlop a blokkszintű titkosítás (az Azure Data Services)| – | |
| Titkosított API-hívások| Igen | Keresztül [az Azure Resource Manager](../azure-resource-manager/index.yml) és a HTTPS. |

## <a name="network-segmentation"></a>Hálózati szegmentálást

| Biztonsági attribútum | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatási végpont támogatás| – |  |
| vNET-injektálási támogatás| – | |
| Hálózatelkülönítés és támogatási optimalizálóként működik| Igen | Minden ügyfél saját útválasztási tartomány található, és bújtatással jut el a saját virtuális hálózaton |
| Kényszerített bújtatás támogatása| – | Border Gateway Protocol (BGP) keresztül |

## <a name="detection"></a>Észlelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitorozási támogatása (a Log analytics, az App insights, stb.)| Igen | Lásd: [ExpressRoute-monitorozás, mérőszámok és riasztások](expressroute-monitoring-metrics-alerts.md).|

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen | Szolgáltatásfiók átjáróhoz a Microsoft (GWM) (vezérlő); Igény szerinti (JIT) fejlesztéshez és OP. elérése |
| Engedélyezés|  Igen |Szolgáltatásfiók átjáróhoz a Microsoft (GWM) (vezérlő); Igény szerinti (JIT) fejlesztéshez és OP. elérése |


## <a name="audit-trail"></a>Auditnapló

| Biztonsági attribútum | Igen/nem | Megjegyzések| 
|---|---|--|
| Vezérlő és a felügyeleti sík naplózási és naplózása| Igen |  |
| Adatsík naplózása és naplózása| Nem |   |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági attribútum | Igen/nem | Megjegyzések|
|---|---|--|
| Konfiguráció kezelésével kapcsolatos támogatás (versioning konfiguráció stb.)| Igen | Keresztül a hálózati erőforrás-szolgáltató (NRP). |
