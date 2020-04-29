---
title: Az Azure Security Control – Adathelyreállítás
description: Az Azure Security Control Adathelyreállítás
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408596"
---
# <a name="security-control-data-recovery"></a>Biztonsági ellenőrzés: Adathelyreállítás

Győződjön meg arról, hogy a rendszeradatok, a konfigurációk és a titkos kulcsok automatikus biztonsági mentése rendszeresen történik.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 9,1 | 10.1 | Ügyfél |

Engedélyezze Azure Backup és konfigurálja a biztonsági mentési forrást (Azure-beli virtuális gépek, SQL Server vagy fájlmegosztás), valamint a kívánt gyakoriságot és megőrzési időt.

- [A Azure Backup engedélyezése](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 9,2 | 10.2 | Ügyfél |

Engedélyezze Azure Backup és a célként megadott virtuális gép (ek) et, valamint a kívánt gyakoriságot és megőrzési időt. Ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vaulton belül.

- [A Azure Backup engedélyezése](https://docs.microsoft.com/azure/backup/)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 9,3 | 10,3 | Ügyfél |

Győződjön meg arról, hogy a Azure Backupon belül rendszeresen végezhető el az adatok visszaállítása a tartalomban. A biztonsági másolatok által felügyelt kulcsok visszaállításának tesztelése.

- [Fájlok helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Key Vault-kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

| Azure-azonosító | CIS-azonosítók | Felelősséget |
|--|--|--|
| 9.4 verzió | 10,4 | Ügyfél |

Helyszíni biztonsági mentéshez az inaktív állapotú adatokat védő titkosítás használható az Azure-ra irányuló biztonsági mentés során megadott jelszóval. Azure-beli virtuális gépek esetén a rendszer Storage Service Encryption (SSE) használatával titkosítja az inaktív adatokat. Szerepköralapú hozzáférés-vezérlés használatával biztosíthatja a biztonsági mentéseket és az ügyfelek által felügyelt kulcsokat.  

A kulcsok véletlen vagy rosszindulatú Törlés elleni védelme érdekében engedélyezze a Key Vault a védelem törlését és kiürítését.  Ha az Azure Storage-t használja a biztonsági másolatok tárolásához, a Soft delete lehetővé teszi az adatok mentését és helyreállítását a Blobok vagy blob-Pillanatképek törlésekor. 

- [Az Azure RBAC ismertetése](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [A helyreállítható törlés és a védelem engedélyezése a Key Vaultban](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Az Azure Storage-blobok helyreállítható törlése](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>További lépések

- Tekintse meg a következő biztonsági vezérlőt: [incidens válasza](security-control-incident-response.md)