---
title: SQL TDE engedélyezése az Azure Key Vault segítségével
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL Database és Data Warehouse az Átlátszó adattitkosítás (TDE) használatát a PowerShell vagy a CLI használatával való inaktív titkosításhoz.
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
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452757"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell és CLI: Az átlátszó adattitkosítás engedélyezése az Azure Key Vault ügyféláltal felügyelt kulcsával

Ez a cikk bemutatja, hogyan használhatja az Azure Key Vault egyik kulcsát az átlátszó adattitkosításhoz (TDE) egy SQL-adatbázisban vagy adattárházon. Ha többet szeretne megtudni a TDE-ről az Azure Key Vault-integrációval – Hozza magával a saját kulcsa (BYOK) támogatását, látogasson el a [TDE-be az ügyfél által felügyelt kulcsokkal az Azure Key Vaultban.](transparent-data-encryption-byok-azure-sql.md) 

## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

- Rendelkeznie kell egy Azure-előfizetéssel, és az adott előfizetés rendszergazdájának kell lennie.
- [Ajánlott, de nem kötelező] Hardveres biztonsági modullal (HSM) vagy helyi kulcstárolóval rendelkezik a TDE Protector kulcsanyag helyi példányának létrehozásához.
- Az Azure PowerShell telepítve és fut.
- Hozzon létre egy Azure Key Vault és a TDE-hez használható kulcs.
  - [A hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasítások](../key-vault/keys/hsm-protected-keys.md)
    - A kulcstartónak a következő tulajdonsággal kell rendelkeznie a TDE-hez használandó tulajdonsággal:
  - [soft-delete](../key-vault/general/overview-soft-delete.md) és tisztítás elleni védelem
- A kulcsnak a következő attribútumokkal kell rendelkeznie a TDE-hez:
   - Nincs lejárati dátum
   - Nincs letiltva
   - Képes végrehajtani *kap*, *wrap kulcs*, *kicsomagolni kulcs* műveletek

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. A konkrét parancsmagok: [AzureRM.Sql.](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

A Key Vault konkrétumait a [Key Vault PowerShell-utasításaiban,](../key-vault/secrets/quick-create-powershell.md) valamint [a Key Vault soft-delete használatával a PowerShell használatával kapcsolatban találja.](../key-vault/general/soft-delete-powershell.md)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Azure AD-identitás hozzárendelése a kiszolgálóhoz

Ha meglévő kiszolgálóval rendelkezik, az alábbiak szerint adhat hozzá egy Azure AD-identitást a kiszolgálóhoz:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Ha kiszolgálót hoz létre, használja a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancsmamot a -Identity címkével az Azure AD-identitás hozzáadásához a kiszolgáló létrehozása során:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault-engedélyek megadása a kiszolgálónak

A [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag segítségével hozzáférést biztosíthat a kiszolgálónak a key vaulthoz, mielőtt kulcsot használna belőle a TDE-hez.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>A Key Vault kulcs hozzáadása a kiszolgálóhoz és a TDE-védő beállítása

- A [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) parancsmag segítségével kérje le a kulcsazonosítót a key vaultból
- Az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) parancsmag segítségével adja hozzá a kulcsot a Key Vaultból a kiszolgálóhoz.
- A [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmag segítségével állítsa be a kulcsot TDE-védőként az összes kiszolgálói erőforráshoz.
- A [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag segítségével ellenőrizze, hogy a TDE-védő megfelelően lett-e konfigurálva.

> [!NOTE]
> A kulcstartó nevének és kulcsnevének együttes hossza nem haladhatja meg a 94 karaktert.

> [!TIP]
> Példa keyid a Key Vault:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>A TDE bekapcsolása

A [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmag segítségével kapcsolja be a TDE-t.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Most az adatbázis vagy az adattárház tde engedélyezve van egy titkosítási kulcs a Key Vaultban.

## <a name="check-the-encryption-state-and-encryption-activity"></a>A titkosítási állapot és a titkosítási tevékenység ellenőrzése

A [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) segítségével lekéri a titkosítási állapotot, a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) pedig az adatbázis vagy adattárház titkosítási folyamatának ellenőrzéséhez.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A szükséges 2.0-s vagy újabb parancssori felület telepítéséhez és az Azure-előfizetéshez való csatlakozáshoz az [Azure Cross-Platform command-line interface 2.0 telepítése és konfigurálása](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakörben van.

A Key Vault konkrétumait a [Key Vault cli 2.0-s használatával és](../key-vault/general/manage-with-cli2.md) a Key Vault [soft-delete használata a CLI-vel való használata.](../key-vault/general/soft-delete-cli.md)

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Azure AD-identitás hozzárendelése a kiszolgálóhoz

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Tartsa a "principalID" a kiszolgáló létrehozása, ez az objektum azonosító hozzárendeléséhez használt kulcstartó engedélyek a következő lépésben

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault-engedélyek megadása a kiszolgálónak

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Tartsa meg az új kulcs kulcsURI-ját vagy kulcsazonosítóját a következő lépéshez, például:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>A Key Vault kulcs hozzáadása a kiszolgálóhoz és a TDE-védő beállítása

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> A kulcstartó nevének és kulcsnevének együttes hossza nem haladhatja meg a 94 karaktert.

## <a name="turn-on-tde"></a>A TDE bekapcsolása

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Most az adatbázis vagy az adattárház rendelkezik TDE engedélyezve van egy ügyfél által felügyelt titkosítási kulcs az Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>A titkosítási állapot és a titkosítási tevékenység ellenőrzése

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- A [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmag segítségével kapcsolja ki a TDE-t.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- A [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmag segítségével adja vissza a kiszolgálóhoz hozzáadott Key Vault-kulcsok listáját.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Az [Remove-AzSqlServerKey VaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) segítségével távolítsa el a Key Vault-kulcsot a kiszolgálóról.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Az általános adatbázis-beállításokról az az sql című [témakörben olvashat.](/cli/azure/sql)

- A tároló kulcsbeállításairól az [AZ SQL server key](/cli/azure/sql/server/key)című témakörben olvashat.

- A TDE-beállításokat az [AZ SQL server tde-key](/cli/azure/sql/server/tde-key) és az az sql db tde című [témakörben találja.](/cli/azure/sql/db/tde)

* * *

## <a name="troubleshooting"></a>Hibaelhárítás

Probléma esetén ellenőrizze az alábbiakat:

- Ha a key vault nem található, győződjön meg arról, hogy a megfelelő előfizetésben van.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Ha az új kulcs nem adható hozzá a kiszolgálóhoz, vagy az új kulcs nem frissíthető TDE-védőként, ellenőrizze az alábbiakat:
   - A kulcsnak nem lehet lejárati dátuma.
   - A kulcsnak engedélyeznie kell a *be,* *wrap kulcsot*és *a kulcskicsomagolási* műveleteket.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan forgathatja el a kiszolgáló TDE-védőjét a biztonsági követelményeknek való megfelelés érdekében: [Az átlátszó adattitkosítás-védő elforgatása a PowerShell használatával.](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Biztonsági kockázat esetén olvassa el, hogyan távolíthatja el a potenciálisan sérült TDE-védőt: [Potenciálisan sérült kulcs eltávolítása.](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)
