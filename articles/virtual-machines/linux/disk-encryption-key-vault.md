---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk a Key Vault létrehozásával és konfigurálásával kapcsolatos lépéseket ismerteti Azure Disk Encryption
ms.service: virtual-machines-linux
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 543e3c5d8148632eaca33935cc15e2d2ab1c0cea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611594"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz

A Azure Disk Encryption a Azure Key Vault használatával vezérli és kezeli a lemezes titkosítási kulcsokat és titkokat.  További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../../key-vault/key-vault-get-started.md) és [a kulcstartó biztonságossá tétele](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. További részletekért lásd: [kulcstartó létrehozása és konfigurálása az Azure Disk Encryptionhoz az Azure ad-vel (előző kiadás)](disk-encryption-key-vault-aad.md) .

A Key Vault létrehozásához és konfigurálásához Azure Disk Encryption a következő három lépésből áll:

1. Ha szükséges, hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. A Key Vault speciális hozzáférési házirendjeinek beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók szemléltetik:

- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)

Ha szeretné, létrehozhat vagy importálhat egy kulcs-titkosítási kulcsot (KEK) is.

> [!Note]
> A cikkben ismertetett lépések a [Azure Disk Encryption előfeltételként megadott parancssori felület parancsfájljában](https://github.com/ejarvi/ade-cli-getting-started) és [Azure Disk Encryption előfeltételek PowerShell-parancsfájlban](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)találhatók.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

A cikkben ismertetett lépések az [Azure CLI](/cli/azure/)-vel, az Azure PowerShell az [module](/powershell/azure/overview)vagy a [Azure Portal](https://portal.azure.com)használatával végezhetők el. 

Amíg a portál elérhető a böngészőben, az Azure CLI és a Azure PowerShell helyi telepítést igényel; További részletekért lásd [: Azure Disk Encryption for Linux (eszközök telepítése](disk-encryption-linux.md#install-tools-and-connect-to-azure) ).

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéséhez. Ehhez [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [Jelentkezzen be az Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)-lel, vagy adja meg a hitelesítő adatait a Azure Portalhoz, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>További lépések

- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [A Linux rendszerű virtuális gépeken Azure Disk Encryption forgatókönyvek](disk-encryption-linux.md) ismertetése
- Tudnivalók a [Azure Disk Encryption hibakereséséről](disk-encryption-troubleshooting.md)
- A [Azure Disk Encryption minta parancsfájljainak](disk-encryption-sample-scripts.md) beolvasása
