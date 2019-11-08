---
title: TDE-védő eltávolítása – PowerShell
description: 'Útmutató: a TDE-t használó, a saját kulcsú (BYOK) támogatással rendelkező, a TDE által feltört, potenciálisan sérült adatvédelmet segítő Azure SQL Database eszközre való reagálás.'
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: df1bf5a53cd5c49465acbe363c71a4a316cd2cc9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820792"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Transzparens adattitkosítás (TDE)-védő eltávolítása a PowerShell használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

- Azure-előfizetéssel kell rendelkeznie, és az előfizetés rendszergazdájának kell lennie
- A Azure PowerShell telepítése és futtatása szükséges. 
- Ez a útmutató azt feltételezi, hogy már használ egy kulcsot Azure Key Vault TDE-védőként egy Azure SQL Database vagy adattárházhoz. További információ: [TRANSZPARENS ADATTITKOSÍTÁS BYOK-támogatással](transparent-data-encryption-byok-azure-sql.md) .

## <a name="overview"></a>Áttekintés

Ez az útmutató azt ismerteti, hogyan lehet reagálni egy potenciálisan sérült TDE-védőre egy olyan Azure SQL Database vagy adatraktár esetében, amely Azure Key Vault-Bring Your Own Key (BYOK) támogatásban lévő ügyfél által felügyelt kulcsokkal TDE használ. Ha többet szeretne megtudni a TDE BYOK-támogatásáról, tekintse meg az [Áttekintés oldalt](transparent-data-encryption-byok-azure-sql.md). 

A következő eljárásokat csak szélsőséges esetekben vagy tesztelési környezetekben lehet elvégezni. Olvassa el figyelmesen az útmutató útmutatását, mivel a Azure Key Vault aktívan használt TDE-védelmi **adatainak törlése adatvesztést**okozhat. 

Ha egy kulcs gyanúja fennáll, hogy egy adott szolgáltatás vagy felhasználó jogosulatlanul hozzáfért a kulcshoz, akkor érdemes törölni a kulcsot.

Ne feledje, hogy amint törli a TDE-védőt a Key Vaultban, a **rendszer letiltja a kiszolgáló alatt lévő titkosított adatbázisokhoz való kapcsolódást, és 24 órán belül megszakítja az adatbázisokat**. A feltört kulccsal titkosított régi biztonsági másolatok már nem érhetők el.

Az alábbi lépések azt ismertetik, hogyan ellenőrizheti a TDE Protector ujjlenyomatai megfelelnek egy adott adatbázis virtuális naplófájljai (VLF) által még használatban. Az adatbázis jelenlegi TDE-oltalmazójának ujjlenyomata, és az adatbázis-azonosító a következő futtatásával található: SELECT [database_id],       [encryption_state], [encryptor_type],/*aszimmetrikus kulcs: AKV, tanúsítvány: szolgáltatás által felügyelt kulcsok*/[ encryptor_thumbprint], a következő helyről: [sys]. [dm_database_encryption_keys] 
 
A következő lekérdezés a VLFs és a titkosító megfelelő ujjlenyomatai megfelelnek adja vissza. A különböző ujjlenyomatok a Azure Key Vault (AKV) különböző kulcsaira utalnak: SELECT * FROM sys. dm_db_log_info (database_id) 

A Get-AzureRmSqlServerKeyVaultKey PowerShell-parancs megadja a lekérdezésben használt TDE-védő ujjlenyomatát, így láthatja, hogy mely kulcsokat kell megőrizni, és mely kulcsokat kell törölni a AKV-ben. Csak az adatbázis által már nem használt kulcsokat lehet biztonságosan törölni Azure Key Vaultból.

Ez a útmutató az incidens válasza után a kívánt eredménytől függően két megközelítést mutat be:

- Az Azure SQL Database-adatbázisok/adattárházak **elérhetőségének** megőrzése
- Az Azure SQL Database-adatbázisok/adattárházak **elérhetetlenné** tétele

## <a name="to-keep-the-encrypted-resources-accessible"></a>A titkosított erőforrások elérhetőségének megőrzése

1. Hozzon létre egy [új kulcsot a Key Vaultban](/powershell/module/az.keyvault/add-azkeyvaultkey). Győződjön meg arról, hogy az új kulcs egy különálló kulcstartóban jön létre a potenciálisan feltört TDE-védőtől, mert a hozzáférés-vezérlést a tároló szintjén kell kiépíteni.
2. Adja hozzá az új kulcsot a kiszolgálóhoz az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) és a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmaggal, és frissítse azt a kiszolgáló új TDE-oltalmazójának használatával.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Győződjön meg arról, hogy a kiszolgáló és a replikák frissítve lettek az új TDE-védőhöz a [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmag használatával. 

   >[!NOTE]
   > Eltarthat néhány percig, amíg az új TDE-védő propagálni képes a kiszolgáló által használt összes adatbázisba és másodlagos adatbázisba.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Készítsen [biztonsági másolatot az új kulcsról](/powershell/module/az.keyvault/backup-azkeyvaultkey) Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Törölje a feltört kulcsot Key Vault a [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) parancsmag használatával. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. A kulcs visszaállítása Key Vaultre a későbbiekben a [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) parancsmag használatával:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>A titkosított erőforrások elérhetetlenné tétele

1. A potenciálisan feltört kulccsal titkosított adatbázisok eldobása.

   A rendszer automatikusan biztonsági másolatot készít az adatbázisról és a naplófájlokról, így az adatbázis adott időpontban történő visszaállítása bármikor elvégezhető (feltéve, hogy megadja a kulcsot). Az adatbázisokat el kell dobni az aktív TDE-védő törlése előtt, hogy megakadályozza a legutóbbi tranzakciók akár 10 perces elvesztését. 
2. Biztonsági másolat készítése a TDE-védő fő anyagáról Key Vaultban.
3. A potenciálisan feltört kulcs eltávolítása Key Vault

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan forgathatja el egy kiszolgáló TDE-védelmezőjét a biztonsági követelmények teljesítése érdekében: [a transzparens adattitkosítás Protector elforgatása a PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Ismerkedés a Bring Your Own Key támogatásával a TDE-hez: a [TDE bekapcsolása a saját kulcsával Key Vault a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md)
