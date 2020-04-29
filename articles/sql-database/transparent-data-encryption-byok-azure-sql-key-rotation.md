---
title: TDE-védő elforgatása – PowerShell
description: Megtudhatja, hogyan forgathatja el az Azure SQL Server transzparens adattitkosítás (TDE) oltalmazóját.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067173"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>A transzparens adattitkosítás (TDE)-védő elforgatása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan használható az Azure SQL Server TDE-védővel való elforgatása Azure Key Vaultból. Az Azure SQL Server TDE-védő elforgatása azt jelenti, hogy a kiszolgálón lévő adatbázisokat védő új aszimmetrikus kulcsra vált. A kulcs elforgatása egy online művelet, amely csak néhány másodpercig tart, mivel ez csak a teljes adatbázis visszafejtését és újratitkosítását végzi el.

Ez az útmutató két lehetőséget tárgyal a TDE-védő a kiszolgálón való elforgatására.

> [!NOTE]
> A szüneteltetett SQL Data Warehouse a kulcsok elforgatása előtt folytatni kell.

> [!IMPORTANT]
> Egy rollover után ne törölje a kulcs korábbi verzióit. A kulcsok átadásakor a rendszer bizonyos adatokat továbbra is titkosít az előző kulcsokkal, például a régebbi adatbázis-biztonsági mentésekkel.

## <a name="prerequisites"></a>Előfeltételek

- Ez a útmutató azt feltételezi, hogy már használ egy kulcsot Azure Key Vault TDE-védőként egy Azure SQL Database vagy adattárházhoz. Lásd: [TRANSZPARENS ADATTITKOSÍTÁS BYOK-támogatással](transparent-data-encryption-byok-azure-sql.md).
- A Azure PowerShell telepítése és futtatása szükséges.
- [Ajánlott, de nem kötelező] Először a hardveres biztonsági modulban (HSM) vagy a helyi Key Store-ban hozza létre a TDE-védő fő anyagát, és importálja Azure Key Vault. További információért kövesse a [hardveres biztonsági modul (HSM) használatának utasításait és Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Adott parancsmagok esetén lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A telepítéshez lásd: az [Azure CLI telepítése](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Kézi kulcs elforgatása

A manuális kulcs elforgatása az alábbi parancsokkal adhat hozzá egy teljesen új kulcsot, amely az új kulcs neve vagy egy másik kulcstartó alá is kerülhet. Ezzel a módszerrel a magas rendelkezésre állás és a Geo-Dr forgatókönyvek támogatásához ugyanazt a kulcsot kell hozzáadni a különböző kulcstartóhoz.

> [!NOTE]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)és a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja az az [kulcstartó kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create), [az SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)és [az az SQL Server TDE-Key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) parancsokat.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Ha a TDE-védőt a Microsoft által felügyelt BYOK módba szeretné váltani, használja a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Ha a TDE-védőt a BYOK módból a Microsoft által felügyelt értékre szeretné váltani, használja a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi példák az [az SQL Server TDE-Key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)használatát használják.

- Ha a TDE-védőt a Microsoft által felügyelt BYOK módba szeretné váltani,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Ha a TDE-védőt a BYOK módból a Microsoft által felügyelt értékre szeretné váltani,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>További lépések

- Biztonsági kockázat esetén Ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: [feltört kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Ismerkedés a Azure Key Vault integrációjának és a Bring Your Own Key támogatásának TDE: a [TDE bekapcsolása a saját kulcsával Key Vault a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md)
