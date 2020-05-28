---
title: TDE-tanúsítvány által felügyelt példány migrálása
description: Az adatbázis-titkosítási kulcsának átirányítása az Azure SQL felügyelt példányának transzparens adattitkosításával
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: eb8c794f4817c11d30112fbdf7d754081cc29859
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045787"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-managed-instance"></a>A TDE-vel védett adatbázis tanúsítványának migrálása felügyelt Azure SQL-példányra
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ha [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) által védett adatbázist telepít át az Azure SQL felügyelt példányára natív visszaállítási lehetőséggel, akkor a SQL Server példány megfelelő tanúsítványát át kell telepíteni az adatbázis-visszaállítás előtt. Ez a cikk végigvezeti a tanúsítvány manuális áttelepítésének folyamatán a felügyelt Azure SQL-példányon:

> [!div class="checklist"]
>
> * A tanúsítvány exportálása egy személyes információcsere (.pfx) fájlba
> * Tanúsítvány kibontása a fájlból base-64 sztringbe
> * Feltöltés PowerShell-parancsmag használatával

Ha a teljes körűen felügyelt szolgáltatást használja a TDE-védelemmel ellátott adatbázis és a hozzá tartozó tanúsítvány zökkenőmentes áttelepítéséhez, tekintse [meg a helyszíni adatbázis áttelepítése az Azure SQL felügyelt példányra Azure Database Migration Service használatával](../../dms/tutorial-sql-server-to-managed-instance.md)című témakört.

> [!IMPORTANT]
> A migrált tanúsítvány csak a TDE-vel védett adatbázis visszaállítására használható. A visszaállítást követően a rendszer a példányon beállított transzparens adattitkosítás típusától függően lecseréli az áttelepített tanúsítványt egy másik oltalmazóra, vagy a szolgáltatás által felügyelt tanúsítványra vagy az aszimmetrikus kulcsra a kulcstartóból.

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

* Telepített [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) parancssori eszköz egy helyszíni kiszolgálón vagy egy olyan számítógépen, amely hozzáfér a fájlként exportált tanúsítványhoz. A Pvk2Pfx eszköz az [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) része, amely egy önálló parancssori környezet.
* Telepített [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell), 5.0-s vagy újabb verzió.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Azure PowerShell modul [telepítve és frissítve](https://docs.microsoft.com/powershell/azure/install-az-ps).
* [Az az. SQL modul](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Az Azure SQL felügyelt példánya továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

Futtassa a következő parancsokat a PowerShellben a modul telepítéséhez/frissítéséhez:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

   ![a TDE-tanúsítványok listája](./media/tde-certificate-migrate/onprem-certificate-list.png)

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

   ![TDE-tanúsítvány biztonsági mentése](./media/tde-certificate-migrate/backup-onprem-certificate.png)

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

## <a name="upload-certificate-to-azure-sql-managed-instance-using-azure-powershell-cmdlet"></a>Tanúsítvány feltöltése a felügyelt Azure SQL-példányba Azure PowerShell-parancsmaggal

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Előkészítő lépések a PowerShellben:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Az előkészítési lépések elvégzése után futtassa a következő parancsokat a Base-64 kódolású tanúsítvány feltöltéséhez a cél felügyelt példányra:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Először be kell [állítania egy Azure Key Vaultt](/azure/key-vault/key-vault-manage-with-cli2) a *. pfx* fájllal.

1. Előkészítő lépések a PowerShellben:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Az előkészítési lépések elvégzése után futtassa a következő parancsokat a Base-64 kódolású tanúsítvány feltöltéséhez a cél felügyelt példányra:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

A tanúsítvány mostantól elérhető a megadott felügyelt példányon, és a megfelelő TDE védett adatbázis biztonsági másolata sikeresen visszaállítható.

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan migrálhatja az adatbázis titkosító kulcsának tanúsítványát transzparens adattitkosítással egy helyszíni vagy az IaaS SQL Server-példányról a felügyelt Azure SQL-példányra.

Lásd: [adatbázis biztonsági másolatának visszaállítása egy Azure SQL felügyelt példányra](restore-sample-database-quickstart.md) , amelyből megtudhatja, hogyan állíthatja vissza az adatbázis biztonsági másolatát egy felügyelt Azure SQL-példányra.
