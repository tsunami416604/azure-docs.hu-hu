---
title: Az Azure lemeztitkosítás engedélyezése Windows-alapú virtuális gépekhez
description: Ez a cikk a Microsoft Azure lemeztitkosítás Windows virtuális gépekhez való engedélyezésével kapcsolatos utasításokat ismerteti.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266779"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure lemeztitkosítás Windows virtuális gépekhez 

Az Azure Disk Encryption segít az adatok biztonságos megőrzésében a vállalat által előírt biztonsági és megfelelőségi követelmények kielégítése érdekében. A Windows [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) szolgáltatását használja az Azure virtuális gépek operációs rendszerének és adatlemezeinek kötettitkosításához, és integrálva van az [Azure Key Vaultszolgáltatással,](../../key-vault/index.yml) hogy segítsen a lemeztitkosítási kulcsok és titkos kulcsok vezérlésében és kezelésében. 

Ha [az Azure Security Center,](../../security-center/index.yml)a rendszer figyelmezteti, ha a virtuális gépek, amelyek nem titkosított. A riasztások nagy súlyosságú, és a javaslat a virtuális gépek titkosítása.

![Az Azure Security Center lemeztitkosítási riasztása](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részletekért tekintse meg [az Azure Disk Encryption with Azure AD (előző kiadás)](disk-encryption-overview-aad.md) című témakört. 
> - Bizonyos javaslatok növelhetik az adatok, a hálózat vagy a számítási erőforrás-használat, ami további licenc vagy előfizetési költségeket. Érvényes aktív Azure-előfizetéssel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az Azure-ban a támogatott régiókban.

Az Azure Disk Encryption for Windows alapjait néhány perc alatt megismerheti a Windows virtuális gép létrehozása és titkosítása az [Azure CLI gyorsindítással,](disk-encryption-cli-quickstart.md) vagy a [Windows virtuális gép létrehozása és titkosítása az Azure Powershell gyorsindítással.](disk-encryption-powershell-quickstart.md)

## <a name="supported-vms-and-operating-systems"></a>Támogatott virtuális gépek és operációs rendszerek

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

A Windows virtuális gépek [számos méretben](sizes-general.md)érhetők el. Az Azure Disk Encryption nem érhető el [alapszintű, A-sorozatú virtuális gépeken](https://azure.microsoft.com/pricing/details/virtual-machines/series/)vagy 2 GB-nál kevesebb memóriával rendelkező virtuális gépeken.

Az Azure Disk Encryption prémium szintű tárhellyel rendelkező virtuális gépekhez is elérhető.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- Windows-ügyfél: Windows 8 és újabb verziók.
- Windows Server: Windows Server 2008 R2 és újabb verziók.  
 
> [!NOTE]
> A Windows Server 2008 R2 titkosításhoz telepítenie kell a . telepítse a Windows Update szolgáltatásból a Microsoft .NET Framework 4.5.2 windows Server 2008 R2 x64-alapú rendszerekhez készült opcionális frissítéssel ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> A Windows Server 2012 R2 Core és a Windows Server 2016 Core titkosításhoz a bdehdcfg összetevő telepítését igényli a virtuális gépre.


## <a name="networking-requirements"></a>Hálózati követelmények
Az Azure lemeztitkosítás engedélyezéséhez a virtuális gépeknek meg kell felelniük a következő hálózati végpontkonfigurációs követelményeknek:
  - A kulcstartóhoz való csatlakozáshoz a Windows virtuális gépnek képesnek kell lennie egy \[\]Azure Active Directory-végponthoz, login.microsoftonline.com.
  - A titkosítási kulcsok at a key vault, a Windows virtuális gép képesnek kell lennie arra, hogy csatlakozzon a key vault végpont.
  - A Windows virtuális gépnek képesnek kell lennie egy Azure-tárolóvégponthoz csatlakozni, amely az Azure-bővítménytárházat és a virtuális merevlemez-fájlokat tároló Azure-tárfiókot üzemelteti.
  -  Ha a biztonsági szabályzat korlátozza a hozzáférést az Azure virtuális gépek az internetre, feloldhatja az előző URI-t, és konfigurálhat egy adott szabályt, hogy lehetővé tegye a kimenő kapcsolatot az IP-k. További információ: [Azure Key Vault tűzfal mögött.](../../key-vault/key-vault-access-behind-firewall.md)    


## <a name="group-policy-requirements"></a>Csoportházirend-követelmények

Az Azure Disk Encryption a BitLocker külső kulcsvédőt használja a Windows virtuális gépekhez. Tartományhoz csatlakozó virtuális gépek esetén ne tessen le olyan csoportházirendeket, amelyek tpm-védőket kényszerítenek ki. A "BitLocker engedélyezése kompatibilis TPM nélkül" csoportházirendről a [BitLocker csoportházirend-útmutatójában talál.](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)

Az egyéni csoportházirenddel rendelkező tartományhoz csatlakozó virtuális gépeken lévő BitLocker-házirendnek a következő beállítást kell [tartalmaznia: A BitLocker helyreállítási információinak konfigurálása -> 256 bites helyreállítási kulcs engedélyezése](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Az Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Azokon a gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítással, alkalmazza az új házirendet, kényszerítse az új házirendfrissítését (gpupdate.exe /force), majd újraindításra lehet szükség.

Az Azure Disk Encryption sikertelen lesz, ha a tartományszintű csoportházirend blokkolja az AES-CBC algoritmust, amelyet a BitLocker használ.

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Az Azure Disk Encryption használatához egy Azure Key Vault szükséges a lemeztitkosítási kulcsok és titkos kulcsok vezérléséhez és kezeléséhez. A key vault és a virtuális gépek ugyanabban az Azure-régióban és előfizetésben kell lennie.

További információt az [Azure Disk Encryption kulcstartójának létrehozása és konfigurálása című témakörben talál.](disk-encryption-key-vault.md)

## <a name="terminology"></a>Terminológia
Az alábbi táblázat az Azure lemeztitkosítási dokumentációjában használt néhány általános kifejezést határozza meg:

| Terminológia | Meghatározás |
| --- | --- |
| Azure Key Vault | A Key Vault egy kriptográfiai, kulcskezelő szolgáltatás, amely a Federal Information Processing Standards (FIPS) által ellenőrzött hardveres biztonsági modulokon alapul. Ezek a szabványok segítenek a kriptográfiai kulcsok és a bizalmas titkok védelmében. További információt az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) [dokumentációjában, valamint az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál. |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) az Azure-erőforrások parancssorból történő kezelésére és felügyeletére van optimalizálva.|
| BitLocker |[A BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparágáltal elismert Windows kötettitkosítási technológia, amely a Windows virtuális gépek lemeztitkosításának engedélyezésére szolgál. |
| Kulcstitkosítási kulcs (KEK) | Az aszimmetrikus kulcs (RSA 2048), amely a titkos kulcs védelmére vagy burkolására használható. Hardveres biztonsági modul (HSM) által védett kulcsot vagy szoftverrel védett kulcsot is biztosíthat. További információt az [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) [dokumentációjában, valamint az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)című témakörben talál. |
| PowerShell-parancsmagok | További információ: [Azure PowerShell-parancsmagok.](/powershell/azure/overview) |


## <a name="next-steps"></a>További lépések

- [Rövid útmutató – Windows virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Rövid útmutató – Windows virtuális gép létrehozása és titkosítása az Azure Powershell használatával](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez](disk-encryption-windows.md)
- [Az Azure Disk Encryptions CLI-parancsfájl előfeltételei](https://github.com/ejarvi/ade-cli-getting-started)
- [Az Azure disk encryptions powershell-parancsfájl előfeltételei](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](disk-encryption-key-vault.md)


