---
title: Azure lemeztitkosítás az Azure AD-alkalmazás előfeltételeivel (előző kiadás)
description: Ez a cikk előfeltételei a Microsoft Azure lemeztitkosítás iaaS virtuális gépek használatának előfeltételeit.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 029743dbb44ab0ebb6ab8f695c0c966137c45107
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459797"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure lemeztitkosítás az Azure AD-vel (előző kiadás)

Az Azure Disk Encryption új kiadása kiküszöböli az Azure Active Directory (Azure AD) alkalmazásparaméter biztosításának követelményét a virtuális gép lemeztitkosításának engedélyezéséhez. Az új kiadás, már nem kell megadnia az Azure AD hitelesítő adatait a titkosítás engedélyezése lépés során. Minden új virtuális gép titkosítására az Azure AD-alkalmazás paraméterei az új kiadás használatával kell titkosítani. A virtuális gép lemeztitkosításának az új kiadás használatával történő engedélyezéséről az Azure Disk Encryption for Linux virtuális gépek című témakörben talál [útmutatást.](disk-encryption-overview.md) Az Azure AD-alkalmazás paramétereivel már titkosított virtuális gépek továbbra is támogatottak, és továbbra is karban kell tartani az AAD szintaxissal.

Ez a cikk az [Azure Disk Encryption for Linux virtuális gépek](disk-encryption-overview.md) kiegészítéseit tartalmazza az Azure Lemeztitkosítás további követelményeivel és előfeltételeivel az Azure AD-vel (előző kiadás).

Az ezekben a szakaszokban szereplő információk változatlanok maradnak:

- [Támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépre vonatkozó követelmények](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Hálózat- és csoportházirend

Az Azure Disk Encryption szolgáltatás engedélyezéséhez a régebbi AAD paraméterszintaxis használatával az infrastruktúra szolgáltatásként (IaaS) virtuális gépeknek meg kell felelniük a következő hálózati végpontkonfigurációs követelményeknek: 
  - A kulcstartóhoz való csatlakozáshoz az IaaS virtuális gépnek képesnek kell lennie egy \[\]Azure AD-végponthoz való csatlakozásra, login.microsoftonline.com.
  - A titkosítási kulcsok at a key vault, az IaaS virtuális gép képesnek kell lennie a key vault végponthoz való csatlakozáshoz.
  - Az IaaS virtuális gépnek képesnek kell lennie egy Azure-tárolóvégponthoz csatlakozni, amely az Azure-bővítménytárházat és a VHD-fájlokat tároló Azure-tárfiókot üzemelteti.
  -  Ha a biztonsági szabályzat korlátozza a hozzáférést az Azure virtuális gépek az internetre, feloldhatja az előző URI-t, és konfigurálhat egy adott szabályt, hogy lehetővé tegye a kimenő kapcsolatot az IP-k. További információ: [Azure Key Vault tűzfal mögött.](../../key-vault/general/access-behind-firewall.md)
  - Windows rendszerben, ha a TLS 1.0 explicit módon le van tiltva, és a .NET verzió nem frissül 4.6-os vagy újabb verzióra, a következő beállításjegyzék-módosítás lehetővé teszi, hogy az Azure Disk Encryption válassza ki a legújabb TLS-verziót:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Csoportházirend
 - Az Azure Disk Encryption megoldás a BitLocker külső kulcsvédő a Windows IaaS virtuális gépek. Tartományhoz csatlakozó virtuális gépek esetén ne tessen le olyan csoportházirendeket, amelyek tpm-védőket kényszerítenek ki. A **BitLocker engedélyezése kompatibilis TPM nélkül**beállítás csoportházirendjéről a [BitLocker csoportházirend-hivatkozása című](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)témakörben talál.

- Az egyéni csoportházirenddel rendelkező, tartományhoz csatlakozó virtuális gépeken a BitLocker-házirendnek a következő beállítást kell [tartalmaznia: A BitLocker helyreállítási információinak konfigurálása -> 256 bites helyreállítási kulcs engedélyezése](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Az Azure Disk Encryption sikertelen, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Azokon a gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítással, alkalmazza az új házirendet, kényszerítse az új házirendfrissítését (gpupdate.exe /force), majd indítsa újra, ha szükséges. 

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei 

Az Azure Disk Encryption használatához az Azure Key Vault megköveteli a lemeztitkosítási kulcsok és titkos kulcsok vezérléséhez és kezeléséhez. A key vault és a virtuális gépek ugyanabban az Azure-régióban és előfizetésben kell lennie.

További információ: [Létrehozása és konfigurálása a key vault azure lemeztitkosítás az Azure AD (előző kiadás)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>További lépések

- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)](disk-encryption-key-vault-aad.md)
- [Az Azure Disk Encryption engedélyezése az Azure AD-vel Linux os virtuális gépeken (előző kiadás)](disk-encryption-linux-aad.md)
- [Az Azure Disk Encryptions CLI-parancsfájl előfeltételei](https://github.com/ejarvi/ade-cli-getting-started)
- [Az Azure disk encryptions powershell-parancsfájl előfeltételei](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)