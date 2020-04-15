---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk az Azure Disk Encryption szolgáltatással használható kulcstároló létrehozásának és konfigurálásának lépéseit ismerteti.
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5d9acb525f35da756a986826574082f1ecafedf5
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314119"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz

Az Azure Disk Encryption az Azure Key Vault segítségével szabályozza és kezeli a lemeztitkosítási kulcsokat és titkos kulcsokat.  A kulcstartókról az [Azure Key Vault – és](../../key-vault/key-vault-get-started.md) a Key Vault [biztonságossá tétele](../../key-vault/key-vault-secure-your-key-vault.md)című témakörben talál további információt. 

> [!WARNING]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részleteket az Azure Disk Encryption és az [Azure AD (előző kiadás) key vault létrehozása és konfigurálása](disk-encryption-key-vault-aad.md) című témakörben találja.

Az Azure Disk Encryption szolgáltatással való használatra szolgáló kulcstartó létrehozása és konfigurálása három lépésből áll:

1. Erőforráscsoport létrehozása, ha szükséges.
2. Kulcstartó létrehozása. 
3. A key vault speciális hozzáférési szabályzatok beállítása.

Ezeket a lépéseket a következő rövid útmutatók szemléltetik:

- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-cli-quickstart.md)

Ha kívánja, kulcstitkosítási kulcsot (KEK) is létrehozhat vagy importálhat.

> [!Note]
> A cikkben ismertetett lépések automatikusan az [Azure Disk Encryption előfeltételei CLI-parancsfájl](https://github.com/ejarvi/ade-cli-getting-started) és az [Azure Disk Encryption előfeltételei PowerShell-parancsfájl.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben ismertetett lépések az [Azure CLI,](/cli/azure/)az [Azure PowerShell Az modul](/powershell/azure/overview)vagy az Azure [Portal](https://portal.azure.com)segítségével hajthatók végre. 

Bár a portál elérhető a böngészőn keresztül, az Azure CLI és az Azure PowerShell helyi telepítést igényel; A részleteket [lásd: Azure Disk Encryption for Linux: Install tools.](disk-encryption-linux.md#install-tools-and-connect-to-azure)

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy az Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéshez. Ehhez jelentkezzen [be az Azure CLI-vel,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [jelentkezzen be az Azure Powershellnel,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)vagy adja meg hitelesítő adatait az Azure Portalon, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 ```
 
## Next steps

- [Azure Disk Encryption prerequisites CLI script](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Learn [Azure Disk Encryption scenarios on Linux VMs](disk-encryption-linux.md)
- Learn how to [troubleshoot Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Read the [Azure Disk Encryption sample scripts](disk-encryption-sample-scripts.md)
