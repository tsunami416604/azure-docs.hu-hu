---
title: PowerShell – TDE-védő elforgatása – Azure SQL Database | Microsoft Docs
description: Megtudhatja, hogyan forgathatja el az Azure SQL Server transzparens adattitkosítás (TDE) oltalmazóját.
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
ms.openlocfilehash: 464ea73d9b3d7116205377600ffccee13a9e2dcb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566034"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>A transzparens adattitkosítás (TDE)-védő elforgatása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan használható az Azure SQL Server TDE-védővel való elforgatása Azure Key Vaultból. Az Azure SQL Server TDE-védő elforgatása azt jelenti, hogy a kiszolgálón lévő adatbázisokat védő új aszimmetrikus kulcsra vált. A kulcs elforgatása egy online művelet, amely csak néhány másodpercig tart, mivel ez csak a teljes adatbázis visszafejtését és újratitkosítását végzi el.

Ez az útmutató két lehetőséget tárgyal a TDE-védő a kiszolgálón való elforgatására.

> [!NOTE]
> A szüneteltetett SQL Data Warehouse a kulcsok elforgatása előtt folytatni kell.
>

> [!IMPORTANT]
> Egy rollover után ne **törölje** a kulcs korábbi verzióit.  A kulcsok átadásakor a rendszer bizonyos adatokat továbbra is titkosít az előző kulcsokkal, például a régebbi adatbázis-biztonsági mentésekkel. 
>

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

- Ez a útmutató azt feltételezi, hogy már használ egy kulcsot Azure Key Vault TDE-védőként egy Azure SQL Database vagy adattárházhoz. Lásd: [TRANSZPARENS ADATTITKOSÍTÁS BYOK](transparent-data-encryption-byok-azure-sql.md)-támogatással.
- A Azure PowerShell telepítése és futtatása szükséges.
- [Ajánlott, de nem kötelező] Először a hardveres biztonsági modulban (HSM) vagy a helyi Key Store-ban hozza létre a TDE-védő fő anyagát, és importálja Azure Key Vault. További információért kövesse a [hardveres biztonsági modul (HSM) használatának utasításait és Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) .

## <a name="manual-key-rotation"></a>Kézi kulcs elforgatása

A kézi kulcs rotációja az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), az [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)és a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot használja egy teljesen új kulcs hozzáadásához, amely egy új kulcs neve, vagy akár egy másik kulcs is lehet. Vault. Ezzel a módszerrel a magas rendelkezésre állás és a Geo-Dr forgatókönyvek támogatásához ugyanazt a kulcsot kell hozzáadni a különböző kulcstartóhoz.

>[!NOTE]
>A Key Vault neve és a kulcsnév együttes hossza nem lehet hosszabb 94 karakternél.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Egyéb hasznos PowerShell-parancsmagok

- Ha a TDE-védőt a Microsoft által felügyelt BYOK módba szeretné váltani, használja a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Ha a TDE-védőt a BYOK módból a Microsoft által felügyelt értékre szeretné váltani, használja a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagot.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>További lépések

- Biztonsági kockázat esetén Ismerje meg, hogyan távolíthat el egy potenciálisan sérült TDE-védőt: [Potenciálisan feltört kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Ismerkedés a Azure Key Vault integrációval és a TDE Bring Your Own Key támogatásával: [A TDE bekapcsolása saját kulcs használatával Key Vault a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md)
