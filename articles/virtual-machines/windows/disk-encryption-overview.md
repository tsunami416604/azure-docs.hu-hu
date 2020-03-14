---
title: Azure Disk Encryption engedélyezése Windows rendszerű virtuális gépekhez
description: Ez a cikk útmutatást nyújt a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 05db717f5d3adc2429431503f588f2cc7f79aef6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266779"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption Windows rendszerű virtuális gépekhez 

Azure Disk Encryption segíti az adatai védelmét és védelmét a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. A Windows [BitLocker](https://en.wikipedia.org/wiki/BitLocker) szolgáltatásával biztosítja a kötetek titkosítását az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei számára, és integrálva van [Azure Key Vault](../../key-vault/index.yml) a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez. 

Ha [Azure Security Center](../../security-center/index.yml)használ, a rendszer riasztást küld, ha nem titkosított virtuális gépek vannak. A riasztások magas súlyossági szintű szabályzatként jelenik meg, és az javasoljuk, hogy ezek a virtuális gépek titkosításához.

![Az Azure Security Center titkosítási riasztás](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
> - Bizonyos ajánlások növelheti az adatok, hálózati vagy számítási erőforrás-használat, ami további licencek vagy előfizetések költségeit. Érvényes aktív Azure-előfizetést hozhat létre erőforrásokat az Azure-ban támogatott régiókban kell rendelkeznie.

Néhány perc alatt megismerheti a Windows Azure Disk Encryption alapjait, és a Windows rendszerű virtuális gépek [létrehozása és titkosítása](disk-encryption-cli-quickstart.md) az Azure CLI gyors üzembe helyezésével, valamint a [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell](disk-encryption-powershell-quickstart.md)gyors üzembe helyezésével.

## <a name="supported-vms-and-operating-systems"></a>Támogatott virtuális gépek és operációs rendszerek

### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

A Windows rendszerű virtuális gépek [számos méretben](sizes-general.md)érhetők el. Azure Disk Encryption nem érhető el az [alapszintű, a sorozatú](https://azure.microsoft.com/pricing/details/virtual-machines/series/)virtuális gépeken, illetve a 2 GB-nál kevesebb memóriával rendelkező virtuális gépeken.

A Premium Storage szolgáltatással rendelkező virtuális gépek esetében Azure Disk Encryption is elérhető.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- Windows-ügyfél: Windows 8 és újabb verziók.
- Windows Server: Windows Server 2008 R2 és újabb verziók.  
 
> [!NOTE]
> A Windows Server 2008 R2 megköveteli, hogy a .NET-keretrendszer 4,5 legyen telepítve a titkosításhoz; telepítse azt Windows Update a Windows Server 2008 R2 x64-alapú rendszerek ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) választható frissítés Microsoft .net keretrendszere 4.5.2-es verziójával.  
>  
> A Windows Server 2012 R2 Core és a Windows Server 2016 Core megköveteli, hogy a bdehdcfg összetevőt a virtuális gépre telepítse a titkosításhoz.


## <a name="networking-requirements"></a>Hálózati követelmények
A Azure Disk Encryption engedélyezéséhez a virtuális gépeknek meg kell felelniük a hálózati végpont következő konfigurációs követelményeinek:
  - Ahhoz, hogy tokent kapjon a kulcstartóhoz való kapcsolódáshoz, a Windows rendszerű virtuális gépnek csatlakoznia kell egy Azure Active Directory-végponthoz, \[login.microsoftonline.com\].
  - A titkosítási kulcsok a kulcstartóba való írásához a Windows rendszerű virtuális gépnek csatlakoznia kell a Key Vault-végponthoz.
  - A Windows rendszerű virtuális gépnek képesnek kell lennie csatlakozni egy Azure Storage-végponthoz, amely az Azure-bővítmény adattárát és a VHD-fájlokat tároló Azure Storage-fiókot üzemelteti.
  -  Ha a biztonsági házirend korlátozza az Internet-hozzáférést az Azure virtuális gépekről, oldja meg az előző URI-t, és konfigurálja egy adott szabályt, amely engedélyezi a kimenő kapcsolat az IP-címekről. További információ: [Azure Key Vault tűzfal mögött](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Csoportházirend követelmények

A Azure Disk Encryption a Windows rendszerű virtuális gépekhez készült BitLocker External Key protectort használja. Tartományhoz csatlakozó virtuális gépek esetén nem leküldéses bármely csoportházirendek, amelyeket a TPM-védőt. További információ a "BitLocker engedélyezése kompatibilis TPM nélkül" Csoportházirendről: a [bitlocker csoportházirend referenciája](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

A tartományhoz csatlakoztatott, egyéni csoportházirendtel rendelkező virtuális gépekre vonatkozó BitLocker-házirendnek tartalmaznia kell a következő beállítást: a [BitLocker helyreállítási adatok felhasználói tárolójának konfigurálása – > engedélyezi a 256 bites helyreállítási kulcsot](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Gépeken, amelyek nem rendelkeznek a megfelelő házirend-beállítást, az új szabályzat alkalmazásának frissítése (a gpupdate.exe/Force) az új szabályzat kényszerítése és indítsa újra lehet szükség.

A Azure Disk Encryption sikertelen lesz, ha a tartományi szintű csoportházirend letiltja a BitLocker által használt AES-CBC algoritmust.

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

További információ: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminológia
Az alábbi táblázat az Azure Disk Encryption dokumentációjában használt általános kifejezéseket ismerteti:

| Terminológia | Meghatározás |
| --- | --- |
| Azure Key Vault | A Key Vault szolgáltatás titkosítási, key management, amelyek az rendelkezik a Federal Information Processing szabványok (FIPS) hitelesített hardveres biztonsági modulokban. Ezen irányelvek segítenek a kriptográfiai kulcsok és a bizalmas, titkos kulcsok védelme érdekében. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) az Azure-erőforrások parancssorból történő kezelésére és felügyeletére van optimalizálva.|
| BitLocker |A [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) egy iparág által felismert Windows mennyiségi titkosítási technológia, amely lehetővé teszi a lemezes titkosítás használatát a Windows rendszerű virtuális gépeken. |
| Kulcs titkosítási kulcsa (KEK) | Az aszimmetrikus kulcs (RSA 2048), amellyel védetté teheti vagy becsomagolhatja a titkos kulcsot. Megadhat egy hardveres biztonsági modul (HSM) – vagy szoftveres védelemmel ellátott kulcs védett. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| PowerShell-parancsmagok | További információ: [Azure PowerShell parancsmagok](/powershell/azure/overview). |


## <a name="next-steps"></a>További lépések

- [Rövid útmutató – Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Rövid útmutató – Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez](disk-encryption-windows.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](disk-encryption-key-vault.md)


