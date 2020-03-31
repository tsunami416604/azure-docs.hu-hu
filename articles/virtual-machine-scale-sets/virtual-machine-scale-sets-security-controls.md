---
title: Az Azure virtuálisgép-méretezési készletek biztonsági vezérlői
description: Az Azure virtuálisgép-méretezési készletek kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
ms.service: virtual-machine-scale-sets
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 4007f4adeee065fe32492d3bd16f3a06d24e7d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190608"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Az Azure virtuálisgép-méretezési készletek biztonsági vezérlői

Ez a cikk az Azure virtuálisgép-méretezési készletekbe beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| A szolgáltatás végpontjának támogatása| Igen | |
| A VNet injekciózás támogatása| Igen | |
| Hálózati elkülönítés és tűzfaltámogatás| Igen |  |
| Kényszerített bújtatástámogatása| Igen | Lásd: [Kényszerített bújtatás konfigurálása az Azure Resource Manager telepítési modelljével.](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Tekintse meg [a Linux-alapú virtuális gépek figyelése és frissítése az Azure-ban](/azure/virtual-machines/linux/tutorial-monitoring) és a [Monitorban, valamint a Windows virtuális gépek frissítése az Azure-ban.](/azure/virtual-machines/windows/tutorial-monitoring) |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen |  |
| Adatsík naplózása és naplózása | Nem |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Hitelesítés| Igen |  |
| Engedélyezés| Igen |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések |
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | Lásd: [Azure disk encryption for virtual machine scale sets](disk-encryption-overview.md). |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat titkosítása és Virtuálishálózati titkosítás)| Igen | Az Azure virtuális gépek támogatja az [ExpressRoute](/azure/expressroute) és a Virtuális hálózat titkosítását. Lásd: [Bejövő titkosítás a virtuális gépeken.](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms) |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél által felügyelt kulcsok egy támogatott Azure titkosítási forgatókönyv; lásd: [Azure lemeztitkosítás a virtuálisgép-méretezési készletekhez](disk-encryption-overview.md)|
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |
| TITKOSÍTOTT API-hívások| Igen | HTTPS-en és TLS-en keresztül. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen |  | 

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
