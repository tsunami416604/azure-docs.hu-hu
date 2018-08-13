---
title: 'PowerShell és CLI: SQL TDE - kulcs – Azure SQL Database engedélyezése |} A Microsoft Docs'
description: Egy Azure SQL Database és az adatraktár használatához a transzparens adattitkosítás (TDE) az inaktív titkosítási konfigurálása a PowerShell vagy parancssori felület használatával.
services: sql-database
keywords: ''
documentationcenter: ''
author: aliceku
manager: craigg
editor: ''
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.devlang: azurecli, powershell
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0dd0d322bacd807dc0cc59aeeb854c427a745bca
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043854"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell és a parancssori felület: Azure Key vaultból saját kulcs használata transzparens adattitkosítás engedélyezése

Ez a cikk végigvezeti a transzparens adattitkosítás (TDE) egy SQL-adatbázis vagy adatraktár Azure Key Vaultban lévő kulcsot használ. A TDE Bring Your Own Key (BYOK) támogatásával kapcsolatos további információkért látogasson el [TDE Bring Your Own Key az Azure SQL](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>PowerShell előfeltételei

- Azure-előfizetés és a lehet az előfizetés-rendszergazda.
- [Opcionális de javasolt] Rendelkezik egy hardveres biztonsági modul (HSM) vagy a helyi kulcs létrehozásához a TDE-Védőhöz megosztottkulcs-anyag helyi másolatát tárolja.
- Rendelkeznie kell Azure PowerShell 4.2.0-s verzió vagy újabb telepítése és futtatása. 
- Hozzon létre egy Azure Key Vault és a kulcs TDE használatára.
   - [Key vault PowerShell-utasítások](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)
   - [Egy hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasítások](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - A key vaultban kell rendelkeznie a TDE használható a következő tulajdonság:
   - [helyreállítható törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
   - [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) 
- A kulcs TDE használható a következő attribútumokkal kell rendelkeznie:
   - Lejárati dátum nélküli
   - Nincs letiltva
   - Sikerült elvégezni *első*, *kulcs becsomagolása*, *kulcs kicsomagolása* műveletek

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>1. lépés Az Azure AD identity rendelhet hozzá a kiszolgálóhoz 

Ha rendelkezik egy meglévő kiszolgálóra, használja az Azure AD-identitás hozzáadása a kiszolgálóhoz a következő:

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

A kiszolgáló létrehozásakor használja a [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) címkével parancsmag-identitás hozzáadása egy Azure AD identity kiszolgáló létrehozása során:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2. lépés A kiszolgálóhoz a Key Vault-engedélyek megadása

Használja a [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) parancsmagot, hogy a kiszolgáló hozzáférést biztosítani a kulcs előtt származó kulcsot használ a TDE-tároló.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3. lépés A Key Vault-kulcs hozzáadása a kiszolgálóhoz, és állítsa be a TDE-Védőhöz

- Használja a [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) parancsmag használatával adja hozzá a kulcsot a Key Vault a kiszolgálón.
- Használja a [Set-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) parancsmagot, hogy a kulcs állítja be a TDE-védőhöz, az összes kiszolgáló-erőforráshoz.
- Használja a [Get-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) parancsmaggal győződjön meg arról, hogy a TDE-védőhöz helyesen lett-e konfigurálva.

> [!Note]
> A kulcstároló nevét és a kulcs nevét együttes hossza nem lehet 94 karakternél.
> 

>[!Tip]
>Példa kulcsazonosító a Key Vaultból: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>4. lépés Kapcsolja be a TDE 

Használja a [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) kapcsolja be a TDE-parancsmagot.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Az adatbázis, sem az adattárházra már TDE engedélyezve van a titkosítási kulcsot a Key Vaultban.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5. lépés Ellenőrizze a titkosítási állapot és a titkosítás tevékenység

Használja a [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) a titkosítás állapotának és a [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) ellenőrizze a titkosítási folyamat egy adatbázis, sem az adattárházra.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Egyéb hasznos PowerShell-parancsmagok

- Használja a [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) kapcsolja ki a TDE-parancsmagot.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Használja a [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) parancsmag a listát a Key Vault-kulcsok hozzáadni a kiszolgálóhoz.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Használja a [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) a Key Vault-kulcs eltávolítása a kiszolgálóról.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Hibaelhárítás

Ellenőrizze a következőket a probléma akkor fordul elő, ha:
- Ha a key vault nem található, ellenőrizze, hogy használja-e a megfelelő előfizetést használ a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) parancsmagot.

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Ha a kiszolgáló nem adható hozzá az új kulcsot, vagy az új kulcs nem lehet frissíteni a TDE-Védőhöz, ellenőrizze a következőket:
   - A kulcs nem rendelkezhet egy lejárati dátuma
   - A kulcsnak rendelkeznie kell a *első*, *kulcs becsomagolása*, és *kulcs kicsomagolása* engedélyezett műveletek.

## <a name="next-steps"></a>További lépések

- Ismerje meg, a TDE-Védőhöz, egy kiszolgáló biztonsági követelmények ahhoz, hogy rotálása: [elforgatása a transzparens adattitkosítási védelmi modulra vonatkozó PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Esetén a biztonsági kockázatot jelent, megtudhatja, hogyan távolítsa el a potenciálisan veszélyeztetett TDE-Védőhöz: [egy esetleg feltört kulcs eltávolítására](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Parancssori felület előfeltételei

- Azure-előfizetés és a lehet az előfizetés-rendszergazda.
- [Opcionális de javasolt] Rendelkezik egy hardveres biztonsági modul (HSM) vagy a helyi kulcs létrehozásához a TDE-Védőhöz megosztottkulcs-anyag helyi másolatát tárolja.
- Parancssori felület 2.0-s vagy újabb verziója. Telepítse a legújabb verziót, és csatlakozzon az Azure-előfizetéshez, lásd: [telepítése és konfigurálása az Azure többplatformos parancssori felület 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Hozzon létre egy Azure Key Vault és a kulcs TDE használatára.
   - [CLI 2.0 használatával a Key Vault felügyelete](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2)
   - [Egy hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasítások](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - A key vaultban kell rendelkeznie a TDE használható a következő tulajdonság:
   - [helyreállítható törlés](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
   - [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli) 
- A kulcs TDE használható a következő attribútumokkal kell rendelkeznie:
   - Lejárati dátum nélküli
   - Nincs letiltva
   - Sikerült elvégezni *első*, *kulcs becsomagolása*, *kulcs kicsomagolása* műveletek
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>1. lépés Hozzon létre egy kiszolgálót és a egy Azure AD identity rendelhet hozzá a kiszolgálóhoz
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2. lépés A kiszolgálóhoz a Key Vault-engedélyek megadása
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3. lépés A Key Vault-kulcs hozzáadása a kiszolgálóhoz, és állítsa be a TDE-Védőhöz
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> A kulcstároló nevét és a kulcs nevét együttes hossza nem lehet 94 karakternél.
> 

>[!Tip]
>Példa kulcsazonosító a Key Vaultból: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>4. lépés Kapcsolja be a TDE 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

Az adatbázis, sem az adattárházra már TDE engedélyezve van a titkosítási kulcsot a Key Vaultban.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5. lépés Ellenőrizze a titkosítási állapot és a titkosítás tevékenység

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>SQL-CLI referenciák

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

