---
title: TDE-védő eltávolítása – PowerShell
description: Útmutató útmutató egy potenciálisan sérült TDE-védő megválaszolására egy Azure SQL-adatbázis vagy adattárház számára a TDE használatával, a Bring YOur Own Key (BYOK) támogatással.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067212"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Transzparens adattitkosítási (TDE) védő eltávolítása a PowerShell használatával

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetéssel kell rendelkeznie, és rendszergazdanak kell lennie az adott előfizetésben.
- Az Azure PowerShell telepítve és fut.
- Ez az útmutató feltételezi, hogy már használ egy kulcsot az Azure Key Vault egy Azure SQL-adatbázis vagy adattárház TDE-védőként. További információ: [Átlátszó adattitkosítás byok-támogatással.](transparent-data-encryption-byok-azure-sql.md)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

 Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. A konkrét parancsmagok: [AzureRM.Sql.](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A telepítésről az [Azure CLI telepítése](/cli/azure/install-azure-cli)című témakörben található.

* * *

## <a name="overview"></a>Áttekintés

Ez az útmutató bemutatja, hogyan reagálhat egy potenciálisan feltört TDE-védőre egy olyan Azure SQL-adatbázis vagy adattárház esetében, amely a TDE-t az azure-kulcstárolóban az ügyfél által felügyelt kulcsokkal használja – saját kulcs (BYOK) támogatáshoz. Ha többet szeretne megtudni a BYOK TDE-támogatásáról, olvassa el az [áttekintő oldalt](transparent-data-encryption-byok-azure-sql.md).

A következő eljárásokat csak szélsőséges esetekben vagy vizsgálati környezetben szabad elvégezni. Tekintse át figyelmesen az útmutatót, mert az aktívan használt TDE-védők azure-beli Key Vaultból való törlése **az adatbázis elérhetetlenségét eredményezi.**

Ha egy kulcs valaha is gyanítható, hogy veszélybe, például egy szolgáltatás vagy felhasználó jogosulatlan hozzáférést a kulcshoz, akkor a legjobb, ha törli a kulcsot.

Ne feledje, hogy a TDE-védő törlése a Key Vaultban, legfeljebb 10 perc alatt az összes titkosított adatbázis elkezdi megtagadni az összes kapcsolatot a megfelelő hibaüzenettel, és az állapotát Nem érhető el állapotra [módosítja.](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector)

A következő lépések ismertetik, hogyan ellenőrizheti a TDE Protector ujjlenyomatait, amelyeket egy adott adatbázis virtuális naplófájljai (VLF) még mindig használnak.
Az adatbázis aktuális TDE-védőjének ujjlenyomata és az adatbázis-azonosító a következő futtatásával érhető el:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

A következő lekérdezés visszaadja a VLF-eket és a megfelelő ujjlenyomatokat. Minden egyes különböző ujjlenyomat az Azure Key Vault (AKV) különböző kulcsaira utal:

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A **Get-AzureRmSqlServerKeyVaultKey** PowerShell parancs a lekérdezésben használt TDE Protector ujjlenyomatát biztosítja, így láthatja, hogy mely kulcsokat kell megtartani, és mely kulcsokat kell törölni az AKV-ban. Csak az adatbázis által már nem használt kulcsok biztonságosan törölhetők az Azure Key Vaultból.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A PowerShell parancs **az sql server kulcs show** biztosítja az ujjlenyomata a TDE Protector használt a lekérdezésben, így láthatja, hogy mely kulcsokat kell tartani, és mely kulcsokat törölni a AKV. Csak az adatbázis által már nem használt kulcsok biztonságosan törölhetők az Azure Key Vaultból.

* * *

Ez az útmutató két megközelítést vezet át az incidensre adott válasz utáni kívánt eredménytől függően:

- Az Azure SQL-adatbázisok / Adattárházak **akadálymentesen** való elérhetővé tétele
- Az Azure SQL-adatbázisok / Adattárházak **elérhetetlenné** tétele

## <a name="to-keep-the-encrypted-resources-accessible"></a>A titkosított erőforrások akadálymentesen tartása

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

1. Hozzon létre egy [új kulcsot a Key Vaultban.](/powershell/module/az.keyvault/add-azkeyvaultkey) Győződjön meg arról, hogy ez az új kulcs egy külön kulcstartóban jön létre a potenciálisan sérült TDE-védőtől, mivel a hozzáférés-vezérlés egy tároló szinten van kiépítve.

2. Adja hozzá az új kulcsot a kiszolgálóhoz az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) és a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagokkal, és frissítse a kiszolgáló új TDE-védőjeként.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Győződjön meg arról, hogy a kiszolgáló és a replikák a [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával frissültek az új TDE-védőre.

   > [!NOTE]
   > Eltarthat néhány percig, amíg az új TDE-védő a kiszolgáló alatt lévő összes adatbázisra és másodlagos adatbázisra propagál.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Készítsen [biztonsági másolatot az új kulcsról a](/powershell/module/az.keyvault/backup-azkeyvaultkey) Key Vaultban.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Törölje a feltört kulcsot a Key Vault ból az [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) parancsmag használatával.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. A [KeyVaultKey-parancsmag](/powershell/module/az.keyvault/restore-azkeyvaultkey) visszaállítása segítségével a key vault kulcs ának jövőbeli visszaállítása:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A parancsra vonatkozó hivatkozásaz [Azure CLI keyvault](/cli/azure/keyvault/key)című témakörben látható.

1. Hozzon létre egy [új kulcsot a Key Vaultban.](/cli/azure/keyvault/key#az-keyvault-key-create) Győződjön meg arról, hogy ez az új kulcs egy külön kulcstartóban jön létre a potenciálisan sérült TDE-védőtől, mivel a hozzáférés-vezérlés egy tároló szinten van kiépítve.

2. Adja hozzá az új kulcsot a kiszolgálóhoz, és frissítse a kiszolgáló új TDE-védőjeként.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Győződjön meg arról, hogy a kiszolgáló és a kópiák frissültek az új TDE-védőre.

   > [!NOTE]
   > Eltarthat néhány percig, amíg az új TDE-védő a kiszolgáló alatt lévő összes adatbázisra és másodlagos adatbázisra propagál.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Készítsen biztonsági másolatot az új kulcsról a Key Vaultban.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Törölje a feltört kulcsot a Key Vaultból.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. A key vault kulcsának visszaállítása a jövőben.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>A titkosított erőforrások elérhetetlenné tétele

1. Dobja el a potenciálisan feltört kulccsal titkosított adatbázisokat.

   Az adatbázis- és naplófájlokról a rendszer automatikusan biztonsági másolatot készített, így az adatbázis bármikor visszaállítható (feltéve, hogy megadja a kulcsot). Az adatbázisokat el kell dobni az aktív TDE-védelem törlése előtt, hogy megelőzzük a legutóbbi tranzakciók legfeljebb 10 percnyi adatvesztését.

2. Biztonsági másolatot a Key Vault TDE-védőjének kulcsanyagáról.
3. A potenciálisan feltört kulcs eltávolítása a Key Vaultból

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan forgathatja el egy kiszolgáló TDE-védőjét a biztonsági követelményeknek való megfelelés érdekében: [Az átlátszó adattitkosítás-védő elforgatása a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Első lépések a Saját kulcs támogatás ának használata a TDE-hez: [Kapcsolja be a TDE-t a saját kulcsával a Key Vaultból a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md)
