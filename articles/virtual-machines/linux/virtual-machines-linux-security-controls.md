---
title: Biztonsági vezérlők Az Azure Linux virtuális gépekhez – Linux
description: Az Azure Linux virtuális gépek kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190570"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Linux virtuális gépek biztonsági vezérlői

Ez a cikk a Linux virtuális gépekbe beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

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
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | Lásd: [Linuxos virtuális gép figyelése és frissítése az Azure-ban.](/azure/virtual-machines/linux/tutorial-monitoring) |
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
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | Lásd: [Azure Disk Encryption for Linux virtuális gépek.](disk-encryption-overview.md) |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat titkosítása és Virtuálishálózati titkosítás)| Igen | Az Azure virtuális gépek támogatja az [ExpressRoute](/azure/expressroute) és a Virtuális hálózat titkosítását. Lásd: [Bejövő titkosítás a virtuális gépeken.](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms) |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen | Az ügyfél által felügyelt kulcsok egy támogatott Azure titkosítási forgatókönyv; lásd: [Azure-titkosítás – áttekintés.](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)|
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |
| TITKOSÍTOTT API-hívások| Igen | HTTPS-en és TLS-en keresztül. |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések|
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen |  | 

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../../security/fundamentals/security-controls.md)
