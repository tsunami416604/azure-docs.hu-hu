---
title: Azure lemeztitkosítás az Azure AD-vel (előző kiadás)
description: Ez a cikk előfeltételei a Microsoft Azure lemeztitkosítás iaaS virtuális gépek használatának előfeltételeit.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: cb9d697c11427c7ebbf811f9cc05740347c74452
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417554"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure lemeztitkosítás az Azure AD-vel (előző kiadás)

**Az Azure Disk Encryption új kiadása kiküszöböli az Azure AD-alkalmazás paraméter biztosításának követelményét a virtuális gép lemeztitkosításának engedélyezéséhez. Az új kiadás, már nem kell megadnia az Azure AD hitelesítő adatait a titkosítás engedélyezése a titkosítási lépés során. Minden új virtuális gép titkosítására az Azure AD alkalmazás paraméterei az új kiadás használatával. A virtuális gép lemeztitkosításának az új kiadással történő engedélyezésére vonatkozó utasítások megtekintéséhez olvassa el az [Azure Disk Encryption for Windows virtuális gépek című témakört.](disk-encryption-overview.md) Az Azure AD-alkalmazás paramétereivel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is karban kell tartani az AAD szintaxissal.**

Ez a cikk kiegészíti az [Azure Disk Encryption for Windows virtuális gépek](disk-encryption-overview.md) további követelmények és előfeltételek az Azure Disk Encryption az Azure AD (előző kiadás). A [támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems) szakasz ugyanaz marad.

## <a name="networking-and-group-policy"></a>Hálózat- és csoportházirend

**Az Azure Lemeztitkosítás szolgáltatás régebbi AAD paraméterszintaxissal történő engedélyezéséhez az IaaS virtuális gépeknek meg kell felelniük a következő hálózati végpontkonfigurációs követelményeknek:** 
  - A kulcstartóhoz való csatlakozáshoz az IaaS virtuális gépnek képesnek kell lennie egy \[\]Azure Active Directory-végponthoz , login.microsoftonline.com.
  - A titkosítási kulcsok at a key vault, az IaaS virtuális gép képesnek kell lennie a key vault végponthoz való csatlakozáshoz.
  - Az IaaS virtuális gépnek képesnek kell lennie egy Azure-tárolóvégponthoz csatlakozni, amely az Azure-bővítménytárházat és a VHD-fájlokat tároló Azure-tárfiókot üzemelteti.
  -  Ha a biztonsági szabályzat korlátozza a hozzáférést az Azure virtuális gépek az internetre, feloldhatja az előző URI-t, és konfigurálhat egy adott szabályt, hogy lehetővé tegye a kimenő kapcsolatot az IP-k. További információ: [Azure Key Vault tűzfal mögött.](../../key-vault/key-vault-access-behind-firewall.md)
  - A titkosítandó virtuális gépnek úgy kell konfigurálnia, hogy a TLS 1.2 protokollt használja alapértelmezett protokollként. Ha a TLS 1.0 explicit módon le van tiltva, és a .NET verzió nem lett 4.6-os vagy újabb verzióra frissítve, a rendszerleíró adatbázis következő módosítása lehetővé teszi, hogy az ADE kiválassza a legújabb TLS-verziót:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Csoportházirend:**
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő a Windows IaaS virtuális gépek. Tartományhoz csatlakozó virtuális gépek esetén ne tessen le olyan csoportházirendeket, amelyek tpm-védőket kényszerítenek ki. A "BitLocker engedélyezése kompatibilis TPM nélkül" csoportházirendről a [BitLocker csoportházirend-útmutatójában talál.](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)

-  Az egyéni csoportházirenddel rendelkező tartományhoz csatlakozó virtuális gépeken lévő BitLocker-házirendnek a következő beállítást kell [tartalmaznia: A BitLocker helyreállítási információinak konfigurálása -> 256 bites helyreállítási kulcs engedélyezése](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Az Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Azokon a gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítással, alkalmazza az új házirendet, kényszerítse az új házirendfrissítését (gpupdate.exe /force), majd újraindításra lehet szükség.  

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Az Azure Disk Encryption használatához egy Azure Key Vault szükséges a lemeztitkosítási kulcsok és titkos kulcsok vezérléséhez és kezeléséhez. A key vault és a virtuális gépek ugyanabban az Azure-régióban és előfizetésben kell lennie.

További információt az [Azure Disk Encryption key vault létrehozása és konfigurálása az Azure AD-vel (előző kiadás) című](disk-encryption-key-vault-aad.md)témakörben talál.
 
## <a name="next-steps"></a>További lépések

- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)](disk-encryption-key-vault-aad.md)
- [Az Azure disk encryption engedélyezése az Azure AD-vel Windows virtuális gépeken (előző kiadás)](disk-encryption-windows-aad.md)
- [Az Azure Disk Encryptions CLI-parancsfájl előfeltételei](https://github.com/ejarvi/ade-cli-getting-started)
- [Az Azure disk encryptions powershell-parancsfájl előfeltételei](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
