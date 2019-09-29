---
title: Azure Storage-titkosítás a REST-adatokhoz | Microsoft Docs
description: Az Azure Storage védi az adatait úgy, hogy automatikusan titkosítja azt, mielőtt megőrzi a felhőben. Az Azure Storage-ban tárolt összes adatforgalom titkosítva van, és az 256 bites AES-titkosítást használó transzparens módon történik, és a FIPS 140-2-kompatibilis.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673050"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-titkosítás a REST-adatokhoz

Az Azure Storage automatikusan titkosítja az adatait a felhőben való megőrzés során. A titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az Azure Storage-ban tárolt adatai a 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis. Az Azure Storage-titkosítás hasonló a Windows rendszeren a BitLocker-titkosításhoz.

Az Azure Storage-titkosítás minden új és meglévő Storage-fiók esetében engedélyezve van, és nem tiltható le. Mivel az adatai alapértelmezés szerint biztonságosak, nem kell módosítania a kódot vagy az alkalmazásokat, hogy kihasználhassa az Azure Storage-titkosítás előnyeit. 

A Storage-fiókok titkosítása a teljesítmény szintjétől (standard vagy prémium) vagy az üzembe helyezési modelltől (Azure Resource Manager vagy klasszikus) függetlenül történik. Az összes Azure Storage-redundancia-beállítás támogatja a titkosítást, és a Storage-fiók összes példánya titkosítva van. Minden Azure Storage-erőforrás titkosítva van, beleértve a blobokat, a lemezeket, a fájlokat, a várólistákat és a táblákat. Az összes objektum metaadatai is titkosítva vannak.

A titkosítás nem befolyásolja az Azure Storage teljesítményét. Az Azure Storage-titkosításhoz nem jár további díj.

További információ az Azure Storage-titkosítás alapjául szolgáló titkosítási modulokról: [Cryptography API: Következő generációs @ no__t-0.

## <a name="key-management"></a>Kulcskezelés

Használhatja a Microsoft által felügyelt kulcsokat a Storage-fiók titkosításához, vagy kezelheti a titkosítást a saját kulcsaival, valamint a Azure Key Vaultekkel együtt.

### <a name="microsoft-managed-keys"></a>Microsoft által felügyelt kulcsok

Alapértelmezés szerint a Storage-fiók a Microsoft által felügyelt titkosítási kulcsokat használja. A Storage-fiók titkosítási beállításait a [Azure Portal](https://portal.azure.com) **titkosítási** szakaszában tekintheti meg, ahogy az az alábbi képen is látható.

![Microsoft által felügyelt kulcsokkal titkosított fiók megtekintése](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Ügyfél által felügyelt kulcsok

Az Azure Storage-titkosítást az ügyfél által felügyelt kulcsokkal kezelheti. Az ügyfél által felügyelt kulcsok nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja. 

Használja a Azure Key Vault a kulcsok kezeléséhez és a kulcshasználat naplózásához. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)

Az ügyfél által felügyelt kulcsokhoz való hozzáférés visszavonásához lásd: [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) és [Azure Key Vault parancssori](https://docs.microsoft.com/cli/azure/keyvault)felület. A hozzáférés visszavonása hatékonyan blokkolja a Storage-fiókban lévő összes adattal való hozzáférést, mivel a titkosítási kulcs nem érhető el az Azure Storage-ban.

Az ügyfél által felügyelt kulcsok Azure Storage-ban való használatáról a következő cikkekből tájékozódhat:

- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához az Azure Portalon](storage-encryption-keys-portal.md)
- [Felhasználó által kezelt kulcsok konfigurálása az Azure Storage titkosításához a PowerShellből](storage-encryption-keys-powershell.md)
- [Ügyfél által felügyelt kulcsok használata Azure Storage-titkosítással az Azure CLI-vel](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Ha az egyik Azure AD-címtárból egy másikba helyez át egy előfizetést, a felügyelt identitások nem frissülnek, és előfordulhat, hogy az ügyfél által felügyelt kulcsok már nem működnek. További információ: **előfizetés továbbítása Azure ad-címtárak között** [Gyakori kérdések és ismert problémák az Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)kapcsolatban.  

> [!NOTE]  
> Az [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md)nem támogatja az ügyfél által felügyelt kulcsok használatát.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage-titkosítás és lemez titkosítása

Az Azure Storage encryption szolgáltatással az összes Azure Storage-fiók és a bennük található erőforrások titkosítva vannak, beleértve az Azure-beli virtuális gépek lemezeit tároló blobokat is. Az Azure-beli virtuális gépek lemezei [Azure Disk Encryptionkal](../../security/azure-security-disk-encryption-overview.md)is titkosíthatók. Azure Disk Encryption az iparági szabványnak megfelelő [BitLockert](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) használ a Windowson és a [dm-crypt-](https://en.wikipedia.org/wiki/Dm-crypt) on Linuxon, hogy az operációs rendszer-alapú titkosítási megoldásokat biztosítson a Azure Key Vaulthoz.

## <a name="next-steps"></a>További lépések

- [Mi az Azure Key Vault?](../../key-vault/key-vault-overview.md)
