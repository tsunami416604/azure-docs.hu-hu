---
title: Az Azure Virtual Machine Scale Sets biztonsági vezérlői
description: Az Azure-Virtual Machine Scale Sets kiértékelésére szolgáló biztonsági ellenőrzési ellenőrzőlista
ms.service: virtual-machine-scale-sets
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 51ddd4f3775ae8e41c0d0bdd4ed8a3a2a59b7db4
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271783"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Az Azure Virtual Machine Scale Sets biztonsági vezérlői

Ez a cikk az Azure Virtual Machine Scale Sets beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Szolgáltatás végpontjának támogatása| Igen | |
| VNet-befecskendezés támogatása| Igen | |
| Hálózati elkülönítés és tűzfalak támogatása| Igen |  |
| Kényszerített bújtatás támogatása| Igen | Lásd: [kényszerített bújtatás konfigurálása a Azure Resource Manager üzemi modell használatával](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | Lásd: Linux rendszerű [virtuális gépek monitorozása és frissítése az Azure-ban](/azure/virtual-machines/linux/tutorial-monitoring) , valamint [Windowsos virtuális gépek monitorozása és frissítése az Azure-ban](/azure/virtual-machines/windows/tutorial-monitoring). |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |
| Adatsíkok naplózása és naplózása | Nem |  |

## <a name="identity"></a>Identitáskezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen |  |
| Engedélyezés| Igen |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | Lásd: [Azure Disk Encryption Virtual Machine Scale sets](disk-encryption-overview.md). |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | Az Azure Virtual Machines támogatja a [ExpressRoute](/azure/expressroute) és a VNet titkosítást. Lásd: [tranzitraktár titkosítás a virtuális gépeken](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél által felügyelt kulcsok egy támogatott Azure-titkosítási forgatókönyv; Lásd: [Azure Disk Encryption Virtual Machine Scale sets](disk-encryption-overview.md)|
| Oszlop szintű titkosítás (Azure Data Services)| – | |
| Titkosított API-hívások| Igen | HTTPS és SSL használatával. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen |  | 

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
