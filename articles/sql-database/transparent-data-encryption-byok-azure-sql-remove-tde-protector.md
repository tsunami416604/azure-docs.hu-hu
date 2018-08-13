---
title: PowerShell - távolítsa el a TDE-védőhöz – Azure SQL Database |} A Microsoft Docs
description: Az útmutató vonatkozó egy esetleg feltört TDE-védőhöz egy Azure SQL Database vagy Data Warehouse használatával a TDE Bring YOur Own Key (BYOK) támogatásával.
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043847"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Távolítsa el a transzparens adattitkosítás (TDE) védőelem a PowerShell használatával
## <a name="prerequisites"></a>Előfeltételek
- Azure-előfizetés és a lehet az előfizetés-rendszergazda
- Rendelkeznie kell Azure PowerShell 4.2.0-s verzió vagy újabb telepítése és futtatása. 
- Ez az útmutató feltételezi, hogy már használja egy kulcs Azure Key vault a TDE-védőhöz, egy Azure SQL Database vagy Data warehouse-bA. Lásd: [transzparens adattitkosítás BYOK-támogatással](transparent-data-encryption-byok-azure-sql.md) további.

## <a name="overview"></a>Áttekintés
Ez az útmutató azt ismerteti, hogyan reagáljon a potenciálisan veszélyeztetett TDE-védőhöz egy Azure SQL Database vagy az adattárház, amely a TDE támogatással Bring Your Own Key (BYOK) használ. TDE BYOK-támogatással kapcsolatos további információkért tekintse meg a [áttekintőlapján](transparent-data-encryption-byok-azure-sql.md). 

Az alábbi eljárások csak szélsőséges esetben vagy tesztelési környezetben kell végrehajtani. Tekintse át alaposan útmutatója, mint aktívan használt TDE törlése az Azure Key Vault protectors eredményezhet **adatvesztés**. 

Ha egy kulcsot minden eddiginél gyanús legyen feltörni, úgy, hogy egy szolgáltatás vagy a felhasználó nem volt engedélyezett a kulcs eléréséhez, célszerű kulcs törlése.

Ne feledje, hogy egyszer a TDE-védőhöz törlődik a Key vaultban **titkosított adatbázis a kiszolgáló felé irányuló összes kapcsolatot le vannak tiltva, és ezeknek az adatbázisoknak kapcsolat nélküli üzemmódban vannak, és 24 órán belül első eldobott**. A feltört kulccsal titkosított régi biztonsági másolatok már nem érhetőek el.

Ez az útmutató két módszer attól függően, a kívánt eredményt keresztül haladnak, az incidensek megoldásához után:
- Az Azure SQL adatbázisokat / Data Warehouses **érhető el**
- Hogy az Azure SQL Database adatbázisok / Data Warehouses **nem érhető el**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Elérhető-e tartani a titkosított erőforrások
1. Hozzon létre egy [új kulcsot a Key Vaultban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Ellenőrizze, hogy ez az új kulcs a potenciálisan veszélyeztetett TDE-védőhöz, a különálló a kulcstartóban található jön létre, mivel hozzáférés-vezérlés a tároló szinten van kiépítve. 
2. Adja meg az új kulcsot a kiszolgálóra történő a [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) és [Set-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) parancsmagok és a kiszolgáló új TDE-védőhöz szerint módosítsa azt.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Győződjön meg arról, hogy a kiszolgáló és az esetleges replikákat frissítve, az új a TDE-védőhöz használatával a [Get-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) parancsmagot. 

   >[!NOTE]
   > Eltarthat néhány percig, az új TDE-védőhöz, adatbázisok és a másodlagos adatbázisokat a kiszolgálóhoz való propagálása.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Igénybe vehet egy [az új kulcs biztonsági mentési](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) a Key Vaultban.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. A feltört kulcs törlése a Key Vault használatával a [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) parancsmagot. 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Kulcs visszaállítása a Key Vault használatával a jövőben a [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) parancsmagot:
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Hogy a titkosított erőforrások nem érhető el
1. Dobja el az adatbázisok által a vélhetően feltört kulcs titkosított.
Az adatbázis és naplófájlok fájlok automatikusan biztonsági másolat készül, így időponthoz visszaállítás, az adatbázisban végezhető bármikor (feltéve, akkor adja meg a kulcsot). Kell egy aktív TDE-védőhöz, akár 10 percet a legutóbbi tranzakciók esetleges adatvesztés elkerülése érdekében a törlés előtt dobja el az adatbázisok. 
2. Készítsen biztonsági másolatot a TDE-védőhöz, a Key Vault-kulcs adatai.
3. Távolítsa el a potenciálisan veszélyeztetett kulcsot a Key Vaultból

## <a name="next-steps"></a>További lépések

- Ismerje meg, a TDE-védőhöz, egy kiszolgáló biztonsági követelmények ahhoz, hogy rotálása: [elforgatása a transzparens adattitkosítási védelmi modulra vonatkozó PowerShell használatával](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- Ismerkedés a Bring Your Own Key TDE támogatása: [kapcsolja be a TDE Key vault PowerShell-lel a saját kulcs használata](transparent-data-encryption-byok-azure-sql-configure.md)
