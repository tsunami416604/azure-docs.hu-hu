---
title: Azure Security Control – Adat-helyreállítás
description: Biztonsági ellenőrzés adathelyreállítása
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934496"
---
# <a name="security-control-data-recovery"></a>Biztonsági ellenőrzés: Adat-helyreállítás

Győződjön meg arról, hogy minden rendszeradatról, konfigurációról és titkos kulcsokról rendszeresen automatikusan biztonsági másolatot kell kapnia.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 9.1 | 10.1 | Ügyfél |

Engedélyezze az Azure Backup biztonsági mentést, és konfigurálja a biztonsági mentési forrást (Azure virtuális gépek, SQL Server vagy fájlmegosztások), valamint a kívánt gyakoriságot és megőrzési időszakot.

Az Azure Backup engedélyezése:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 9.2 | 10.2 | Ügyfél |

Engedélyezze az Azure Backup és a cél virtuális gép(ek), valamint a kívánt gyakorisági és megőrzési időszakok. Az azure-beli key vaultban lévő ügyfél által kezelt kulcsok biztonsági mentése.

Az Azure Backup engedélyezése:

https://docs.microsoft.com/azure/backup/

A kulcstároló kulcsainak biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 9.3 | 10.3 | Ügyfél |

Győződjön meg arról, hogy rendszeresidőközönként végre adatok visszaállítása a tartalom az Azure Backup. Ha szükséges, tesztelje a visszaállítást egy elkülönített VLAN-ra. Az ügyfél által felügyelt kulcsok biztonsági másolatot készülő kulcsainak visszaállítása.

Fájlok helyreállítása az Azure virtuális gép biztonsági mentéséből:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

A kulcstároló kulcsainak visszaállítása az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 9.4 verzió | 10.4 | Ügyfél |

Helyszíni biztonsági mentéshez az inaktív állapotú adatokat védő titkosítás használható az Azure-ra irányuló biztonsági mentés során megadott jelszóval. Azure-beli virtuális gépek esetén a rendszer Storage Service Encryption (SSE) használatával titkosítja az inaktív adatokat. Engedélyezheti a Soft-Delete a Key Vault ban, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törlés.

A soft-delete engedélyezése a Key Vaultban:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>További lépések

Lásd a következő biztonsági vezérlőt: [Incidensválasz](security-control-incident-response.md)
