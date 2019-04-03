---
title: PowerShell - távolítsa el a TDE-védőhöz – Azure SQL Database |} A Microsoft Docs
description: Az útmutató vonatkozó egy esetleg feltört TDE-védőhöz egy Azure SQL Database vagy Data Warehouse használatával a TDE Bring YOur Own Key (BYOK) támogatásával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 73fcb2753fa7eb15f34b04ddc5bb0b55c4636623
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847811"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Távolítsa el a transzparens adattitkosítás (TDE) védőelem a PowerShell használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

- Azure-előfizetés és a lehet az előfizetés-rendszergazda
- Azure PowerShell telepítenie és futtatnia kell rendelkeznie. 
- Ez az útmutató feltételezi, hogy már használja egy kulcs Azure Key vault a TDE-védőhöz, egy Azure SQL Database vagy Data warehouse-bA. Lásd: [transzparens adattitkosítás BYOK-támogatással](transparent-data-encryption-byok-azure-sql.md) további.

## <a name="overview"></a>Áttekintés

Ez az útmutató azt ismerteti, hogyan reagáljon a potenciálisan veszélyeztetett TDE-védőhöz egy Azure SQL Database vagy az adattárház, amely az ügyfél által felügyelt kulcsok Azure Key Vault - Bring Your Own Key (BYOK) támogatása a TDE használ. TDE BYOK-támogatással kapcsolatos további információkért tekintse meg a [áttekintőlapján](transparent-data-encryption-byok-azure-sql.md). 

Az alábbi eljárások csak szélsőséges esetben vagy tesztelési környezetben kell végrehajtani. Tekintse át alaposan útmutatója, mint aktívan használt TDE törlése az Azure Key Vault protectors eredményezhet **adatvesztés**. 

Ha egy kulcsot minden eddiginél gyanús legyen feltörni, úgy, hogy egy szolgáltatás vagy a felhasználó nem volt engedélyezett a kulcs eléréséhez, célszerű kulcs törlése.

Ne feledje, hogy egyszer a TDE-védőhöz törlődik a Key vaultban **titkosított adatbázis a kiszolgáló felé irányuló összes kapcsolatot le vannak tiltva, és ezeknek az adatbázisoknak kapcsolat nélküli üzemmódban vannak, és 24 órán belül első eldobott**. A feltört kulccsal titkosított régi biztonsági másolatok már nem érhetőek el.

Ez az útmutató két módszer attól függően, a kívánt eredményt keresztül haladnak, az incidensek megoldásához után:

- Az Azure SQL adatbázisokat / Data Warehouses **érhető el**
- Hogy az Azure SQL-adatbázisok / Data Warehouses **nem érhető el**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Elérhető-e tartani a titkosított erőforrások

1. Hozzon létre egy [új kulcsot a Key Vaultban](/powershell/module/az.keyvault/add-azkeyvaultkey). Ellenőrizze, hogy ez az új kulcs a potenciálisan veszélyeztetett TDE-védőhöz, a különálló a kulcstartóban található jön létre, mivel hozzáférés-vezérlés a tároló szinten van kiépítve.
2. Adja meg az új kulcsot a kiszolgálóra történő a [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) és [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagok és a kiszolgáló új TDE-védőhöz szerint módosítsa azt.

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

3. Győződjön meg arról, hogy a kiszolgáló és az esetleges replikákat frissítve, az új a TDE-védőhöz használatával a [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) parancsmagot. 

   >[!NOTE]
   > Eltarthat néhány percig, az új TDE-védőhöz, adatbázisok és a másodlagos adatbázisokat a kiszolgálóhoz való propagálása.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Igénybe vehet egy [az új kulcs biztonsági mentési](/powershell/module/az.keyvault/backup-azkeyvaultkey) a Key Vaultban.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. A feltört kulcs törlése a Key Vault használatával a [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) parancsmagot. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Kulcs visszaállítása a Key Vault használatával a jövőben a [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) parancsmagot:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Hogy a titkosított erőforrások nem érhető el

1. Dobja el az adatbázisok által a vélhetően feltört kulcs titkosított.

   Az adatbázis és naplófájlok fájlok automatikusan biztonsági másolat készül, így időponthoz visszaállítás, az adatbázisban végezhető bármikor (feltéve, akkor adja meg a kulcsot). Kell egy aktív TDE-védőhöz, akár 10 percet a legutóbbi tranzakciók esetleges adatvesztés elkerülése érdekében a törlés előtt dobja el az adatbázisok. 
2. Készítsen biztonsági másolatot a TDE-védőhöz, a Key Vault-kulcs adatai.
3. Távolítsa el a potenciálisan veszélyeztetett kulcsot a Key Vaultból

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogy a TDE-védőhöz, egy kiszolgáló biztonsági követelmények ahhoz, hogy rotálása: [A transzparens adattitkosítási védelmi modulra vonatkozó PowerShell használatával elforgatása](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Első lépések a Bring Your Own Key TDE támogatása: [Kapcsolja be a TDE Key vault PowerShell-lel a saját kulcs használata](transparent-data-encryption-byok-azure-sql-configure.md)
