---
title: Biztonsági vezérlők
description: Tudnivalók a Azure Backup szolgáltatásban használt biztonsági ellenőrzésekről. Ezek a vezérlők segítik a szolgáltatást a biztonsági rések megelőzésében, észlelésében és megválaszolásában.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 7ff3ff5c1b024a228778b0214e67239d3c8ab721
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89418755"
---
# <a name="security-controls-for-azure-backup"></a>A Azure Backup biztonsági vezérlői

Ez a cikk a Azure Backup beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| No |  |  |
| VNet-befecskendezés támogatása| No |  |  |
| Hálózati elkülönítés és tűzfalak támogatása| Yes | |  |
| Az Azure-beli virtuális gépek kényszerített bújtatási támogatása | Yes  |  |  |
| Az Azure-beli virtuális gépeken futó alkalmazások kényszerített bújtatási támogatása| No  |  |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció
|---|---|--|--|
| Azure monitoring-támogatás (például log Analytics, alkalmazás-elemzések)| Yes | A Log Analytics erőforrás-naplókon keresztül támogatott. További információ: [Azure Backup védett munkaterhelések figyelése log Analytics használatával](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Yes | A Azure Portal összes ügyfél által aktivált műveletét a rendszer naplózza a tevékenység naplófájljaiba. |  |
| Adatsíkok naplózása és naplózása| No | Azure Backup az adatsík nem érhető el közvetlenül.  |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció
|---|---|--|--|
| Hitelesítés| Yes | A hitelesítés Azure Active Directoryon keresztül történik. |  |
| Engedélyezés| Yes | A rendszer felhasználja az ügyfél által létrehozott és az Azure beépített szerepköreit. További információ: [szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Yes | A Storage szolgáltatás titkosítása a Storage-fiókokhoz. |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | No |  |  |
| Oszlop szintű titkosítás (Azure Data Services)| No |  |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| No | HTTPS használatával. |  |
| Titkosított API-hívások| Yes |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció
|---|---|--|--|
| Configuration Management-támogatás (a konfiguráció verziószámozása stb.)| Igen|  |  |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
