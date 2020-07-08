---
title: Azure Linux Virtual Machines – Linux biztonsági vezérlők
description: Az Azure-Linux Virtual Machines kiértékelésére szolgáló biztonsági ellenőrzési ellenőrzőlista
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77190570"
---
# <a name="security-controls-for-linux-virtual-machines"></a>A Linux Virtual Machines biztonsági vezérlői

Ez a cikk a Linux Virtual Machines beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Yes | |
| VNet-befecskendezés támogatása| Yes | |
| Hálózati elkülönítés és tűzfalak támogatása| Yes |  |
| Kényszerített bújtatás támogatása| Yes | Lásd: [kényszerített bújtatás konfigurálása a Azure Resource Manager üzemi modell használatával](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | Lásd: [Linux rendszerű virtuális gépek monitorozása és frissítése az Azure-ban](/azure/virtual-machines/linux/tutorial-monitoring). |
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
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | Lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md). |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes | Az Azure Virtual Machines támogatja a [ExpressRoute](/azure/expressroute) és a VNet titkosítást. Lásd: [tranzitraktár titkosítás a virtuális gépeken](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Yes | Az ügyfél által felügyelt kulcsok egy támogatott Azure-titkosítási forgatókönyv; Lásd: az [Azure-titkosítás áttekintése](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Oszlop szintű titkosítás (Azure Data Services)| N.A. | |
| Titkosított API-hívások| Yes | HTTPS és TLS protokollon keresztül. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  | 

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../../security/fundamentals/security-controls.md).
