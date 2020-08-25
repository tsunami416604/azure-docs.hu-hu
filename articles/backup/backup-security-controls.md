---
title: Biztonsági vezérlők
description: Tudnivalók a Azure Backup szolgáltatásban használt biztonsági ellenőrzésekről. Ezek a vezérlők segítik a szolgáltatást a biztonsági rések megelőzésében, észlelésében és megválaszolásában.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: e69053d4646207b34e8df31b949410994c967303
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827290"
---
# <a name="security-controls-for-azure-backup"></a>A Azure Backup biztonsági vezérlői

Ez a cikk a Azure Backup beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|--|
| Szolgáltatás végpontjának támogatása| Nem |  |  |
| VNet-befecskendezés támogatása| Nem |  |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A virtuális gépek biztonsági mentése a kényszerített bújtatást támogatja. A kényszerített bújtatás nem támogatott a virtuális gépeken belül futó munkaterhelések esetén. |  |
| Kényszerített bújtatás támogatása| Nem |  |  |

## <a name="monitoring--logging"></a>& naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció
|---|---|--|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A Log Analytics erőforrás-naplókon keresztül támogatott. További információ: [Azure Backup védett munkaterhelések figyelése log Analytics használatával](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Azure Portal összes ügyfél által aktivált műveletét a rendszer naplózza a tevékenység naplófájljaiba. |  |
| Adatsíkok naplózása és naplózása| Nem | Azure Backup az adatsík nem érhető el közvetlenül.  |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció
|---|---|--|--|
| Hitelesítés| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |  |
| Engedélyezés| Igen | A rendszer felhasználja az ügyfél által létrehozott és az Azure beépített szerepköreit. További információ: [szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](./backup-rbac-rs-vault.md). |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek | Dokumentáció
|---|---|--|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A Storage szolgáltatás titkosítása a Storage-fiókokhoz. |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem |  |  |
| Oszlop szintű titkosítás (Azure Data Services)| Nem |  |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Nem | HTTPS használatával. |  |
| Titkosított API-hívások| Igen |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Jegyzetek| Dokumentáció
|---|---|--|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen|  |  |

## <a name="next-steps"></a>Következő lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
