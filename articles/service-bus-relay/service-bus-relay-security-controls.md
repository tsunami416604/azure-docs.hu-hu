---
title: Az Azure Service Bus Relay biztonsági vezérlői
description: Ez a cikk az Azure Service Bus Relay kiértékelésére szolgáló beépített biztonsági vezérlők ellenőrzőlistáját tartalmazza.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 28d3ba14aa7769ac4f3fc22bd2b5bd7acd30557c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514017"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Az Azure Service Bus Relay biztonsági vezérlői

Ez a cikk az Azure Service Bus Relay beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| A szolgáltatás végpontjának támogatása| Nem |  |   |
| Hálózati elkülönítés és tűzfaltámogatás| Nem |  |   |
| Kényszerített bújtatástámogatása| N/A | Relé a TLS alagút  |   |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | |   |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Az [Azure Resource Manager](../azure-resource-manager/index.yml)segítségével. |   |
| Adatsík naplózása és naplózása| Igen | Kapcsolat sikeres / hiba és a hibák és a naplózott.  |   |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | Keresztül SAS. | [Azure Relay hitelesítése és engedélyezése](relay-authentication-and-authorization.md) |
| Engedélyezés|  Igen | Keresztül SAS. | [Azure Relay hitelesítése és engedélyezése](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok |  N/A | A továbbítás egy webes szoftvercsatorna, és nem tart meg adatokat. |   |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Csak Microsoft TLS-certeket használ.  |   |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |   |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | A szolgáltatás hoz tls-t igényel. |   |
| TITKOSÍTOTT API-hívások| Igen | HTTPS-hez. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | Az [Azure Resource Manager](../azure-resource-manager/index.yml)segítségével.|   |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)