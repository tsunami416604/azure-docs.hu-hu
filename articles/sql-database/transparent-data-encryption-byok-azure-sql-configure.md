---
title: SQL-TDE engedélyezése Azure Key Vault
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL Database és egy adattárházat, hogy a PowerShell vagy a parancssori felület használatával megkezdje a titkosítást a transzparens adattitkosítás (TDE) használatával.
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
ms.openlocfilehash: 72825a709ec55bf5879e15287eb8b258dc4c8e94
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965793"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell és CLI: transzparens adattitkosítás engedélyezése az ügyfél által felügyelt kulccsal Azure Key Vault

Ez a cikk bemutatja, hogyan használhatja a Azure Key Vault for transzparens adattitkosítás (TDE) kulcsát SQL Database vagy adattárházban. Ha többet szeretne megtudni a Azure Key Vault Integration-Bring Your Own Key (BYOK) támogatással rendelkező TDE, látogasson el a [TDE felhasználó által felügyelt kulcsokra a Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

- Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
- [Ajánlott, de nem kötelező] Hardveres biztonsági modult (HSM) vagy helyi kulcstárolót kell létrehoznia a TDE-védő kulcsfontosságú anyagának helyi másolatának létrehozásához.
- A Azure PowerShell telepítése és futtatása szükséges.
- Hozzon létre egy Azure Key Vault és egy kulcsot a TDE használatához.
  - [Útmutató a hardveres biztonsági modul (HSM) és a Key Vault használatához](../key-vault/key-vault-hsm-protected-keys.md)
    - A Key vaultnak a következő tulajdonságot kell használnia a TDE:
  - a védelem eltávolítása és [törlése](../key-vault/key-vault-ovw-soft-delete.md)
- A kulcsnak a következő attribútumokkal kell rendelkeznie a TDE való használathoz:
   - Nincs lejárati dátum
   - Nincs letiltva
   - Képes a *Get*, a *wrap Key*, a *dewrap Key* Operations művelet végrehajtására

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. Adott parancsmagok esetén lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

A Key Vault kapcsolatos részletekért tekintse meg a [Key Vault PowerShell-utasításokat](../key-vault/quick-create-powershell.md) , valamint a [Key Vault a PowerShell használatával történő törlését](../key-vault/key-vault-soft-delete-powershell.md).

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Azure AD-identitás kiosztása a kiszolgálóhoz

Ha rendelkezik meglévő kiszolgálóval, az alábbi paranccsal adhat hozzá Azure AD-identitást a kiszolgálóhoz:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Ha kiszolgálót hoz létre, használja a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancsmagot a tag-Identity paranccsal az Azure ad-identitás hozzáadásához a kiszolgáló létrehozásakor:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault engedélyek megadása a kiszolgálónak

A [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag használatával biztosíthatja a kiszolgáló hozzáférését a Key vaulthoz, mielőtt a TDE-hoz tartozó kulcsot használ.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adja hozzá a Key Vault kulcsot a kiszolgálóhoz, és állítsa be a TDE-védőt.

- A Key Vault AZONOSÍTÓjának lekéréséhez használja a [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) parancsmagot.
- Az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) parancsmag használatával adja hozzá a kulcsot a Key Vault a-kiszolgálóhoz.
- A [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmag használatával állítsa be a kulcsot TDE-védőként az összes kiszolgálói erőforráshoz.
- A [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával ellenőrizze, hogy a TDE-védő a kívánt módon lett-e konfigurálva.

> [!NOTE]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

> [!TIP]
> Példa a Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h KeyId

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE bekapcsolása

A TDE bekapcsolásához használja a [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmagot.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Most, hogy az adatbázis vagy az adattárház TDE engedélyezve van a Key Vaultban lévő titkosítási kulccsal.

## <a name="check-the-encryption-state-and-encryption-activity"></a>A titkosítási állapot és a titkosítási tevékenység keresése

A [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) használatával lekérheti a titkosítási állapotot és a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) , és megtekintheti az adatbázis vagy az adatraktár titkosítási folyamatát.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

A szükséges parancssori felület 2,0-es vagy újabb verziójának telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz lásd: [Az Azure platformfüggetlen parancssori 2,0 felületének telepítése és konfigurálása](https://docs.microsoft.com/cli/azure/install-azure-cli).

A Key Vault kapcsolatos részletekért lásd: [Key Vault kezelése a parancssori felület 2,0](../key-vault/key-vault-manage-with-cli2.md) és a parancssori felülettel való [Törlés Key Vault](../key-vault/key-vault-soft-delete-cli.md)használatával.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Azure AD-identitás kiosztása a kiszolgálóhoz

```powershell
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Tartsa meg a "principalID" a kiszolgáló létrehozásával, a Key Vault-engedélyek hozzárendeléséhez használt objektumazonosító a következő lépésben

## <a name="grant-key-vault-permissions-to-your-server"></a>Key Vault engedélyek megadása a kiszolgálónak

```powershell
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Tartsa meg az új kulcs kulcs-URI-JÁT vagy keyID a következő lépéshez, például: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Adja hozzá a Key Vault kulcsot a kiszolgálóhoz, és állítsa be a TDE-védőt.

```powershell
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

## <a name="turn-on-tde"></a>TDE bekapcsolása

```powershell
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Most, hogy az adatbázis vagy az adattárház TDE van engedélyezve a felhasználó által felügyelt titkosítási kulccsal Azure Key Vaultban.

## <a name="check-the-encryption-state-and-encryption-activity"></a>A titkosítási állapot és a titkosítási tevékenység keresése

```powershell
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

- A TDE kikapcsolásához használja a [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmagot.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled”
   ```

- A [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmag használatával adja vissza a kiszolgálóhoz hozzáadott Key Vault kulcsok listáját.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- A [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) használatával távolítson el egy Key Vault kulcsot a kiszolgálóról.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

- Az adatbázis általános beállításai: [az SQL](/cli/azure/sql).

- A tár legfontosabb beállításai: [az SQL Server Key](/cli/azure/sql/server/key).

- A TDE beállításai: [az SQL Server TDE-Key](/cli/azure/sql/server/tde-key) és [az SQL db TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Hibaelhárítás

Ha probléma merül fel, ellenőrizze a következőket:

- Ha a kulcstartó nem található, győződjön meg róla, hogy a megfelelő előfizetésben van.

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Ha az új kulcs nem adható hozzá a kiszolgálóhoz, vagy az új kulcs nem frissíthető TDE-védőként, ellenőrizze a következőket:
   - A kulcs nem rendelkezhet lejárati dátummal
   - A kulcsnak engedélyezve kell lennie a *Get*, a *wrap Key*és a *dewrap Key* műveletnek.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan forgathatja el egy kiszolgáló TDE-védelmezőjét a biztonsági követelményeknek való megfelelés érdekében: [a transzparens adattitkosítás Protector elforgatása a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Biztonsági kockázat esetén Ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: [távolítson el egy potenciálisan feltört kulcsot](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
