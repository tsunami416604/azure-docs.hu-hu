---
title: Az Azure Windows Virtual Machines biztonsági vezérlői
description: Az Azure-Windows Virtual Machines kiértékelésére szolgáló biztonsági ellenőrzési ellenőrzőlista
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 6ab6133faef4a6c7a8eb929e5f4cd1840e971a99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088342"
---
# <a name="security-controls-for-windows-virtual-machines"></a>A Windows Virtual Machines biztonsági vezérlői

Ez a cikk a Windows Virtual Machines beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Yes | |
| VNet-befecskendezés támogatása| Yes | |
| Hálózati elkülönítés és tűzfalak támogatása| Yes |  |
| Kényszerített bújtatás támogatása| Yes | Lásd: [kényszerített bújtatás konfigurálása a Azure Resource Manager üzemi modell használatával](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | [Windows rendszerű virtuális gépek monitorozása és frissítése az Azure-ban](./tutorial-monitor.md). |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes |  |
| Adatsíkok naplózása és naplózása | No |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Hitelesítés| Yes |  |
| Engedélyezés| Yes |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | Lásd: [virtuális lemezek titkosítása egy Windows rendszerű virtuális gépen](./disk-encryption-overview.md). |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes | Az Azure Virtual Machines támogatja a [ExpressRoute](../../expressroute/index.yml) és a VNet titkosítást. Lásd: [tranzitraktár titkosítás a virtuális gépeken](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Yes | Az ügyfél által felügyelt kulcsok egy támogatott Azure-titkosítási forgatókönyv; Lásd: az [Azure-titkosítás áttekintése](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Oszlop szintű titkosítás (Azure Data Services)| n.a. | |
| Titkosított API-hívások| Yes | HTTPS és TLS protokollon keresztül. |



## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  | 

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).
