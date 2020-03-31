---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk az Azure Disk Encryption szolgáltatással használható kulcstároló létrehozásának és konfigurálásának lépéseit ismerteti.
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279005"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz

Az Azure Disk Encryption az Azure Key Vault segítségével szabályozza és kezeli a lemeztitkosítási kulcsokat és titkos kulcsokat.  A kulcstartókról az [Azure Key Vault – és](../key-vault/key-vault-get-started.md) a Key Vault [biztonságossá tétele](../key-vault/key-vault-secure-your-key-vault.md)című témakörben talál további információt.

Az Azure Disk Encryption szolgáltatással való használatra szolgáló kulcstartó létrehozása és konfigurálása három lépésből áll:

1. Erőforráscsoport létrehozása, ha szükséges.
2. Kulcstartó létrehozása. 
3. A key vault speciális hozzáférési szabályzatok beállítása.

Ezeket a lépéseket a következő rövid útmutatók szemléltetik:

Ha kívánja, kulcstitkosítási kulcsot (KEK) is létrehozhat vagy importálhat.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben ismertetett lépések az [Azure CLI,](/cli/azure/)az [Azure PowerShell Az modul](/powershell/azure/overview)vagy az Azure [Portal](https://portal.azure.com)segítségével hajthatók végre.

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy az Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéshez. Ehhez jelentkezzen [be az Azure CLI-vel,](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) [jelentkezzen be az Azure Powershellnel,](/powershell/azure/authenticate-azureps?view=azps-2.5.0)vagy adja meg hitelesítő adatait az Azure Portalon, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>További lépések

- [Azure lemeztitkosítás – áttekintés](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure PowerShell használatával](disk-encryption-powershell.md)
