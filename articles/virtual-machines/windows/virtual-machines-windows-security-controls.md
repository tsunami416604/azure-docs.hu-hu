---
title: Az Azure Windows virtuális gépek biztonsági vezérlői
description: Az Azure Windows virtuális gépek kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455342"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Biztonsági vezérlők a Windows virtuális gépekhez

Ez a cikk a Windows virtuális gépekbe beépített biztonsági vezérlőket dokumentálja.

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
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | [Windows-virtuális gépek figyelése és frissítése az Azure-ban.](tutorial-monitoring.md) |
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
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | Lásd: [Virtuális lemezek titkosítása Windows virtuális gépen.](/azure/virtual-machines/windows/disk-encryption-overview) |
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
