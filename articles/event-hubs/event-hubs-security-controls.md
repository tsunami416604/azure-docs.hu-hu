---
title: Az Azure Event Hubs biztonsági vezérlői
description: Ez a cikk az Azure-Event Hubs (hálózat, identitás, adatvédelem stb.) kiértékelésére szolgáló biztonsági ellenőrzésekkel kapcsolatos feladatlistát tartalmaz.
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309506"
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

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen |  |  |
| Adatsíkok naplózása és naplózása| Igen |   |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | | [Hozzáférés engedélyezése az Azure Event Hubshoz](authorize-access-event-hubs.md), hozzáférés [engedélyezése Event Hubs erőforrásokhoz Azure Active Directory használatával](authorize-access-azure-active-directory.md), hozzáférés [engedélyezése Event Hubs erőforrásokhoz a közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md) |
| Engedélyezés|  Igen | | [Felügyelt identitások hitelesítése Azure Active Directoryekkel a Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md), [egy alkalmazás hitelesítése Azure Active Directory használatával a Event Hubs erőforrások eléréséhez](authenticate-application.md), a [megosztott hozzáférési aláírások (SAS) használatával Event Hubs erőforrásokhoz való hozzáférés hitelesítése](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  Igen | |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen. Dedikált fürtökhöz érhető el. | Az Azure kulcstartóban lévő ügyfél által felügyelt kulcs használatával titkosíthatja az adatok egy nyugalmi állapotban lévő alközpontban. | [Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs-adatok inaktív titkosításához a Azure Portal használatával](configure-customer-managed-key.md) |
| Oszlop szintű titkosítás (Azure Data Services)| N/A | |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Igen | |  |
| Titkosított API-hívások| Igen |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |  |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
