---
title: Az Azure Event Hubs biztonsági vezérlői
description: Ez a cikk az Azure-Event Hubs (hálózat, identitás, adatvédelem stb.) kiértékelésére szolgáló biztonsági ellenőrzésekkel kapcsolatos feladatlistát tartalmaz.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: da20778f1e24372e445d635e675df6484905f195
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315398"
---
# <a name="security-controls-for-azure-event-hubs"></a>Az Azure Event Hubs biztonsági vezérlői

Ez a cikk az Azure Event Hubs beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Yes |  |  |
| VNet-befecskendezés támogatása| No | |  |
| Hálózati elkülönítés és tűzfalak támogatása| Yes |  |  |
| Kényszerített bújtatás támogatása| No |  |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció |
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Yes | |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes |  |  |
| Adatsíkok naplózása és naplózása| Yes |   |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Yes | | [Hozzáférés engedélyezése az Azure Event Hubshoz](authorize-access-event-hubs.md), hozzáférés [engedélyezése Event Hubs erőforrásokhoz Azure Active Directory használatával](authorize-access-azure-active-directory.md), hozzáférés [engedélyezése Event Hubs erőforrásokhoz a közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md) |
| Engedélyezés|  Yes | | [Felügyelt identitások hitelesítése Azure Active Directoryekkel a Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md), [egy alkalmazás hitelesítése Azure Active Directory használatával a Event Hubs erőforrások eléréséhez](authenticate-application.md), a [megosztott hozzáférési aláírások (SAS) használatával Event Hubs erőforrásokhoz való hozzáférés hitelesítése](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok |  Yes | |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen. Dedikált fürtökhöz érhető el. | Az Azure kulcstartóban lévő ügyfél által felügyelt kulcs használatával titkosíthatja az adatok egy nyugalmi állapotban lévő alközpontban. | [Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs-adatok inaktív titkosításához a Azure Portal használatával](configure-customer-managed-key.md) |
| Oszlop szintű titkosítás (Azure Data Services)| N.A. | |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Yes | |  |
| Titkosított API-hívások| Yes |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció |
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen | |  |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
