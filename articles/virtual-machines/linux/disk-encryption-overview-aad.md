---
title: Azure Disk Encryption az Azure AD-alkalmazás előfeltételeivel (előző kiadás)
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970581"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption az Azure AD-vel (előző kiadás)

A Azure Disk Encryption új kiadása szükségtelenné teszi a Azure Active Directory (Azure AD) alkalmazás paraméterének megadását a VM-lemezek titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos útmutatásért lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát

Ez a cikk kiegészíti az Azure AD-vel (korábbi kiadással) Azure Disk Encryptionekhez szükséges további követelményekkel és előfeltételekkel [Azure Disk Encryption linuxos virtuális gépekhez](disk-encryption-overview.md) .

Az ezekben a fejezetekben található információk a következők maradnak:

- [Támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépekre vonatkozó követelmények](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Hálózatkezelés és Csoportházirend

Ha engedélyezni szeretné a Azure Disk Encryption funkciót a régebbi HRE paraméter szintaxisával, az infrastruktúra (IaaS) virtuális gépeknek meg kell felelniük a következő hálózati végpontok konfigurációs követelményeinek: 
  - Ahhoz, hogy jogkivonatot kapjon a kulcstartóhoz való kapcsolódáshoz, a IaaS virtuális gépnek képesnek kell lennie csatlakozni egy Azure AD-végponthoz, \[login.microsoftonline.com\].
  - A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  - Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.
  -  Ha a biztonsági házirend korlátozza az Azure-beli virtuális gépekről az internetre való hozzáférést, az előző URI-t megoldhatja, és konfigurálhat egy adott szabályt, hogy engedélyezze a kimenő kapcsolatot az IP-címekkel. További információ: [Azure Key Vault tűzfal mögött](../../key-vault/key-vault-access-behind-firewall.md).
  - Windows rendszeren, ha a TLS 1,0 explicit módon le van tiltva, és a .NET verziója nem frissül 4,6 vagy újabb verzióra, a következő beállításjegyzék-módosítás lehetővé teszi, hogy Azure Disk Encryption válassza ki a legújabb TLS-verziót:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Csoportházirend
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. A tartományhoz csatlakoztatott virtuális gépek esetében ne küldjön le olyan csoportházirendeket, amelyek kikényszerítik a TPM-védőket. További információ a **BitLocker kompatibilis TPM nélküli engedélyezése**beállításának Csoportházirendáról: a [BitLocker csoportházirend referenciája](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Az egyéni Csoportházirend rendelkező, tartományhoz csatlakoztatott virtuális gépeken a BitLocker-házirendnek tartalmaznia kell a következő beállítást: a [BitLocker helyreállítási adatok felhasználói tárolójának konfigurálása – > engedélyezi a 256 bites helyreállítási kulcsot](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption sikertelen, ha a BitLocker egyéni Csoportházirend beállításai nem kompatibilisek. A megfelelő házirend-beállítással nem rendelkező gépeken alkalmazza az új házirendet, kényszerítse az új házirend frissítését (gpupdate. exe/Force), majd indítsa újra, ha szükséges. 

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei 

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

További információ: [Key Vault létrehozása és konfigurálása Azure Disk Encryption az Azure ad-vel (korábbi kiadás)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Következő lépések

- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption engedélyezése az Azure AD-vel Linux rendszerű virtuális gépeken (korábbi kiadás)](disk-encryption-linux-aad.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)