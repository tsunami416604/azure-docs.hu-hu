---
title: Az Azure Event Hubs biztonsági vezérlői
description: Ez a cikk az Azure Event Hubs (hálózat, identitás, adatvédelem stb.) kiértékelésére szolgáló biztonsági vezérlők ellenőrzőlistáját tartalmazza.
services: event-hubs
ms.service: event-hubs
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: 0769e88eb72b5b347dd9ebf4b1634501ca54098e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309506"
---
# <a name="security-controls-for-azure-event-hubs"></a>Az Azure Event Hubs biztonsági vezérlői

Ez a cikk az Azure Event Hubs beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| A szolgáltatás végpontjának támogatása| Igen |  |  |
| A VNet injekciózás támogatása| Nem | |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen |  |  |
| Kényszerített bújtatástámogatása| Nem |  |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | |  |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen |  |  |
| Adatsík naplózása és naplózása| Igen |   |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Hitelesítés| Igen | | [Hozzáférés engedélyezése az Azure Event Hubs hoz,](authorize-access-event-hubs.md) [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával,](authorize-access-azure-active-directory.md)Hozzáférés engedélyezése az Event [Hubs-erőforrásokhoz megosztott hozzáférés-aláírásokkal](authorize-access-shared-access-signature.md) |
| Engedélyezés|  Igen | | [Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs Resources eléréséhez,](authenticate-managed-identity.md)alkalmazás hitelesítése az [Azure Active Directoryval az Event Hubs-erőforrások eléréséhez,](authenticate-application.md) [Hozzáférés hitelesítése az Event Hubs-erőforrásokhoz megosztott hozzáférés-aláírásokkal (SAS)](authenticate-shared-access-signature.md) |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció |
|---|---|--|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok |  Igen | |  |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Igen. Dedikált fürtökhöz érhető el. | Az Azure KeyVault ügyfél által felügyelt kulcs segítségével titkosíthatja az adatokat egy inaktív eseményközpontban. | [Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs inaktív adatainak titkosításához az Azure Portal használatával](configure-customer-managed-key.md) |
| Oszlopszintű titkosítás (Azure Data Services)| N/A | |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Igen | |  |
| TITKOSÍTOTT API-hívások| Igen |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| Dokumentáció |
|---|---|--|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen | |  |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
