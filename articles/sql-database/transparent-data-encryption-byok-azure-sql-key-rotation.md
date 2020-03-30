---
title: TDE-védő elforgatása – PowerShell
description: Ismerje meg, hogyan forgassa el az Átlátszó adattitkosítás (TDE) protector egy Azure SQL-kiszolgáló.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067173"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Az átlátszó adattitkosítás (TDE) védőelforgatása a PowerShell használatával

Ez a cikk ismerteti a kulcs rotációs egy Azure SQL-kiszolgáló tde-védő használatával az Azure Key Vault. Az Azure SQL-kiszolgáló TDE-védőjének elforgatása azt jelenti, hogy egy új aszimmetrikus kulcsra vált, amely védi a kiszolgálón lévő adatbázisokat. A kulcselforgatás egy online művelet, és csak néhány másodpercet vesz igénybe, mert ez csak az adatbázis adattitkosítási kulcsát fejti vissza és titkosítja újra, nem pedig a teljes adatbázist.

Ez az útmutató két lehetőséget tartalmaz a TDE-védő elforgatására a kiszolgálón.

> [!NOTE]
> A szüneteltetett SQL Data Warehouse-t a kulcselforgatások előtt újra kell indítani.

> [!IMPORTANT]
> Ne törölje a kulcs korábbi verzióit a váltás után. A kulcsok gördülékenysítésekor egyes adatok továbbra is titkosítva vannak az előző kulcsokkal, például a régebbi adatbázis-biztonsági mentések.

## <a name="prerequisites"></a>Előfeltételek

- Ez az útmutató feltételezi, hogy már használ egy kulcsot az Azure Key Vault egy Azure SQL-adatbázis vagy adattárház TDE-védőként. Lásd [Átlátszó adattitkosítás BYOK támogatással](transparent-data-encryption-byok-azure-sql.md).
- Az Azure PowerShell telepítve és fut.
- [Ajánlott, de nem kötelező] Először hozza létre a TDE-védő kulcsanyagát egy hardveres biztonsági modulban (HSM) vagy helyi kulcstárolóban, és importálja a kulcsanyagot az Azure Key Vaultba. További információért kövesse a [hardveres biztonsági modul (HSM) és a Key Vault használatára vonatkozó utasításokat.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Az Az modul telepítési útmutatását [az Azure PowerShell telepítését](/powershell/azure/install-az-ps) ismertető cikkben találja. A konkrét parancsmagok: [AzureRM.Sql.](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A telepítésről az [Azure CLI telepítése](/cli/azure/install-azure-cli)című témakörben található.

* * *

## <a name="manual-key-rotation"></a>Kézi billentyűelforgatás

A kézi kulcselforgatás a következő parancsok at használja egy teljesen új kulcs hozzáadásához, amely új kulcsnév vagy akár egy másik kulcstartó alatt lehet. Ezzel a megközelítéssel támogatja ugyanazt a kulcsot a különböző kulcstartók a magas rendelkezésre állású és geo-dr forgatókönyvek támogatása érdekében.

> [!NOTE]
> A kulcstartó nevének és kulcsnevének együttes hossza nem haladhatja meg a 94 karaktert.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Használja az [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)és [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagokat.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja az [az keyvault kulcs létrehozása](/cli/azure/keyvault/key#az-keyvault-key-create), az sql server kulcs [létrehozása](/cli/azure/sql/server/key#az-sql-server-key-create), és az SQL [server tde-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) parancsokat.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Hasznos PowerShell-parancsmagok

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

- Ha a TDE-védőt a Microsoft által kezelt módról BYOK módba szeretné váltani, használja a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagát.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Ha a TDE-védőt BYOK módról Microsoft által felügyelt módra szeretné váltani, használja a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) parancsmagát.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő példák [az SQL server tde-key set -t](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)használják.

- Ha a TDE-védőt a Microsoft által kezelt módból BYOK módba szeretné váltani,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Ha a TDE-védőt BYOK módról Microsoft által felügyelt

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>További lépések

- Biztonsági kockázat esetén ismerje meg, hogyan távolíthatja el a potenciálisan veszélyeztetett TDE-védőt: [Potenciálisan sérült kulcs eltávolítása](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Ismerkedés az Azure Key Vault-integrációval és a Saját kulcs támogatás ának a TDE-vel: [Kapcsolja be a TDE-t a saját kulcsával a Key Vaultból a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md)
