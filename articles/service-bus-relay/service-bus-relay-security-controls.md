---
title: Azure Service Bus Relay biztonsági vezérlői
description: A Azure Service Bus Relay kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistája
services: service-bus-relay
ms.service: service-bus-relay
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 7f4e2a31673905a7e28d1dbb5520650aefc6f368
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219982"
---
# <a name="security-controls-for-azure-service-bus-relay"></a>Azure Service Bus Relay biztonsági vezérlői

Ez a cikk a Azure Service Bus Relaybe beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Nem |  |   |
| Hálózati elkülönítés és tűzfalak támogatása| Nem |  |   |
| Kényszerített bújtatás támogatása| – | A Relay a TLS-alagút  |   |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |   |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül. |   |
| Adatsíkok naplózása és naplózása| Igen | A sikeres és sikertelen kapcsolatok, valamint a naplózott hibák.  |   |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Authentication| Igen | SAS-n keresztül. | [Hitelesítés és engedélyezés Azure Relay](relay-authentication-and-authorization.md) |
| Authorization|  Igen | SAS-n keresztül. | [Hitelesítés és engedélyezés Azure Relay](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  – | A Relay egy webes szoftvercsatorna, és nem őrzi meg az adatmegőrzési időt. |   |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem | Csak a Microsoft TLS-tanúsítványokat használja.  |   |
| Oszlop szintű titkosítás (Azure Data Services)| – | |   |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | A szolgáltatáshoz TLS szükséges. |   |
| Titkosított API-hívások| Igen | HTTPS. |


## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | [Azure Resource Manageron](../azure-resource-manager/index.yml)keresztül.|   |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).