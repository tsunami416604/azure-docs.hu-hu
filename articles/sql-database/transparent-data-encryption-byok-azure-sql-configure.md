---
title: 'PowerShell és parancssori felület: SQL-TDE engedélyezése – Azure Key Vault – saját kulcs használata – Azure SQL Database | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL Database és egy adattárházat, hogy a PowerShell vagy a parancssori felület használatával megkezdje a titkosítást a transzparens adattitkosítás (TDE) használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: f191a9f589f54bdd657c017060f501b176a8647d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596717"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell és parancssori felület: Az ügyfél által felügyelt kulccsal rendelkező transzparens adattitkosítás engedélyezése Azure Key Vault

Ez a cikk bemutatja, hogyan használhatja a Azure Key Vault for transzparens adattitkosítás (TDE) kulcsát SQL Database vagy adattárházban. Ha többet szeretne megtudni a Azure Key Vault Integration-Bring Your Own Key (BYOK) támogatással rendelkező TDE, látogasson el a [TDE felhasználó által felügyelt kulcsokra a Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>A PowerShell előfeltételei

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

- Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
- [Ajánlott, de nem kötelező] Hardveres biztonsági modult (HSM) vagy helyi kulcstárolót kell létrehoznia a TDE-védő kulcsfontosságú anyagának helyi másolatának létrehozásához.
- A Azure PowerShell telepítése és futtatása szükséges. 
- Hozzon létre egy Azure Key Vault és egy kulcsot a TDE használatához.
  - [PowerShell-utasítások a Key Vaultból](../key-vault/quick-create-powershell.md)
  - [Útmutató a hardveres biztonsági modul (HSM) és a Key Vault használatához](../key-vault/key-vault-hsm-protected-keys.md)
    - A Key vaultnak a következő tulajdonságot kell használnia a TDE:
  - a védelem eltávolítása és [törlése](../key-vault/key-vault-ovw-soft-delete.md)
  - [A Key Vault helyreállítható törlés funkciójának használata PowerShell-lel](../key-vault/key-vault-soft-delete-powershell.md) 
- A kulcsnak a következő attribútumokkal kell rendelkeznie a TDE való használathoz:
   - Nincs lejárati dátum
   - Nincs letiltva
   - Képes a *Get*, a *wrap Key*, a *dewrap Key* Operations művelet végrehajtására

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>1\.lépés Azure AD-identitás kiosztása a kiszolgálóhoz 

Ha rendelkezik meglévő kiszolgálóval, az alábbi paranccsal adhat hozzá Azure AD-identitást a kiszolgálóhoz:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Ha kiszolgálót hoz létre, használja a [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) parancsmagot a tag-Identity paranccsal az Azure ad-identitás hozzáadásához a kiszolgáló létrehozásakor:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2\.lépés Key Vault engedélyek megadása a kiszolgálónak

A [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag használatával biztosíthatja a kiszolgáló hozzáférését a Key vaulthoz, mielőtt a TDE-hoz tartozó kulcsot használ.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3\. lépés. Adja hozzá a Key Vault kulcsot a kiszolgálóhoz, és állítsa be a TDE-védőt.


- A Key Vault AZONOSÍTÓjának lekéréséhez használja a [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) parancsmagot.
- Az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) parancsmag használatával adja hozzá a kulcsot a Key Vault a-kiszolgálóhoz.
- A [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmag használatával állítsa be a kulcsot TDE-védőként az összes kiszolgálói erőforráshoz.
- A [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával ellenőrizze, hogy a TDE-védő a kívánt módon lett-e konfigurálva.

> [!Note]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.
> 

>[!Tip]
>Példa a Key Vault KeyId: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>4\. lépés. TDE bekapcsolása 

A TDE bekapcsolásához használja a [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmagot.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Most, hogy az adatbázis vagy az adattárház TDE engedélyezve van a Key Vaultban lévő titkosítási kulccsal.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5\. lépés. A titkosítási állapot és a titkosítási tevékenység keresése

A [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) használatával lekérheti a titkosítási állapotot és a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) , és megtekintheti az adatbázis vagy az adatraktár titkosítási folyamatát.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Egyéb hasznos PowerShell-parancsmagok

- A TDE kikapcsolásához használja a [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) parancsmagot.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- A [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) parancsmag használatával adja vissza a kiszolgálóhoz hozzáadott Key Vault kulcsok listáját.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- A [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) használatával távolítson el egy Key Vault kulcsot a kiszolgálóról.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Hibaelhárítás

Ha probléma merül fel, ellenőrizze a következőket:
- Ha a kulcstároló nem található, győződjön meg róla, hogy a megfelelő előfizetést használja a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmaggal.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Ha az új kulcs nem adható hozzá a kiszolgálóhoz, vagy az új kulcs nem frissíthető TDE-védőként, ellenőrizze a következőket:
   - A kulcs nem rendelkezhet lejárati dátummal
   - A kulcsnak engedélyezve kell lennie a *Get*, a *wrap Key*és a dewrap *Key* műveletnek.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan forgathatja el egy kiszolgáló TDE-védelmezőjét a biztonsági követelményeknek való megfelelés érdekében: [Forgassa el a transzparens adattitkosítás-védőt a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Biztonsági kockázat esetén Ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: [Egy potenciálisan feltört kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>A CLI előfeltételei

- Rendelkeznie kell egy Azure-előfizetéssel, és rendszergazdának kell lennie az előfizetésben.
- [Ajánlott, de nem kötelező] Hardveres biztonsági modult (HSM) vagy helyi kulcstárolót kell létrehoznia a TDE-védő kulcsfontosságú anyagának helyi másolatának létrehozásához.
- Parancssori felület 2,0-es vagy újabb verziója. A legújabb verzió telepítéséhez és az Azure-előfizetéshez való kapcsolódáshoz lásd: [Az Azure többplatformos parancssori felületének telepítése és konfigurálása (2,0](https://docs.microsoft.com/cli/azure/install-azure-cli)). 
- Hozzon létre egy Azure Key Vault és egy kulcsot a TDE használatához.
  - [Key Vault kezelése a CLI 2,0 használatával](../key-vault/key-vault-manage-with-cli2.md)
  - [Útmutató a hardveres biztonsági modul (HSM) és a Key Vault használatához](../key-vault/key-vault-hsm-protected-keys.md)
    - A Key vaultnak a következő tulajdonságot kell használnia a TDE:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [A Key Vault helyreállítható törlés funkciójának használata parancssori felülettel](../key-vault/key-vault-soft-delete-cli.md) 
- A kulcsnak a következő attribútumokkal kell rendelkeznie a TDE való használathoz:
   - Nincs lejárati dátum
   - Nincs letiltva
   - Képes a *Get*, a *wrap Key*, a *dewrap Key* Operations művelet végrehajtására
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>1\.lépés Kiszolgáló létrehozása Azure AD-identitással
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Tartsa meg a "principalID" a kiszolgáló létrehozásával, a Key Vault-engedélyek hozzárendeléséhez használt objektumazonosító a következő lépésben
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>2\.lépés Key Vault engedélyek megadása a logikai SQL-kiszolgálónak
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Tartsa meg az új kulcs kulcs-URI-JÁT vagy keyID a következő lépéshez, például: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3\. lépés. Adja hozzá a Key Vault kulcsot a kiszolgálóhoz, és állítsa be a TDE-védőt.
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.
> 

  
## <a name="step-4-turn-on-tde"></a>4\. lépés. TDE bekapcsolása 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Most, hogy az adatbázis vagy az adattárház TDE van engedélyezve a felhasználó által felügyelt titkosítási kulccsal Azure Key Vaultban.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5\. lépés. A titkosítási állapot és a titkosítási tevékenység keresése

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL CLI-hivatkozások

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

