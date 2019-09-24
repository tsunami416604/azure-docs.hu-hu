---
title: Az Azure Event Hubs biztonsági vezérlői
description: Az Azure-Event Hubs kiértékelésére szolgáló biztonsági ellenőrzési ellenőrzőlista
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 277a745d26961ed509258d5423fc3c0da9b79a24
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219406"
---
# <a name="security-controls-for-azure-event-hubs"></a>Az Azure Event Hubs biztonsági vezérlői

Ez a cikk az Azure Event Hubs beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Igen |  |  |
| VNet-befecskendezés támogatása| Nem | |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen |  |  |
| Kényszerített bújtatás támogatása| Nem |  |  |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |  |
| Adatsíkok naplózása és naplózása| Igen |   |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Authentication| Igen | | [Hozzáférés engedélyezése az Azure Event Hubshoz](authorize-access-event-hubs.md), hozzáférés [engedélyezése Event Hubs erőforrásokhoz Azure Active Directory használatával](authorize-access-azure-active-directory.md), hozzáférés [engedélyezése Event Hubs erőforrásokhoz a közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md) |
| Authorization|  Igen | | [Felügyelt identitások hitelesítése Azure Active Directoryekkel a Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md), [egy alkalmazás hitelesítése Azure Active Directory használatával a Event Hubs erőforrások](authenticate-application.md)eléréséhez, a [Event Hubs-erőforrásokhoz való hozzáférés hitelesítése közös hozzáférésű aláírások (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  Igen | |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem |  |  |
| Oszlop szintű titkosítás (Azure Data Services)| – | |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | |  |
| Titkosított API-hívások| Igen |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |  |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
