---
title: Migrate TDE certificate - managed instance
description: Migrate certificate protecting Database Encryption Key of a database with transparent Data Encryption to Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 6ef8d49ba7c9ac2c3c60197c11b9bf5936171f9e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420741"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrate certificate of TDE protected database to Azure SQL Database Managed Instance

When migrating a database protected by [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) to Azure SQL Database Managed Instance using native restore option, the corresponding certificate from the on-premises or IaaS SQL Server needs to be migrated before database restore. Ez a cikk a tanúsítványnak a felügyelt Azure SQL Database-példányra történő manuális migrálásának a folyamatát mutatja be lépésről lépésre:

> [!div class="checklist"]
> * A tanúsítvány exportálása egy személyes információcsere (.pfx) fájlba
> * Tanúsítvány kibontása a fájlból base-64 sztringbe
> * Feltöltés PowerShell-parancsmag használatával

Alternatív megoldásként használhat egy teljes körűen felügyelt szolgáltatást a TDE-vel védett adatbázis és a kapcsolódó tanúsítvány zökkenőmentes migrálásához. További információért olvassa el a cikket, amely részletesen ismerteti a [helyszíni adatbázis egy felügyelt példányra való migrálását az Azure Database Migration Service használatával](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> A migrált tanúsítvány csak a TDE-vel védett adatbázis visszaállítására használható. Soon after restore is done, the migrated certificate gets replaced by a different protector, either service-managed certificate or asymmetric key from the key vault, depending on the type of the transparent data encryption you set on the instance.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

- Telepített [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) parancssori eszköz egy helyszíni kiszolgálón vagy egy olyan számítógépen, amely hozzáfér a fájlként exportált tanúsítványhoz. A Pvk2Pfx eszköz az [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) része, amely egy önálló parancssori környezet.
- Telepített [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell), 5.0-s vagy újabb verzió.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Győződjön meg róla, hogy rendelkezik az alábbiakkal:

- Azure PowerShell module [installed and updated](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Az.Sql module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. For these cmdlets, see [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). The arguments for the commands in the Az module and in the AzureRm modules are substantially identical.

Run the following commands in PowerShell to install/update the module:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>A TDE-tanúsítvány exportálása egy személyes információcsere (.pfx) fájlba

A tanúsítvány exportálható közvetlenül az SQL Server-példányról vagy egy tanúsítványtárolóból.

### <a name="export-certificate-from-the-source-sql-server"></a>Tanúsítvány exportálása a forrásként szolgáló SQL Server-példányról

A következő lépéseket követve exportálhat tanúsítványokat az SQL Server Management Studióval, és konvertálhatja azokat pfx formátumba. A lépések során az általános *TDE_Cert* és *full_path* nevet adtuk a tanúsítványnak és a fájlneveknek. Ezeket a gyakorlatban a tényleges nevekre kell cserélni.

1. Az SSMS-ben nyisson meg egy új lekérdezési ablakot, és csatlakozzon a forrásként szolgáló SQL Server-példányhoz.

1. A következő szkripttel megjelenítheti a TDE-vel védett adatbázisok listáját, valamint lekérdezheti a migrálni kívánt adatbázist védő titkosítás tanúsítványának nevét:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![a TDE-tanúsítványok listája](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. A következő szkriptet futtatva a tanúsítványt két fájlba (.cer és .pvk) exportálhatja, megőrizve a nyilvános és a titkos kulcs adatait:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![TDE-tanúsítvány biztonsági mentése](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. A PowerShell-konzollal másolja az újonnan létrehozott fájlok tanúsítványadatait egy személyes információcsere (.pfx) fájlba a Pvk2Pfx eszköz használatával:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Tanúsítvány exportálása egy tanúsítványtárolóból

Amennyiben a tanúsítványt az SQL Server helyi számítógépének tanúsítványtárolója tartalmazza, a következő módon tudja exportálni onnan:

1. Nyissa meg a PowerShell-konzolt, és futtassa a következő parancsot a Microsoft Management Console Tanúsítványok beépülő moduljának megnyitásához:

   ```cmd
   certlm
   ```

2. A Tanúsítványok beépülő MMC-modulban bontsa ki a Személyes -> Tanúsítványok pontot a tanúsítványok listájának megtekintéséhez.

3. Kattintson a jobb gombbal a tanúsítványra, és válassza az Export… (Exportálás…) parancsot.

4. A varázsló utasításait követve exportálja a tanúsítványt és a titkos kulcsot személyes információcsere formátumba.

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Upload certificate to Azure SQL Database Managed Instance using Azure PowerShell cmdlet

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Előkészítő lépések a PowerShellben:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Ha elvégezte a szükséges előkészítő lépéseket, futtassa a következő parancsokat a base-64 kódolású tanúsítványnak a célként megadott felügyelt példányba való feltöltéséhez:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

You need to first [setup an Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) with your *.pfx* file.

1. Előkészítő lépések a PowerShellben:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Ha elvégezte a szükséges előkészítő lépéseket, futtassa a következő parancsokat a base-64 kódolású tanúsítványnak a célként megadott felügyelt példányba való feltöltéséhez:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

A tanúsítvány ezzel elérhetővé válik a megadott felügyelt példányban, és a megfelelő TDE-vel védett adatbázis biztonsági mentése sikeresen visszaállítható.

## <a name="next-steps"></a>Következő lépések

In this article, you learned how to migrate certificate protecting encryption key of database with Transparent Data Encryption, from the on-premises or IaaS SQL Server to Azure SQL Database Managed Instance.

Az adatbázis biztonsági másolatának egy felügyelt Azure SQL Database-példányra történő visszaállításáról itt olvashat: [Adatbázis biztonsági másolatának visszaállítása egy felügyelt Azure SQL Database-példányon](sql-database-managed-instance-get-started-restore.md).
