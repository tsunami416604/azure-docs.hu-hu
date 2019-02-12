---
title: PowerShell - rotálása TDE-védőhöz – Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogyan elforgatása a transzparens adattitkosítás (TDE) védő egy Azure SQL Serverhez.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 12/06/2018
ms.openlocfilehash: 45cd4e884530836d515e0c6cce8a6fc9be109d88
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992007"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>A PowerShell-lel transzparens adattitkosítási (TDE) védő elforgatása

Ez a cikk bemutatja egy Azure SQL Server használata az Azure Key vault a TDE-védőhöz kulcsrotálás. Elforgatás egy Azure SQL server TDE védőt eszközökkel vált egy új aszimmetrikus kulcs, amely védelmet nyújt a kiszolgálón lévő adatbázisokhoz. Kulcsrotálás egy online művelet, és néhány másodperc alatt befejezni, mert ez csak visszafejti, majd újból titkosítja az adatbázis szolgáltatásadat-titkosítási kulcs, nem a teljes adatbázist csak vesz igénybe.

Ez az útmutató bemutatja a kiszolgálón a TDE-védőhöz elforgatása két alternatívája közül választhat.

> [!NOTE]
> SQL Data Warehouse felfüggesztése előtt kulcsrotálás kell folytatni.
>

> [!IMPORTANT]
> **Ehhez törölje** korábbi verzióiban a kulcsot egy kulcsváltás után.  Kulcsok átváltva a rendszer, amikor bizonyos még mindig titkosítja az adatokat a korábbi kulcsok, például a régebbi adatbázis biztonsági mentése. 
>

## <a name="prerequisites"></a>Előfeltételek

- Ez az útmutató feltételezi, hogy már használja egy kulcs Azure Key vault a TDE-védőhöz, egy Azure SQL Database vagy Data warehouse-bA. Lásd: [transzparens adattitkosítás az Azure Key Vault-integráció - BYOK-támogatással](transparent-data-encryption-byok-azure-sql.md).
- Rendelkeznie kell Azure PowerShell-lel 3.7.0 verzió vagy újabb telepítve és fut. 
- [Opcionális de javasolt] Hozzon létre egy hardveres biztonsági modul (HSM) esetében a TDE-védőhöz megosztottkulcs-anyag vagy helyi kulcs első tárolja, és a megosztottkulcs-anyag importálja az Azure Key Vaultba. Kövesse a [egy hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasítások](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) további.

## <a name="manual-key-rotation"></a>Manuális kulcsrotálás

Manuális kulcsrotálás használja a [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey), és [Set-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) Adjon hozzá egy teljesen új kulcsot, amely lehet egy új kulcsnév vagy akár egy másik a key vault-parancsmagokat. Ez a megközelítés különböző kulcstartókhoz támogatja a magas rendelkezésre állás és a geo-Dr-beli forgatókönyvek ugyanazzal a kulccsal hozzáadását támogatja.

>[!NOTE]
>A kulcstároló nevét és a kulcs nevét együttes hossza nem lehet 94 karakternél.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Egyéb hasznos PowerShell-parancsmagok

- A TDE-védőhöz a Microsoft által felügyelt BYOK módra vált, használja a [Set-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Váltson a TDE-védőhöz BYOK módban, a Microsoft által felügyelt, használja a [Set-azurermsqlservertransparentdataencryptionprotector parancsmag](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>További lépések

- Esetén a biztonsági kockázatot jelent megtudhatja, hogyan távolítsa el a potenciálisan veszélyeztetett TDE-védőhöz: [Egy esetleg feltört kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Ismerkedés az Azure Key Vault-integráció és a Bring Your Own Key a TDE támogatása: [Kapcsolja be a TDE Key vault PowerShell-lel a saját kulcs használata](transparent-data-encryption-byok-azure-sql-configure.md)
