---
title: Az Azure Storage-titkosítás inaktív adatok |} A Microsoft Docs
description: Az Azure Storage automatikus titkosításával védi az adatokat, mielőtt megőrzése a felhőbe. Összes adatot az Azure Storage, a blobok, lemezek, fájlok, üzenetsorok vagy táblák, titkosított, és visszafejti a transzparens módon segítségével 256 bites AES-titkosítás, és megfelel a FIPS 140-2 szabványnak.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 211cfeb3aba29245e154f4a7db86fb4a3659c36f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730828"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Inaktív adatok az Azure Storage-titkosítás

Az Azure Storage automatikusan titkosítja az adatokat, amikor átlátni, a felhőbe. Titkosítás védi az adatokat, és annak érdekében, hogy a biztonsági és megfelelőségi követelmények kielégítésével. Az Azure Storage szolgáltatásban tárolt adatok titkosítva van, és visszafejti a transzparens módon segítségével 256 bites [AES-titkosítás](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)legerősebb az egyik rendelkezésre álló titkosítások és megfelel a FIPS 140-2 szabványnak. Az Azure Storage encryption hasonlít a Windows BitLocker-titkosítást.

Az Azure Storage-titkosítás engedélyezve van az összes meglévő és új storage-fiók, és nem tiltható le. Alapértelmezés szerint az adatok biztonságos, mert nem kell módosítani a kód vagy az alkalmazások az Azure Storage-titkosítás kihasználásához. Storage-fiókok vannak titkosítva, függetlenül azok teljesítményszint (standard vagy prémium szintű) vagy az (Azure Resource Manager vagy klasszikus) üzemi modell. Az összes Azure Storage redundanciabeállításai támogatja a titkosítást, és a egy storage-fiók összes másolatát vannak titkosítva. Az összes Azure Storage-erőforrások titkosítva vannak, beleértve a blobok, lemezek, fájlokat, üzenetsorokat és táblákat.

Titkosítási nem befolyásolja az Azure Storage teljesítményét. Nincs az Azure Storage-titkosítás további költség nélkül.

Az alapul szolgáló Azure Storage encryption titkosítási modulokkal kapcsolatos további információkért lásd: [titkosítási API: Új generációs](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Kulcskezelés

Támaszkodhat a Microsoft által felügyelt kulcsokat a tárfiók a titkosításhoz, vagy a saját kulcsok Azure Key Vault együtt kezelheti a titkosítás.

### <a name="microsoft-managed-keys"></a>A Microsoft által kezelt kulcsok

Alapértelmezés szerint a storage-fiókot használja a Microsoft által felügyelt titkosítási kulcsokat. Megtekintheti a titkosítási beállítások a tárfiók a a **titkosítási** szakaszában a [az Azure portal](https://portal.azure.com), ahogy az alábbi képen látható.

![A Microsoft által felügyelt kulcsokkal titkosítja fiók megtekintése](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

Az Azure Storage-titkosítás ügyfél által felügyelt kulcsokkal is kezelheti. Felhasználó által kezelt kulcsokkal rugalmasabban hozhat létre, elforgatása, tiltsa le, és visszavonhatja a hozzáférés-vezérlést. Naplózhatja a titkosítási kulcsokat az adatok védelmét is. 

Az Azure Key Vault használatával a kulcsok kezelése és naplózása a kulcshasználat. A saját kulcsok létrehozása és a key vaultban tárolja őket, vagy az Azure Key Vault API-k segítségével kulcsok létrehozásához. A storage-fiók és a key vault ugyanabban a régióban kell lennie, de különböző előfizetésekhez is lehetnek. Azure Key Vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md).

Felhasználó által kezelt kulcsokkal való hozzáférés visszavonása, lásd: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [Azure Key Vault parancssori felület](https://docs.microsoft.com/cli/azure/keyvault). Hatékony hozzáférés visszavonása letiltja a hozzáférést a storage-fiókban lévő összes adatot, mivel a titkosítási kulcs nem érhető el az Azure Storage által.

Felhasználó által kezelt kulcsok használata az Azure Storage, lásd: egyet az alábbi cikkek:

- [Ügyfél által felügyelt kulcsok az Azure Portalról az Azure Storage-titkosítás konfigurálása](storage-encryption-keys-portal.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosítás a PowerShell konfigurálása](storage-encryption-keys-powershell.md)
- [Felhasználó által kezelt kulcsok használata az Azure Storage-titkosítás az Azure CLI-vel](storage-encryption-keys-cli.md)

> [!NOTE]  
> Felhasználó által kezelt kulcsok használata nem támogatott a [Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Az Azure Storage-titkosítás és a lemeztitkosítás

Az Azure Storage-titkosítás az összes Azure Storage-fiókok és az erőforrások vannak titkosítva, többek között a lapblobokat, amelyek az Azure virtuális gépek lemezeinek biztonsági. Emellett az Azure virtuális gépek lemezei a lehet titkosított [az Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Az Azure Disk Encryption iparági szabványt használó [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) a Windows és [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) Linux operációsrendszer-alapú titkosítás megoldások, amelyek integrálhatók az Azure Key Vaultban.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
