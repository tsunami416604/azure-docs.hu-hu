---
title: Biztonsági vezérlők
description: Ismerje meg az Azure Backup szolgáltatásban használt biztonsági vezérlőket. Ezek a vezérlők segítik a szolgáltatást a biztonsági rések megelőzésében, észlelésén és az azokra való reagálásban.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 0e3f5ce942ea8aef9bf5eb98883ae1e72a7ab239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172137"
---
# <a name="security-controls-for-azure-backup"></a>Biztonsági vezérlők az Azure Backup hoz

Ez a cikk az Azure Backup beépített biztonsági vezérlőket dokumentálja.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Hálózat)

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | Dokumentáció
|---|---|--|
| A szolgáltatás végpontjának támogatása| Nem |  |  |
| A VNet injekciózás támogatása| Nem |  |  |
| Hálózati elkülönítés és tűzfaltámogatás| Igen | Kényszerített bújtatás támogatott virtuális gépek biztonsági mentése. Kényszerített bújtatás nem támogatott a virtuális gépeken belül futó számítási feladatok. |  |
| Kényszerített bújtatástámogatása| Nem |  |  |

## <a name="monitoring--logging"></a>Naplózás & figyelése

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| | Dokumentáció
|---|---|--|
| Azure figyelési támogatás (Naplóelemzés, Alkalmazáselemzések stb.)| Igen | A Log Analytics az erőforrásnaplók on keresztül támogatott. További információ: [Monitor Azure Backup védett számítási feladatok a Log Analytics használatával.](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) |  |
| Vezérlő és felügyeleti sík naplózása és naplózása| Igen | Az Azure Portalon végrehajtott összes ügyfél által aktivált műveletet a rendszer naplózza a tevékenységnaplókba. |  |
| Adatsík naplózása és naplózása| Nem | Az Azure Backup adatsíkja nem érhető el közvetlenül.  |  |

## <a name="identity"></a>Identitás

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| | Dokumentáció
|---|---|--|
| Hitelesítés| Igen | A hitelesítés az Azure Active Directoryn keresztül történik. |  |
| Engedélyezés| Igen | Az ügyfél által létrehozott és a beépített RBAC-szerepkörök használatosak. További információt a [Szerepköralapú hozzáférés-vezérlés használata az Azure Backup helyreállítási pontok kezeléséhez](/azure/backup/backup-rbac-rs-vault)című témakörben talál. |  |

## <a name="data-protection"></a>Adatvédelem

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések | | Dokumentáció
|---|---|--|
| Kiszolgálóoldali titkosítás inaktív állapotban: Microsoft által felügyelt kulcsok | Igen | A tárolási szolgáltatás titkosításának használata tárfiókokhoz. |  |
| Kiszolgálóoldali titkosítás inaktív állapotban: ügyfél által felügyelt kulcsok (BYOK) | Nem |  |  |
| Oszlopszintű titkosítás (Azure Data Services)| Nem |  |  |
| Titkosítás átvitel közben (például ExpressRoute-titkosítás, virtuális hálózat-titkosítás és Virtuálishálózati titkosítás)| Nem | HTTPS használatával. |  |
| TITKOSÍTOTT API-hívások| Igen |  |  |

## <a name="configuration-management"></a>Konfigurációkezelés

| Biztonsági ellenőrzés | Igen/nem | Megjegyzések| | Dokumentáció
|---|---|--|
| Konfigurációkezelés támogatása (a konfiguráció verziószámozása stb.)| Igen|  |  |

## <a name="next-steps"></a>További lépések

- További információ az [Azure-szolgáltatások beépített biztonsági vezérlőiről.](../security/fundamentals/security-controls.md)
