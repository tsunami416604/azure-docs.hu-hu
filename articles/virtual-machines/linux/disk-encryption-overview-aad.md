---
title: Az Azure Disk Encryption az Azure AD alkalmazás előfeltételeknek (előző kiadás)
description: Ez a cikk ismerteti a Microsoft Azure Disk Encryption használatával IaaS-beli virtuális gépek előfeltételei.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b0e3e683b2c103bc7f9b6812115e2e7a5d871034
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828679"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption az Azure AD-vel (előző kiadás)

**Az Azure Disk Encryption új kiadása így nem egy Azure AD-alkalmazás paramétert, amelyek biztosítják a virtuális gép lemez-titkosítás engedélyezéséhez. Az új kiadással, már nem kell az Azure AD hitelesítő adatok titkosítás engedélyezése lépés során. Valamennyi új virtuális gépeket titkosítani kell az Azure AD alkalmazás paramétereit az új kiadás használata nélkül. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos utasítások megtekintéséhez lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md). Az Azure AD-alkalmazás paraméterekkel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is fenn kell tartani az AAD-szintaxissal.**

Ez a cikk az Azure AD-vel (korábbi kiadással) kapcsolatos további követelményekkel és előAzure Disk Encryption feltételekkel [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md) nyújt kiegészítéseket.

Az ezekben a fejezetekben található információk a következők maradnak:

- [Támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépekre vonatkozó követelmények](disk-encryption-overview.md#additional-vm-requirements)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="networking-and-group-policy"></a>Hálózatkezelés és Csoportházirend

**Ahhoz, hogy az Azure Disk Encryption funkciót, a régebbi AAD használatával paraméter szintaxisa az IaaS virtuális gépek hálózati végpont a következő konfigurációs követelményeknek kell megfelelnie:** 
  - Szeretne csatlakozni a key vault jogkivonat beszerzéséhez, IaaS virtuális Gépen kell lennie csatlakozni egy Azure Active Directory végpontján \[login.microsoftonline.com\].
  - A titkosítási kulcsok a key vault ír, az IaaS virtuális gép tud csatlakozni a key vault-végpontot kell lennie.
  - Az IaaS virtuális gépek csatlakozni egy Azure storage-végpont, amelyen az Azure-bővítményt adattárat és a egy Azure storage-fiókot, amelyen a VHD-fájlok képesnek kell lennie.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információkért lásd: [Azure Key Vault tűzfal mögötti](../../key-vault/key-vault-access-behind-firewall.md).
  - A Windows Ha a TLS 1.0 explicit módon le van tiltva, és a .NET-verziója nem lett frissítve a 4.6-os vagy újabb, a következő beállításjegyzék-módosítás lehetővé teszi az újabb TLS-verzió kiválasztásához ADE:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Csoportházirend:**
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő Windows IaaS virtuális gépekhez használja. Tartományhoz csatlakozó virtuális gépek esetén nem leküldéses bármely csoportházirendek, amelyeket a TPM-védőt. "A BitLocker engedélyezése a kompatibilis TPM nélküli" a csoportházirenddel kapcsolatos információkért lásd: [a BitLocker csoportházirend-hivatkozás](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A tartományhoz csatlakoztatott, egyéni csoportházirendtel rendelkező virtuális gépekre vonatkozó BitLocker-házirendnek a következő beállítást kell tartalmaznia: [Konfigurálja a BitLocker helyreállítási adatok felhasználói tárolóját – > engedélyezze a 256 bites helyreállítási kulcsot](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítást, az új szabályzat alkalmazásának frissítése (a gpupdate.exe/Force) az új szabályzat kényszerítése és indítsa újra lehet szükség.  

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

Részletekért lásd: [kulcstartó létrehozása és konfigurálása az Azure Disk Encryptionhoz az Azure ad-vel (előző kiadás)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>További lépések

- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption engedélyezése az Azure AD-vel Linux rendszerű virtuális gépeken (korábbi kiadás)](disk-encryption-linux-aad.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)