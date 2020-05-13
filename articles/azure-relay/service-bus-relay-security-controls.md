---
title: A Azure Relay biztonsági vezérlői
description: Ez a cikk a Azure Relay kiértékelésére szolgáló beépített biztonsági ellenőrzésekkel kapcsolatos feladatlistát tartalmaz.
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: f8165d994e998af4f15cd6aa2fd08b75191b8b64
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211460"
---
# <a name="security-controls-for-azure-relay"></a>A Azure Relay biztonsági vezérlői

Ez a cikk a Azure Relay beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Nem |  |   |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |   |
| Kényszerített bújtatás támogatása| N/A | A Relay a TLS-alagút  |   |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |   |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |   |
| Adatsíkok naplózása és naplózása| Igen | A sikeres és sikertelen kapcsolatok, valamint a naplózott hibák.  |   |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | SAS-n keresztül. | [Hitelesítés és engedélyezés Azure Relay](relay-authentication-and-authorization.md) |
| Engedélyezés|  Igen | SAS-n keresztül. | [Hitelesítés és engedélyezés Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  N/A | A Relay egy webes szoftvercsatorna, és nem őrzi meg az adatmegőrzési időt. |   |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Csak a Microsoft TLS-tanúsítványokat használja.  |   |
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |   |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A szolgáltatáshoz TLS szükséges. |   |
| Titkosított API-hívások| Igen | HTTPS. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül.|   |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).