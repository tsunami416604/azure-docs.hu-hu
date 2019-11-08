---
title: A Azure Backup biztonsági vezérlői
description: Tudnivalók a Azure Backup szolgáltatásban használt biztonsági ellenőrzésekről. Ezek a vezérlők segítik a szolgáltatást a biztonsági rések megelőzésében, észlelésében és megválaszolásában.
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: 9d7bb2e9ea2d2cf9cce6c4f6d3b4f3346e7ce363
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747147"
---
# <a name="security-controls-for-azure-backup"></a>A Azure Backup biztonsági vezérlői

Ez a cikk a Azure Backup beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| Szolgáltatás végpontjának támogatása| Nem |  |  |
| VNet-befecskendezés támogatása| Nem |  |  |
| Hálózati elkülönítés és tűzfalak támogatása| Igen | A virtuális gépek biztonsági mentése a kényszerített bújtatást támogatja. A kényszerített bújtatás nem támogatott a virtuális gépeken belül futó munkaterhelések esetén. |  |
| Kényszerített bújtatás támogatása| Nem |  |  |

## <a name="monitoring--logging"></a>& Naplózás figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| | Dokumentáció
|---|---|--|
| Azure monitoring-támogatás (log Analytics, alkalmazás-elemzések stb.)| Igen | A Log Analytics erőforrás-naplókon keresztül támogatott. További információ: [Azure Backup védett munkaterhelések figyelése log Analytics használatával](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/). |  |
| Vezérlési és felügyeleti síkok naplózása és naplózása| Igen | A Azure Portal összes ügyfél által aktivált műveletét a rendszer naplózza a tevékenység naplófájljaiba. |  |
| Adatsíkok naplózása és naplózása| Nem | Azure Backup az adatsík nem érhető el közvetlenül.  |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| | Dokumentáció
|---|---|--|
| Authentication| Igen | A hitelesítés Azure Active Directoryon keresztül történik. |  |
| Engedélyezés| Igen | A rendszer felhasználja az ügyfél által létrehozott és beépített RBAC-szerepköröket. További információ: [szerepköralapú Access Control használata Azure Backup helyreállítási pontok kezeléséhez](/azure/backup/backup-rbac-rs-vault). |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | | Dokumentáció
|---|---|--|
| Kiszolgálóoldali titkosítás nyugalmi állapotban: Microsoft által felügyelt kulcsok | Igen | A Storage szolgáltatás titkosítása a Storage-fiókokhoz. |  |
| Kiszolgálóoldali titkosítás nyugalmi állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem |  |  |
| Oszlop szintű titkosítás (Azure Data Services)| Nem |  |  |
| Az átvitel közbeni titkosítás (például ExpressRoute titkosítás, VNet titkosítás és VNet-VNet titkosítás)| Nem | HTTPS használatával. |  |
| Titkosított API-hívások| Igen |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| | Dokumentáció
|---|---|--|
| Configuration Management-támogatás (konfiguráció verziószámozása stb.)| Igen|  |  |

## <a name="next-steps"></a>További lépések

- További információ a [beépített biztonsági vezérlőkről az Azure-szolgáltatások között](../security/fundamentals/security-controls.md).
