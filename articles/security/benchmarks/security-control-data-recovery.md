---
title: Az Azure Security Control – Adathelyreállítás
description: Biztonsági vezérlő adathelyreállítása
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934496"
---
# <a name="security-control-data-recovery"></a>Biztonsági ellenőrzés: Adathelyreállítás

Győződjön meg arról, hogy a rendszeradatok, a konfigurációk és a titkos kulcsok automatikus biztonsági mentése rendszeresen történik.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 9,1 | 10.1 | Ügyfél |

Engedélyezze Azure Backup és konfigurálja a biztonsági mentési forrást (Azure-beli virtuális gépek, SQL Server vagy fájlmegosztás), valamint a kívánt gyakoriságot és megőrzési időt.

A Azure Backup engedélyezése:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 9,2 | 10.2 | Ügyfél |

Engedélyezze Azure Backup és a célként megadott virtuális gép (ek) et, valamint a kívánt gyakoriságot és megőrzési időt. Ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

A Azure Backup engedélyezése:

https://docs.microsoft.com/azure/backup/

Key Vault-kulcsok biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 9,3 | 10,3 | Ügyfél |

Győződjön meg arról, hogy a Azure Backupon belül rendszeresen végezhető el az adatok visszaállítása a tartalomban. Ha szükséges, tesztelje a visszaállítást egy elkülönített VLAN-ra. A biztonsági másolatok által felügyelt kulcsok visszaállításának tesztelése.

Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Key Vault-kulcsok visszaállítása az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 9.4 | 10,4 | Ügyfél |

Helyszíni biztonsági mentéshez az inaktív állapotú adatokat védő titkosítás használható az Azure-ra irányuló biztonsági mentés során megadott jelszóval. Azure-beli virtuális gépek esetén a rendszer Storage Service Encryption (SSE) használatával titkosítja az inaktív adatokat. A kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezheti a Key Vault törlését.

A Soft delete engedélyezése a Key Vaultban:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő biztonsági vezérlőt: [incidens válasza](security-control-incident-response.md)
